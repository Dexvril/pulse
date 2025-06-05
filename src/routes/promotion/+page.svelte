<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';

  let promotions = [];
  let searchTerm = '';
  let statusFilter = '';
  let errorMessage = '';
  let showModal = false;
  let isEditMode = false;
  let currentPromo = {
    id: null,
    name: '',
    description: '',
    discount: 0,
    start_date: '',
    end_date: ''
  };

  onMount(async () => {
    await fetchPromotions();
  });

  async function fetchPromotions() {
    try {
      console.log('Fetching promotions...');
      const { data, error } = await supabase.from('promotions').select('*');
      if (error) throw new Error(`Gagal mengambil promo: ${error.message}`);
      promotions = data;
      console.log('Promotions fetched:', promotions);
    } catch (error) {
      console.error('Error fetching promotions:', error.message);
      errorMessage = `Gagal memuat data promo: ${error.message}`;
    }
  }

  async function addPromo() {
    try {
      const { error } = await supabase.from('promotions').insert({
        name: currentPromo.name,
        description: currentPromo.description,
        discount: parseInt(currentPromo.discount),
        start_date: currentPromo.start_date,
        end_date: currentPromo.end_date,
        status: true
      });
      if (error) throw new Error(`Gagal menambahkan promo: ${error.message}`);
      await fetchPromotions();
      closeModal();
    } catch (error) {
      console.error('Error adding promo:', error.message);
      errorMessage = `Gagal menambahkan promo: ${error.message}`;
    }
  }

  async function updatePromo() {
    try {
      const { error } = await supabase
        .from('promotions')
        .update({
          name: currentPromo.name,
          description: currentPromo.description,
          discount: parseInt(currentPromo.discount),
          start_date: currentPromo.start_date,
          end_date: currentPromo.end_date
        })
        .eq('id', currentPromo.id);
      if (error) throw new Error(`Gagal mengedit promo: ${error.message}`);
      await fetchPromotions();
      closeModal();
    } catch (error) {
      console.error('Error updating promo:', error.message);
      errorMessage = `Gagal mengedit promo: ${error.message}`;
    }
  }

  async function toggleStatus(promo) {
    try {
      const { error } = await supabase
        .from('promotions')
        .update({ status: !promo.status })
        .eq('id', promo.id);
      if (error) throw new Error(`Gagal mengubah status promo: ${error.message}`);
      await fetchPromotions();
    } catch (error) {
      console.error('Error toggling status:', error.message);
      errorMessage = `Gagal mengubah status promo: ${error.message}`;
    }
  }

  async function deletePromo(promoId) {
    if (!confirm('Apakah Anda yakin ingin menghapus promo ini?')) return;
    try {
      const { error } = await supabase.from('promotions').delete().eq('id', promoId);
      if (error) throw new Error(`Gagal menghapus promo: ${error.message}`);
      await fetchPromotions();
    } catch (error) {
      console.error('Error deleting promo:', error.message);
      errorMessage = `Gagal menghapus promo: ${error.message}`;
    }
  }

  function openAddModal() {
    isEditMode = false;
    currentPromo = { id: null, name: '', description: '', discount: 0, start_date: '', end_date: '' };
    showModal = true;
  }

  function openEditModal(promo) {
    isEditMode = true;
    currentPromo = {
      id: promo.id,
      name: promo.name,
      description: promo.description || '',
      discount: promo.discount,
      start_date: new Date(promo.start_date).toISOString().split('T')[0],
      end_date: new Date(promo.end_date).toISOString().split('T')[0]
    };
    showModal = true;
  }

  function closeModal() {
    showModal = false;
    currentPromo = { id: null, name: '', description: '', discount: 0, start_date: '', end_date: '' };
  }

  function savePromo() {
    if (isEditMode) {
      updatePromo();
    } else {
      addPromo();
    }
  }

  $: filteredPromotions = promotions.filter(promo =>
    (!statusFilter || promo.status.toString() === statusFilter) &&
    promo.name.toLowerCase().includes(searchTerm.toLowerCase())
  );
</script>

<div class="promotion-page">
  <h1>Promosi</h1>
  <p class="description">Daftar promosi untuk layanan | Total Promo: {promotions.length}</p>

  <div class="controls">
    <div class="filter-section">
      <div class="filter-group">
        <label for="status">Status:</label>
        <select id="status" bind:value={statusFilter}>
          <option value="">- Semua Status -</option>
          <option value="true">Aktif</option>
          <option value="false">Non-Aktif</option>
        </select>
      </div>
      <div class="filter-group">
        <input
          type="text"
          bind:value={searchTerm}
          placeholder="Cari Nama Promo"
          class="search-bar"
        />
      </div>
      <button on:click={openAddModal} class="add-btn">Tambah Promo Baru</button>
    </div>
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Nama Promo</th>
          <th>Deskripsi</th>
          <th>Diskon (%)</th>
          <th>Tanggal Mulai</th>
          <th>Tanggal Selesai</th>
          <th>Status</th>
          <th>Aksi</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredPromotions as promo, index}
          <tr>
            <td>{index + 1}</td>
            <td>{promo.name || 'N/A'}</td>
            <td>{promo.description || 'N/A'}</td>
            <td>{promo.discount}</td>
            <td>{new Date(promo.start_date).toLocaleDateString('id-ID')}</td>
            <td>{new Date(promo.end_date).toLocaleDateString('id-ID')}</td>
            <td>
              <input
                type="checkbox"
                checked={promo.status}
                on:change={() => toggleStatus(promo)}
              />
            </td>
            <td>
              <button on:click={() => openEditModal(promo)} class="edit-btn">Edit</button>
              <button on:click={() => deletePromo(promo.id)} class="delete-btn">Hapus</button>
            </td>
          </tr>
        {/each}
        {#if filteredPromotions.length === 0}
          <tr>
            <td colspan="8" class="no-data">Tidak ada promo ditemukan.</td>
          </tr>
        {/if}
      </tbody>
    </table>
  </div>

  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}

  {#if showModal}
    <div class="modal-overlay">
      <div class="modal">
        <h2>{isEditMode ? 'Edit Promo' : 'Tambah Promo Baru'}</h2>
        <div class="form-group">
          <label for="name">Nama Promo:</label>
          <input type="text" id="name" bind:value={currentPromo.name} placeholder="Masukkan nama promo" />
        </div>
        <div class="form-group">
          <label for="description">Deskripsi:</label>
          <textarea id="description" bind:value={currentPromo.description} placeholder="Masukkan deskripsi promo"></textarea>
        </div>
        <div class="form-group">
          <label for="discount">Diskon (%):</label>
          <input type="number" id="discount" bind:value={currentPromo.discount} placeholder="Masukkan diskon" />
        </div>
        <div class="form-group">
          <label for="start_date">Tanggal Mulai:</label>
          <input type="date" id="start_date" bind:value={currentPromo.start_date} />
        </div>
        <div class="form-group">
          <label for="end_date">Tanggal Selesai:</label>
          <input type="date" id="end_date" bind:value={currentPromo.end_date} />
        </div>
        <div class="modal-actions">
          <button on:click={closeModal} class="cancel-btn">Batal</button>
          <button on:click={savePromo} class="save-btn">Simpan</button>
        </div>
      </div>
    </div>
  {/if}
</div>

<style>
  .promotion-page {
    padding: 20px;
    background-color: #f4f6f9;
    min-height: calc(100vh - 60px);
    margin-left: 250px;
    box-sizing: border-box;
    width: calc(100% - 250px);
    transition: margin-left 0.3s ease;
  }

  h1 {
    font-size: 1.8rem;
    color: #2c3e50;
    margin-bottom: 10px;
    font-weight: 600;
  }

  .description {
    color: #7f8c8d;
    margin-bottom: 20px;
    font-size: 0.9rem;
  }

  .controls {
    margin-bottom: 20px;
  }

  .filter-section {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    align-items: flex-end;
  }

  .filter-group {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .filter-group label {
    font-size: 0.9rem;
    color: #34495e;
  }

  .filter-group select,
  .filter-group input[type="text"],
  .filter-group input[type="date"] {
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
    width: 200px;
  }

  .add-btn {
    padding: 8px 16px;
    background-color: #27ae60;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
    transition: background-color 0.3s ease;
  }

  .add-btn:hover {
    background-color: #219653;
  }

  .table-container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
    overflow-x: auto;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    min-width: 800px;
  }

  th, td {
    padding: 12px;
    border-bottom: 1px solid #eee;
    text-align: left;
  }

  th {
    background-color: #2c3e50;
    color: white;
    font-weight: 500;
  }

  td {
    color: #34495e;
  }

  .no-data {
    text-align: center;
    color: #7f8c8d;
    padding: 20px;
  }

  .edit-btn {
    padding: 6px 12px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.8rem;
    background-color: #f1c40f;
    color: white;
    margin-right: 5px;
  }

  .edit-btn:hover {
    background-color: #d4a017;
  }

  .delete-btn {
    padding: 6px 12px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.8rem;
    background-color: #e74c3c;
    color: white;
  }

  .delete-btn:hover {
    background-color: #c0392b;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
    font-size: 0.9rem;
  }

  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
  }

  .modal {
    background: white;
    padding: 20px;
    border-radius: 8px;
    width: 400px;
    max-width: 90%;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  }

  .modal h2 {
    font-size: 1.5rem;
    color: #2c3e50;
    margin-bottom: 20px;
  }

  .form-group {
    margin-bottom: 15px;
  }

  .form-group label {
    display: block;
    font-size: 0.9rem;
    color: #34495e;
    margin-bottom: 5px;
  }

  .form-group input,
  .form-group textarea {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
  }

  .form-group textarea {
    height: 80px;
    resize: vertical;
  }

  .modal-actions {
    display: flex;
    justify-content: flex-end;
    gap: 10px;
  }

  .cancel-btn {
    padding: 8px 16px;
    background-color: #95a5a6;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  .cancel-btn:hover {
    background-color: #7f8c8d;
  }

  .save-btn {
    padding: 8px 16px;
    background-color: #3498db;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  .save-btn:hover {
    background-color: #2980b9;
  }

  @media (max-width: 768px) {
    .promotion-page {
      margin-left: 0;
      width: 100%;
    }
    .filter-group select,
    .filter-group input[type="text"],
    .filter-group input[type="date"] {
      width: 100%;
    }
  }
</style>
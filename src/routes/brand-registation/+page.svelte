<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';

  let brands = [];
  let errorMessage = '';
  let newBrand = {
    name: '',
    logo_url: ''
  };

  onMount(async () => {
    await fetchBrands();
  });

  async function fetchBrands() {
    try {
      console.log('Fetching brands...');
      const { data, error } = await supabase.from('brands').select('*');
      if (error) throw new Error(`Gagal mengambil data merek: ${error.message}`);
      brands = data;
      console.log('Brands fetched:', brands);
    } catch (error) {
      console.error('Error fetching brands:', error.message);
      errorMessage = `Gagal memuat data merek: ${error.message}`;
    }
  }

  async function addBrand() {
    if (!newBrand.name) {
      errorMessage = 'Nama merek harus diisi.';
      return;
    }

    try {
      const { error } = await supabase.from('brands').insert({
        name: newBrand.name,
        logo_url: newBrand.logo_url
      });
      if (error) throw new Error(`Gagal menambahkan merek: ${error.message}`);
      await fetchBrands();
      newBrand = { name: '', logo_url: '' };
    } catch (error) {
      console.error('Error adding brand:', error.message);
      errorMessage = `Gagal menambahkan merek: ${error.message}`;
    }
  }

  async function deleteBrand(brandId) {
    if (!confirm('Apakah Anda yakin ingin menghapus merek ini?')) return;
    try {
      const { error } = await supabase.from('brands').delete().eq('id', brandId);
      if (error) throw new Error(`Gagal menghapus merek: ${error.message}`);
      await fetchBrands();
    } catch (error) {
      console.error('Error deleting brand:', error.message);
      errorMessage = `Gagal menghapus merek: ${error.message}`;
    }
  }
</script>

<div class="brand-registration-page">
  <h1>Pendaftaran Merek</h1>
  <p class="description">Tambah dan kelola merek untuk layanan | Total Merek: {brands.length}</p>

  <div class="form-container">
    <h2>Tambah Merek Baru</h2>
    <div class="form-group">
      <label for="name">Nama Merek:</label>
      <input type="text" id="name" bind:value={newBrand.name} placeholder="Masukkan nama merek" />
    </div>
    <div class="form-group">
      <label for="logo_url">URL Logo:</label>
      <input type="text" id="logo_url" bind:value={newBrand.logo_url} placeholder="Masukkan URL logo (opsional)" />
    </div>
    <button on:click={addBrand} class="add-btn">Tambah Merek</button>
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Nama Merek</th>
          <th>URL Logo</th>
          <th>Aksi</th>
        </tr>
      </thead>
      <tbody>
        {#each brands as brand, index}
          <tr>
            <td>{index + 1}</td>
            <td>{brand.name || 'N/A'}</td>
            <td>{brand.logo_url || 'N/A'}</td>
            <td>
              <button on:click={() => deleteBrand(brand.id)} class="delete-btn">Hapus</button>
            </td>
          </tr>
        {/each}
        {#if brands.length === 0}
          <tr>
            <td colspan="4" class="no-data">Tidak ada merek ditemukan.</td>
          </tr>
        {/if}
      </tbody>
    </table>
  </div>

  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
</div>

<style>
  .brand-registration-page {
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

  h2 {
    font-size: 1.5rem;
    color: #2c3e50;
    margin-bottom: 15px;
  }

  .description {
    color: #7f8c8d;
    margin-bottom: 20px;
    font-size: 0.9rem;
  }

  .form-container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
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
    min-width: 600px;
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

  @media (max-width: 768px) {
    .brand-registration-page {
      margin-left: 0;
      width: 100%;
    }
  }
</style>
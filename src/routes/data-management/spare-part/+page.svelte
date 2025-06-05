<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let spareParts = [];
  let brands = [];
  let searchTerm = '';
  let errorMessage = '';
  let showAddPopup = false;
  let newSparePart = { name: '', part_number: '', brand_id: null, stock: 0, price: 0 };

  onMount(async () => {
    try {
      // Ambil data brands untuk mapping
      const { data: brandData, error: brandError } = await supabase.from('brands').select('id, name');
      if (brandError) throw brandError;
      brands = brandData;

      // Ambil data spare parts
      const { data, error } = await supabase.from('spare_parts').select('*');
      if (error) throw error;
      spareParts = data.map(part => {
        const brand = brands.find(b => b.id === part.brand_id);
        return { ...part, brand_name: brand ? brand.name : 'N/A' };
      });
    } catch (error) {
      errorMessage = 'Failed to load spare parts.';
    }
  });

  $: filteredSpareParts = spareParts.filter(part =>
    part.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    part.part_number.toLowerCase().includes(searchTerm.toLowerCase())
  );

  async function addSparePart() {
    if (!newSparePart.name.trim() || !newSparePart.part_number.trim() || !newSparePart.brand_id || newSparePart.stock < 0 || newSparePart.price <= 0) {
      errorMessage = 'Please fill in all required fields with valid values.';
      return;
    }
    try {
      const { error } = await supabase.from('spare_parts').insert({
        name: newSparePart.name.trim(),
        part_number: newSparePart.part_number.trim(),
        brand_id: newSparePart.brand_id,
        stock: newSparePart.stock,
        price: newSparePart.price,
        created_at: new Date().toISOString(),
        updated_at: new Date().toISOString()
      });
      if (error) throw error;
      newSparePart = { name: '', part_number: '', brand_id: null, stock: 0, price: 0 };
      showAddPopup = false;
      const { data } = await supabase.from('spare_parts').select('*');
      spareParts = data.map(part => {
        const brand = brands.find(b => b.id === part.brand_id);
        return { ...part, brand_name: brand ? brand.name : 'N/A' };
      });
      errorMessage = '';
    } catch (error) {
      errorMessage = 'Failed to add spare part.';
    }
  }

  function viewSparePartDetail(part) {
    goto(`/data-management/spare-part-detail?id=${part.id}`);
  }
</script>

<div class="spare-part-list">
  <h1>Spare Part</h1>
  <p class="description">Spare Part List | Total Spare Part: {spareParts.length}</p>

  <div class="controls">
    <div class="search-container">
      <input
        type="text"
        bind:value={searchTerm}
        placeholder="Search Spare Part"
        class="search-bar"
      />
    </div>
    <button on:click={() => (showAddPopup = true)} class="add-btn">+ Add Spare Part</button>
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Part Name</th>
          <th>Part Number</th>
          <th>Brand</th>
          <th>Stock</th>
          <th>Price (IDR)</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredSpareParts as part, index}
          <tr>
            <td>{index + 1}</td>
            <td>{part.name}</td>
            <td>{part.part_number}</td>
            <td>{part.brand_name}</td>
            <td>{part.stock}</td>
            <td>{part.price.toLocaleString('id-ID')}</td>
            <td>
              <button on:click={() => viewSparePartDetail(part)} class="detail-btn">Detail</button>
            </td>
          </tr>
        {/each}
        {#if filteredSpareParts.length === 0}
          <tr>
            <td colspan="7" class="no-data">No spare parts found.</td>
          </tr>
        {/if}
      </tbody>
    </table>
  </div>

  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}

  {#if showAddPopup}
    <div class="popup-overlay" on:click|self={() => (showAddPopup = false)}>
      <div class="popup">
        <h2>Add Spare Part</h2>
        <button class="close-btn" on:click={() => (showAddPopup = false)}>×</button>
        <p>Please fill in the form to add data</p>
        <div class="form-group">
          <label for="partName">Part Name:*</label>
          <input
            type="text"
            id="partName"
            bind:value={newSparePart.name}
            placeholder="Part Name"
            required
          />
        </div>
        <div class="form-group">
          <label for="partNumber">Part Number:*</label>
          <input
            type="text"
            id="partNumber"
            bind:value={newSparePart.part_number}
            placeholder="Part Number"
            required
          />
        </div>
        <div class="form-group">
          <label for="brand">Brand:*</label>
          <select id="brand" bind:value={newSparePart.brand_id} required>
            <option value="">Select Brand</option>
            {#each brands as brand}
              <option value={brand.id}>{brand.name}</option>
            {/each}
          </select>
        </div>
        <div class="form-group">
          <label for="stock">Stock:*</label>
          <input
            type="number"
            id="stock"
            bind:value={newSparePart.stock}
            placeholder="Stock Quantity"
            required
          />
        </div>
        <div class="form-group">
          <label for="price">Price (IDR):*</label>
          <input
            type="number"
            id="price"
            bind:value={newSparePart.price}
            placeholder="Price in IDR"
            required
          />
        </div>
        <button on:click={addSparePart} class="add-spare-part-btn">Add Spare Part</button>
      </div>
    </div>
  {/if}
</div>

<style>
  .spare-part-list {
    padding: 20px;
    background-color: #f4f6f9;
    min-height: calc(100vh - 60px);
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
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  .search-container {
    flex: 1;
    margin-right: 10px;
  }

  .search-bar {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
  }

  .add-btn {
    padding: 10px 20px;
    background-color: #2c3e50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  .add-btn:hover {
    background-color: #34495e;
  }

  .table-container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
  }

  table {
    width: 100%;
    border-collapse: collapse;
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

  .detail-btn {
    padding: 6px 12px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.8rem;
    background-color: #3498db;
    color: white;
  }

  .detail-btn:hover {
    background-color: #2980b9;
  }

  .popup-overlay {
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

  .popup {
    background: white;
    padding: 20px;
    border-radius: 8px;
    width: 300px;
    position: relative;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .popup h2 {
    font-size: 1.2rem;
    color: #2c3e50;
    margin-bottom: 10px;
  }

  .close-btn {
    position: absolute;
    top: 10px;
    right: 10px;
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    color: #7f8c8d;
  }

  .form-group {
    margin-bottom: 15px;
  }

  .form-group label {
    display: block;
    margin-bottom: 5px;
    color: #2c3e50;
    font-weight: 500;
  }

  .form-group input, .form-group select {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
  }

  .add-spare-part-btn {
    padding: 10px 20px;
    background-color: #2c3e50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    width: 100%;
  }

  .add-spare-part-btn:hover {
    background-color: #34495e;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }
</style>
<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  let brands = [];
  let searchTerm = '';
  let errorMessage = '';
  let showAddPopup = false;
  let newBrandName = '';
  let newBrandLogo = '';

  onMount(async () => {
    try {
      const { data, error } = await supabase.from('brands').select('*');
      if (error) throw error;
      brands = data;
    } catch (error) {
      errorMessage = 'Failed to load brands.';
    }
  });

  $: filteredBrands = brands.filter(brand =>
    brand.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  async function addBrand() {
    if (!newBrandName.trim()) {
      errorMessage = 'Brand name is required.';
      return;
    }
    try {
      const { error } = await supabase.from('brands').insert({
        name: newBrandName.trim(),
        logo_url: newBrandLogo || null,
        created_at: new Date().toISOString(),
        updated_at: new Date().toISOString()
      });
      if (error) throw error;
      newBrandName = '';
      newBrandLogo = '';
      showAddPopup = false;
      // Refresh data
      const { data } = await supabase.from('brands').select('*');
      brands = data;
      errorMessage = '';
    } catch (error) {
      errorMessage = 'Failed to add brand.';
    }
  }
</script>

<div class="available-brand">
  <h1>Available Brand</h1>
  <p class="description">Brand List | Total Brand: {brands.length}</p>

  <div class="controls">
    <div class="search-container">
      <input
        type="text"
        bind:value={searchTerm}
        placeholder="Search Brand"
        class="search-bar"
      />
    </div>
    <button on:click={() => (showAddPopup = true)} class="add-btn">+ Add Brand</button>
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Brand Name</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredBrands as brand, index}
          <tr>
            <td>{index + 1}</td>
            <td>{brand.name}</td>
            <td>
              <button class="action-btn">✎</button>
            </td>
          </tr>
        {/each}
        {#if filteredBrands.length === 0}
          <tr>
            <td colspan="3" class="no-data">No brands found.</td>
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
        <h2>Add Available Brand</h2>
        <button class="close-btn" on:click={() => (showAddPopup = false)}>×</button>
        <p>Please fill in the form to add/edit data</p>
        <div class="form-group">
          <label for="brandLogo">Brand Logo:</label>
          <input
            type="text"
            id="brandLogo"
            bind:value={newBrandLogo}
            placeholder="Click to the area to upload (JPG/PNG format)"
          />
        </div>
        <div class="form-group">
          <label for="brandName">Brand Name:*</label>
          <input
            type="text"
            id="brandName"
            bind:value={newBrandName}
            placeholder="Brand Name"
            required
          />
        </div>
        <button on:click={addBrand} class="add-brand-btn">Add Brand</button>
      </div>
    </div>
  {/if}
</div>

<style>
  .available-brand {
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

  .action-btn {
    background: none;
    border: none;
    font-size: 1rem;
    cursor: pointer;
    color: #f1c40f;
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

  .form-group input {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
  }

  .add-brand-btn {
    padding: 10px 20px;
    background-color: #2c3e50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    width: 100%;
  }

  .add-brand-btn:hover {
    background-color: #34495e;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }
</style>
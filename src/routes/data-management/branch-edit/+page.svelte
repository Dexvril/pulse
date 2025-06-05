<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let branch = {
    id: null,
    name: '',
    address: '',
    province: '',
    region: '',
    whatsapp: '',
    email: ''
  };
  let errorMessage = '';
  let successMessage = '';

  onMount(async () => {
    const branchId = $page.url.searchParams.get('id');
    if (!branchId) {
      errorMessage = 'Branch ID not provided.';
      return;
    }

    try {
      const { data, error } = await supabase
        .from('branches')
        .select('*')
        .eq('id', branchId)
        .single();
      if (error) throw error;
      branch = { ...data };
    } catch (error) {
      errorMessage = 'Failed to load branch details.';
    }
  });

  async function saveBranch() {
    try {
      const { error } = await supabase
        .from('branches')
        .update({
          name: branch.name,
          address: branch.address,
          province: branch.province,
          region: branch.region,
          whatsapp: branch.whatsapp,
          email: branch.email,
          updated_at: new Date().toISOString()
        })
        .eq('id', branch.id);
      if (error) throw error;
      successMessage = 'Branch updated successfully!';
      setTimeout(() => {
        successMessage = '';
        goto('/data-management/workshop-information');
      }, 2000);
    } catch (error) {
      errorMessage = 'Failed to update branch.';
    }
  }

  function goBack() {
    goto('/data-management/workshop-information');
  }
</script>

<div class="branch-edit">
  <h1>Branch Profile Edit</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
  {#if successMessage}
    <p class="success">{successMessage}</p>
  {/if}
  {#if branch.id}
    <div class="edit-content">
      <div class="branch-info">
        <img src="/branch-logo.png" alt="Branch Logo" class="branch-logo" />
        <div class="info-text">
          <div class="form-group">
            <label for="name">Name:</label>
            <input id="name" bind:value={branch.name} required />
          </div>
          <div class="form-group">
            <label for="address">Address:</label>
            <input id="address" bind:value={branch.address} required />
          </div>
          <div class="form-group">
            <label for="province">Province:</label>
            <select id="province" bind:value={branch.province}>
              <option value="">Select Province</option>
              <option value="Bali">Bali</option>
              <option value="Jawa Barat">Jawa Barat</option>
              <option value="Jawa Timur">Jawa Timur</option>
              <option value="Jawa Tengah">Jawa Tengah</option>
            </select>
          </div>
          <div class="form-group">
            <label for="region">Region:</label>
            <select id="region" bind:value={branch.region}>
              <option value="">Select Region</option>
              <option value="Kota Denpasar">Kota Denpasar</option>
              <option value="Kota Bogor">Kota Bogor</option>
              <option value="Kota Surabaya">Kota Surabaya</option>
              <option value="Kota Semarang">Kota Semarang</option>
            </select>
          </div>
          <div class="form-group">
            <label for="whatsapp">WhatsApp Number:</label>
            <input id="whatsapp" bind:value={branch.whatsapp} />
          </div>
          <div class="form-group">
            <label for="email">Email:</label>
            <input id="email" bind:value={branch.email} />
          </div>
        </div>
      </div>
      <button on:click={saveBranch} class="save-btn">Save Changes</button>
    </div>
  {:else}
    <p class="loading">Loading branch data...</p>
  {/if}
</div>

<style>
  .branch-edit {
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

  .back-btn {
    background-color: #e74c3c;
    color: white;
    border: none;
    padding: 8px 16px;
    border-radius: 4px;
    cursor: pointer;
    margin-bottom: 20px;
  }

  .back-btn:hover {
    background-color: #c0392b;
  }

  .edit-content {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
  }

  .branch-info {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
  }

  .branch-logo {
    width: 80px;
    height: auto;
    margin-right: 20px;
  }

  .info-text .form-group {
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

  .save-btn {
    padding: 10px 20px;
    background-color: #2c3e50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    width: 100%;
    margin-top: 20px;
  }

  .save-btn:hover {
    background-color: #34495e;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }

  .success {
    color: #27ae60;
    margin-top: 10px;
    text-align: center;
  }

  .loading {
    text-align: center;
    color: #7f8c8d;
    margin-top: 20px;
  }
</style>
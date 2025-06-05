<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let brand = {
    id: null,
    name: '',
    about: '',
    to_email: '',
    address: '',
    province: '',
    region: '',
    contact: '',
    email: ''
  };
  let errorMessage = '';
  let successMessage = '';

  onMount(async () => {
    const brandId = $page.url.searchParams.get('id');
    if (!brandId) {
      errorMessage = 'Brand ID not provided.';
      return;
    }

    try {
      const { data, error } = await supabase
        .from('brands')
        .select('*')
        .eq('id', brandId)
        .single();
      if (error) throw error;
      brand = { ...data }; // Load existing data into the form
    } catch (error) {
      errorMessage = 'Failed to load brand details.';
    }
  });

  async function saveBrand() {
    try {
      const { error } = await supabase
        .from('brands')
        .update({
          name: brand.name,
          about: brand.about,
          to_email: brand.to_email,
          address: brand.address,
          province: brand.province,
          region: brand.region,
          contact: brand.contact,
          email: brand.email,
          updated_at: new Date().toISOString()
        })
        .eq('id', brand.id);
      if (error) throw error;
      successMessage = 'Brand updated successfully!';
      setTimeout(() => {
        successMessage = '';
        goto('/data-management/workshop-information');
      }, 2000); // Redirect after 2 seconds
    } catch (error) {
      errorMessage = 'Failed to update brand.';
    }
  }

  function goBack() {
    goto('/data-management/workshop-information');
  }
</script>

<div class="brand-edit">
  <h1>Brand Profile Edit</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
  {#if successMessage}
    <p class="success">{successMessage}</p>
  {/if}
  {#if brand.id}
    <div class="edit-content">
      <div class="brand-info">
        <img src="/brand-logo.png" alt="Brand Logo" class="brand-logo" />
        <div class="info-text">
          <div class="form-group">
            <label for="name">Name:</label>
            <input id="name" bind:value={brand.name} required />
          </div>
          <div class="form-group">
            <label for="about">About:</label>
            <input id="about" bind:value={brand.about} />
          </div>
          <div class="form-group">
            <label for="to_email">To Email:</label>
            <input id="to_email" bind:value={brand.to_email} />
          </div>
          <div class="form-group">
            <label for="address">Address:</label>
            <input id="address" bind:value={brand.address} required />
          </div>
          <div class="form-group">
            <label for="province">Province:</label>
            <select id="province" bind:value={brand.province}>
              <option value="">Select Province</option>
              <option value="Jawa Barat">Jawa Barat</option>
              <option value="Jawa Timur">Jawa Timur</option>
              <option value="Jawa Tengah">Jawa Tengah</option>
            </select>
          </div>
          <div class="form-group">
            <label for="region">Region:</label>
            <select id="region" bind:value={brand.region}>
              <option value="">Select Region</option>
              <option value="Kota Bogor">Kota Bogor</option>
              <option value="Kota Surabaya">Kota Surabaya</option>
              <option value="Kota Semarang">Kota Semarang</option>
            </select>
          </div>
          <div class="form-group">
            <label for="contact">Contact:</label>
            <input id="contact" bind:value={brand.contact} />
          </div>
          <div class="form-group">
            <label for="email">Email:</label>
            <input id="email" bind:value={brand.email} />
          </div>
        </div>
      </div>
      <button on:click={saveBrand} class="save-btn">Save Changes</button>
    </div>
  {:else}
    <p class="loading">Loading brand data...</p>
  {/if}
</div>

<style>
  .brand-edit {
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

  .brand-info {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
  }

  .brand-logo {
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
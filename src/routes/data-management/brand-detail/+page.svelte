<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let brand = null;
  let errorMessage = '';

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
      brand = data;
    } catch (error) {
      errorMessage = 'Failed to load brand details.';
    }
  });

  function goBack() {
    goto('/data-management/workshop-information');
  }
</script>

<div class="brand-detail">
  <h1>Brand Profile Detail</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if brand}
    <div class="detail-content">
      <div class="brand-info">
        <img src="/brand-logo.png" alt="Brand Logo" class="brand-logo" />
        <div class="info-text">
          <p><strong>Name:</strong> {brand.name || 'N/A'}</p>
          <p><strong>About:</strong> {brand.about || 'N/A'}</p>
          <p><strong>To Email:</strong> {brand.to_email || 'N/A'}</p>
          <p><strong>Address:</strong> {brand.address || 'N/A'}</p>
          <p><strong>Province:</strong> {brand.province || 'N/A'}</p>
          <p><strong>Region:</strong> {brand.region || 'N/A'}</p>
          <p><strong>Contact:</strong> {brand.contact || 'N/A'}</p>
          <p><strong>Email:</strong> {brand.email || 'N/A'}</p>
        </div>
      </div>
      <div class="service-section">
        <h3>Service List</h3>
        <ul>
          {#each brand.services || [] as service}
            <li>{service}</li>
          {/each}
        </ul>
      </div>
      <div class="spare-section">
        <h3>Spare List</h3>
        <ul>
          {#each brand.spares || [] as spare}
            <li>{spare}</li>
          {/each}
        </ul>
      </div>
    </div>
  {/if}
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
</div>

<style>
  .brand-detail {
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

  .detail-content {
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

  .info-text p {
    margin: 5px 0;
    color: #34495e;
  }

  .service-section, .spare-section {
    margin-top: 20px;
  }

  h3 {
    font-size: 1.1rem;
    color: #2c3e50;
    margin-bottom: 10px;
  }

  ul {
    list-style: none;
    padding: 0;
  }

  ul li {
    padding: 5px 0;
    color: #34495e;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }
</style>
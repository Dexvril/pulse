<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let sparePart = null;
  let brandName = 'N/A';
  let errorMessage = '';

  onMount(async () => {
    const partId = $page.url.searchParams.get('id');
    if (!partId) {
      errorMessage = 'Spare Part ID not provided.';
      return;
    }

    try {
      const { data, error } = await supabase
        .from('spare_parts')
        .select('*')
        .eq('id', partId)
        .single();
      if (error) throw error;
      sparePart = data;

      if (sparePart.brand_id) {
        const { data: brandData, error: brandError } = await supabase
          .from('brands')
          .select('name')
          .eq('id', sparePart.brand_id)
          .single();
        if (brandError) throw brandError;
        brandName = brandData.name;
      }
    } catch (error) {
      errorMessage = 'Failed to load spare part details.';
    }
  });

  function goBack() {
    goto('/data-management/spare-part');
  }
</script>

<div class="spare-part-detail">
  <h1>Detail Spare Part</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if sparePart}
    <div class="detail-content">
      <div class="spare-part-info">
        <h2>{sparePart.name}</h2>
        <p><strong>Spare Part Information</strong></p>
        <p><strong>Name:</strong> {sparePart.name || 'N/A'}</p>
        <p><strong>Part Number:</strong> {sparePart.part_number || 'N/A'}</p>
        <p><strong>Brand:</strong> {brandName}</p>
        <p><strong>Stock:</strong> {sparePart.stock}</p>
        <p><strong>Price:</strong> Rp {sparePart.price.toLocaleString('id-ID')}</p>
      </div>
    </div>
  {/if}
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
</div>

<style>
  .spare-part-detail {
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
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .spare-part-info {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
  }

  .spare-part-info h2 {
    font-size: 1.5rem;
    margin-bottom: 10px;
  }

  .spare-part-info p {
    margin: 5px 0;
    color: #34495e;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }
</style>
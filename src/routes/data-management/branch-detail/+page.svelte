<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let branch = null;
  let errorMessage = '';

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
      branch = data;
    } catch (error) {
      errorMessage = 'Failed to load branch details.';
    }
  });

  function goBack() {
    goto('/data-management/workshop-information');
  }
</script>

<div class="branch-detail">
  <h1>Branch Profile Detail</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if branch}
    <div class="detail-content">
      <div class="branch-info">
        <img src="/branch-logo.png" alt="Branch Logo" class="branch-logo" />
        <div class="info-text">
          <p><strong>Name:</strong> {branch.name || 'N/A'}</p>
          <p><strong>Address:</strong> {branch.address || 'N/A'}</p>
          <p><strong>Province:</strong> {branch.province || 'N/A'}</p>
          <p><strong>Region:</strong> {branch.region || 'N/A'}</p>
          <p><strong>WhatsApp Number:</strong> {branch.whatsapp || 'N/A'}</p>
          <p><strong>Email:</strong> {branch.email || 'N/A'}</p>
        </div>
      </div>
    </div>
  {/if}
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
</div>

<style>
  .branch-detail {
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

  .info-text p {
    margin: 5px 0;
    color: #34495e;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }
</style>
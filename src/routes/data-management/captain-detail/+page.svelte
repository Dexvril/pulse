<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let captain = null;
  let branchName = 'N/A';
  let errorMessage = '';

  onMount(async () => {
    const captainId = $page.url.searchParams.get('id');
    if (!captainId) {
      errorMessage = 'Captain ID not provided.';
      return;
    }

    try {
      const { data: captainData, error: captainError } = await supabase
        .from('captains')
        .select('*')
        .eq('id', captainId)
        .single();
      if (captainError) throw captainError;
      captain = captainData;

      if (captain.branch_id) {
        const { data: branchData, error: branchError } = await supabase
          .from('branches')
          .select('name')
          .eq('id', captain.branch_id)
          .single();
        if (branchError) throw branchError;
        branchName = branchData.name;
      }
    } catch (error) {
      errorMessage = 'Failed to load captain details.';
    }
  });

  function goBack() {
    goto('/data-management/captain');
  }

  function editCaptain() {
    if (captain) {
      goto(`/data-management/captain-edit?id=${captain.id}`);
    }
  }
</script>

<div class="captain-detail">
  <h1>Detail Captain</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if captain}
    <div class="detail-content">
      <div class="captain-info">
        <img src="/captain-avatar.png" alt="Captain Avatar" class="captain-avatar" />
        <div class="info-text">
          <h2>{captain.name}</h2>
          <p><strong>Personal Information</strong></p>
          <p><strong>Name:</strong> {captain.name || 'N/A'}</p>
          <p><strong>Captain ID:</strong> {captain.captain_id || 'N/A'}</p>
          <p><strong>Branch:</strong> {branchName}</p>
          <p><strong>Contact:</strong> {captain.contact || 'N/A'}</p>
          <p><strong>Email:</strong> {captain.email || 'N/A'}</p>
          <p><strong>Status:</strong> {captain.status ? 'Active' : 'Inactive'}</p>
        </div>
      </div>
      <button on:click={editCaptain} class="edit-btn">Edit</button>
    </div>
  {/if}
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
</div>

<style>
  .captain-detail {
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

  .captain-info {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
  }

  .captain-avatar {
    width: 150px;
    height: 150px;
    border-radius: 50%;
    margin-right: 20px;
  }

  .info-text {
    color: #34495e;
  }

  .info-text h2 {
    font-size: 1.5rem;
    margin-bottom: 10px;
  }

  .info-text p {
    margin: 5px 0;
  }

  .edit-btn {
    padding: 10px 20px;
    background-color: #e67e22;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    width: 100px;
    align-self: flex-end;
  }

  .edit-btn:hover {
    background-color: #d35400;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }
</style>
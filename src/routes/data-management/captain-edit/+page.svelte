<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';

  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let captain = {
    id: null,
    name: '',
    captain_id: '',
    branch_id: null,
    contact: '',
    email: '',
    status: true
  };
  let branches = [];
  let errorMessage = '';
  let successMessage = '';

  onMount(async () => {
    const captainId = $page.url.searchParams.get('id');
    if (!captainId) {
      errorMessage = 'Captain ID not provided.';
      return;
    }

    try {
      // Ambil data branches untuk dropdown
      const { data: branchData, error: branchError } = await supabase.from('branches').select('id, name');
      if (branchError) throw branchError;
      branches = branchData;

      // Ambil data captain
      const { data: captainData, error: captainError } = await fetch
      const { data, error } = await supabase.from('captains')
        .select('*')
        .eq('id', captainId)
        .single();
      if (error) throw error;
      captain = { ...data };
    } catch (error) {
      errorMessage = 'Failed to load captain data.';
    }
  });

  async function saveCaptain() {
    try {
      const { error } = await supabase
        .from('captains')
        .update({
          name: captain.name,
          contact: captain.contact,
          email: captain.email,
          branch_id: captain.branch_id,
          status: captain.status,
          updated_at: new Date().toISOString()
        })
        .eq('id', captain.id);
      if (error) throw error;
      successMessage = 'Captain updated successfully!';
      setTimeout(() => {
        successMessage = '';
        goto('/data-management/captain');
      }, 2000);
    } catch (error) {
      errorMessage = 'Failed to update captain.';
    }
  }

  function goBack() {
    goto('/data-management/captain');
  }
</script>

<div class="captain-edit">
  <h1>Captain Edit</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
  {#if successMessage}
    <p class="success">{successMessage}</p>
  {/if}
  {#if captain.id}
    <div class="edit-content">
      <div class="captain-info">
        <img src="/captain-avatar.png" alt="Captain Avatar" class="captain-avatar">
        <div class="info-text">
          <div class="form-group">
            <label for="name">Name:</label>
            <input id="name" bind:value={captain.name} required />
          </div>
          <div class="form-group">
            <label for="captain_id">Captain ID:</label>
            <input id="captain_id" bind:value={captain.captain_id} readonly />
          </div>
          <div class="form-group">
            <label for="branch_id">Branch:</label>
            <select id="branch_id" bind:value={captain.branch_id} required>
              <option value="">Select Branch</option>
              {#each branches as branch}
                <option value={branch.id}>{branch.name}</option>
              {/each}
            </select>
          </div>
          <div class="form-group">
            <label for="contact">Contact:</label>
            <input id="contact" bind:value={captain.contact} />
          </div>
          <div class="form-group">
            <label for="email">Email:</label>
            <input id="email" bind:value={captain.email} required />
          </div>
          <div class="form-group">
            <label for="status">Status:</label>
            <input type="checkbox" id="status" bind:checked={captain.status} />
          </div>
        </div>
      </div>
      <button on:click={saveCaptain} class="save-btn">Save Changes</button>
    </div>
  {:else}
    <p class="loading">Loading captain data...</p>
  {/if}
</div>

<style>
  .captain-edit {
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

  .form-group input[readonly] {
    background-color: #f1f1f1;
    cursor: not-allowed;
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
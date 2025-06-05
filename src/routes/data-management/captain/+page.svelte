<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let captains = [];
  let branches = [];
  let searchTerm = '';
  let errorMessage = '';

  onMount(async () => {
    try {
      // Ambil data branches untuk mapping
      const { data: branchData, error: branchError } = await supabase.from('branches').select('id, name');
      if (branchError) throw branchError;
      branches = branchData;

      // Ambil data captains
      const { data: captainData, error: captainError } = await supabase.from('captains').select('*');
      if (captainError) throw captainError;
      captains = captainData.map(captain => {
        const branch = branches.find(b => b.id === captain.branch_id);
        return { ...captain, branch_name: branch ? branch.name : 'N/A' };
      });
    } catch (error) {
      errorMessage = 'Failed to load captains.';
    }
  });

  $: filteredCaptains = captains.filter(captain =>
    captain.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    captain.captain_id.toLowerCase().includes(searchTerm.toLowerCase())
  );

  function viewCaptainDetail(captain) {
    goto(`/data-management/captain-detail?id=${captain.id}`);
  }

  function editCaptain(captain) {
    goto(`/data-management/captain-edit?id=${captain.id}`);
  }
</script>

<div class="captain-list">
  <h1>Captain</h1>
  <p class="description">Total Captain: {captains.length}</p>

  <div class="search-container">
    <input
      type="text"
      bind:value={searchTerm}
      placeholder="Search Captain"
      class="search-bar"
    />
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Name</th>
          <th>Captain ID</th>
          <th>Branch</th>
          <th>Contact</th>
          <th>Email</th>
          <th>Action</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredCaptains as captain, index}
          <tr>
            <td>{index + 1}</td>
            <td>{captain.name}</td>
            <td>{captain.captain_id}</td>
            <td>{captain.branch_name}</td>
            <td>{captain.contact || 'N/A'}</td>
            <td>{captain.email}</td>
            <td>
              <button on:click={() => viewCaptainDetail(captain)} class="detail-btn">Detail</button>
              <button on:click={() => editCaptain(captain)} class="edit-btn">Edit</button>
            </td>
            <td>
              <input type="checkbox" checked={captain.status} disabled />
            </td>
          </tr>
        {/each}
        {#if filteredCaptains.length === 0}
          <tr>
            <td colspan="8" class="no-data">No captains found.</td>
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
  .captain-list {
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

  .search-container {
    margin-bottom: 20px;
  }

  .search-bar {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
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

  .detail-btn, .edit-btn {
    padding: 6px 12px;
    margin-right: 5px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.8rem;
  }

  .detail-btn {
    background-color: #3498db;
    color: white;
  }

  .detail-btn:hover {
    background-color: #2980b9;
  }

  .edit-btn {
    background-color: #e67e22;
    color: white;
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
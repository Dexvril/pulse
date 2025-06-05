<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let members = [];
  let searchTerm = '';
  let errorMessage = '';

  onMount(async () => {
    try {
      const { data, error } = await supabase.from('members').select('*');
      if (error) throw error;
      members = data;
    } catch (error) {
      errorMessage = 'Failed to load members.';
    }
  });

  $: filteredMembers = members.filter(member =>
    member.username.toLowerCase().includes(searchTerm.toLowerCase()) ||
    member.member_id.toLowerCase().includes(searchTerm.toLowerCase())
  );

  function viewMemberDetail(member) {
    goto(`/data-management/member-detail?id=${member.id}`);
  }

  function editMember(member) {
    goto(`/data-management/member-edit?id=${member.id}`);
  }
</script>

<div class="member-list">
  <h1>Member</h1>
  <p class="description">Total Member: {members.length}</p>

  <div class="search-container">
    <input
      type="text"
      bind:value={searchTerm}
      placeholder="Search User"
      class="search-bar"
    />
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>User Name</th>
          <th>Member ID</th>
          <th>Number of Cars</th>
          <th>Contact</th>
          <th>Email</th>
          <th>Action</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredMembers as member, index}
          <tr>
            <td>{index + 1}</td>
            <td>{member.username}</td>
            <td>{member.member_id}</td>
            <td>{member.number_of_cars}</td>
            <td>{member.whatsapp || 'N/A'}</td>
            <td>{member.email}</td>
            <td>
              <button on:click={() => viewMemberDetail(member)} class="detail-btn">Detail</button>
              <button on:click={() => editMember(member)} class="edit-btn">Edit</button>
            </td>
            <td>
              <input type="checkbox" checked={member.status} disabled />
            </td>
          </tr>
        {/each}
        {#if filteredMembers.length === 0}
          <tr>
            <td colspan="8" class="no-data">No members found.</td>
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
  .member-list {
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
<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let member = null;
  let errorMessage = '';

  onMount(async () => {
    const memberId = $page.url.searchParams.get('id');
    if (!memberId) {
      errorMessage = 'Member ID not provided.';
      return;
    }

    try {
      const { data, error } = await supabase
        .from('members')
        .select('*')
        .eq('id', memberId)
        .single();
      if (error) throw error;
      member = data;
    } catch (error) {
      errorMessage = 'Failed to load member details.';
    }
  });

  function goBack() {
    goto('/data-management/member');
  }

  function editMember() {
    if (member) {
      goto(`/data-management/member-edit?id=${member.id}`);
    }
  }
</script>

<div class="member-detail">
  <h1>Detail Member</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if member}
    <div class="detail-content">
      <div class="member-info">
        <img src="/member-avatar.png" alt="Member Avatar" class="member-avatar" />
        <div class="info-text">
          <h2>{member.username}</h2>
          <p><strong>Personal Information</strong></p>
          <p><strong>Name:</strong> {member.username || 'N/A'}</p>
          <p><strong>Member ID:</strong> {member.member_id || 'N/A'}</p>
          <p><strong>Email:</strong> {member.email || 'N/A'}</p>
          <p><strong>WhatsApp Number:</strong> {member.whatsapp || 'N/A'}</p>
          <p><strong>Point:</strong> {member.point || 0}</p>
          <p><strong>List Car</strong></p>
          <p><strong>Number of Cars:</strong> {member.number_of_cars || 0}</p>
          <p><strong>Address:</strong> {member.address || 'N/A'}</p>
        </div>
      </div>
      <button on:click={editMember} class="edit-btn">Edit</button>
    </div>
  {/if}
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
</div>

<style>
  .member-detail {
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

  .member-info {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
  }

  .member-avatar {
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
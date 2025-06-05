<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let member = {
    id: null,
    username: '',
    member_id: '',
    email: '',
    whatsapp: '',
    point: 0,
    number_of_cars: 0,
    address: '',
    province: '',
    region: '',
    status: true
  };
  let errorMessage = '';
  let successMessage = '';

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
      member = { ...data };
    } catch (error) {
      errorMessage = 'Failed to load member details.';
    }
  });

  async function saveMember() {
    try {
      const { error } = await supabase
        .from('members')
        .update({
          username: member.username,
          email: member.email,
          whatsapp: member.whatsapp,
          point: member.point,
          number_of_cars: member.number_of_cars,
          address: member.address,
          province: member.province,
          region: member.region,
          status: member.status,
          updated_at: new Date().toISOString()
        })
        .eq('id', member.id);
      if (error) throw error;
      successMessage = 'Member updated successfully!';
      setTimeout(() => {
        successMessage = '';
        goto('/data-management/member');
      }, 2000);
    } catch (error) {
      errorMessage = 'Failed to update member.';
    }
  }

  function goBack() {
    goto('/data-management/member');
  }
</script>

<div class="member-edit">
  <h1>Member Edit</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
  {#if successMessage}
    <p class="success">{successMessage}</p>
  {/if}
  {#if member.id}
    <div class="edit-content">
      <div class="member-info">
        <img src="/member-avatar.png" alt="Member Avatar" class="member-avatar" />
        <div class="info-text">
          <div class="form-group">
            <label for="username">Name:</label>
            <input id="username" bind:value={member.username} required />
          </div>
          <div class="form-group">
            <label for="member_id">Member ID:</label>
            <input id="member_id" bind:value={member.member_id} readonly />
          </div>
          <div class="form-group">
            <label for="email">Email:</label>
            <input id="email" bind:value={member.email} required />
          </div>
          <div class="form-group">
            <label for="whatsapp">WhatsApp Number:</label>
            <input id="whatsapp" bind:value={member.whatsapp} />
          </div>
          <div class="form-group">
            <label for="point">Point:</label>
            <input id="point" type="number" bind:value={member.point} />
          </div>
          <div class="form-group">
            <label for="number_of_cars">Number of Cars:</label>
            <input id="number_of_cars" type="number" bind:value={member.number_of_cars} />
          </div>
          <div class="form-group">
            <label for="address">Address:</label>
            <input id="address" bind:value={member.address} />
          </div>
          <div class="form-group">
            <label for="province">Province:</label>
            <select id="province" bind:value={member.province}>
              <option value="">Select Province</option>
              <option value="Jawa Barat">Jawa Barat</option>
              <option value="DKI Jakarta">DKI Jakarta</option>
              <option value="Jawa Timur">Jawa Timur</option>
              <option value="Jawa Tengah">Jawa Tengah</option>
            </select>
          </div>
          <div class="form-group">
            <label for="region">Region:</label>
            <select id="region" bind:value={member.region}>
              <option value="">Select Region</option>
              <option value="Kota Bogor">Kota Bogor</option>
              <option value="Jakarta Selatan">Jakarta Selatan</option>
              <option value="Kota Surabaya">Kota Surabaya</option>
              <option value="Kota Semarang">Kota Semarang</option>
            </select>
          </div>
          <div class="form-group">
            <label for="status">Status:</label>
            <input type="checkbox" id="status" bind:checked={member.status} />
          </div>
        </div>
      </div>
      <button on:click={saveMember} class="save-btn">Save Changes</button>
    </div>
  {:else}
    <p class="loading">Loading member data...</p>
  {/if}
</div>

<style>
  .member-edit {
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
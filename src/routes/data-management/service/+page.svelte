<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let services = [];
  let searchTerm = '';
  let errorMessage = '';
  let showAddPopup = false;
  let newService = { name: '', description: '', duration: 0, price: 0 };

  onMount(async () => {
    try {
      const { data, error } = await supabase.from('services').select('*');
      if (error) throw error;
      services = data;
    } catch (error) {
      errorMessage = 'Failed to load services.';
    }
  });

  $: filteredServices = services.filter(service =>
    service.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  async function addService() {
    if (!newService.name.trim() || newService.duration <= 0 || newService.price <= 0) {
      errorMessage = 'Please fill in all required fields with valid values.';
      return;
    }
    try {
      const { error } = await supabase.from('services').insert({
        name: newService.name.trim(),
        description: newService.description || null,
        duration: newService.duration,
        price: newService.price,
        created_at: new Date().toISOString(),
        updated_at: new Date().toISOString()
      });
      if (error) throw error;
      newService = { name: '', description: '', duration: 0, price: 0 };
      showAddPopup = false;
      const { data } = await supabase.from('services').select('*');
      services = data;
      errorMessage = '';
    } catch (error) {
      errorMessage = 'Failed to add service.';
    }
  }

  function viewServiceDetail(service) {
    goto(`/data-management/service-detail?id=${service.id}`);
  }

  function editService(service) {
    goto(`/data-management/service-edit?id=${service.id}`);
  }
</script>

<div class="service-list">
  <h1>Service</h1>
  <p class="description">Service List | Total Service: {services.length}</p>

  <div class="controls">
    <div class="search-container">
      <input
        type="text"
        bind:value={searchTerm}
        placeholder="Search Service"
        class="search-bar"
      />
    </div>
    <button on:click={() => (showAddPopup = true)} class="add-btn">+ Add Service</button>
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Service Name</th>
          <th>Duration (min)</th>
          <th>Price (IDR)</th>
          <th>Status</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredServices as service, index}
          <tr>
            <td>{index + 1}</td>
            <td>{service.name}</td>
            <td>{service.duration}</td>
            <td>{service.price.toLocaleString('id-ID')}</td>
            <td>
              <input type="checkbox" checked={service.status} disabled />
            </td>
            <td>
              <button on:click={() => viewServiceDetail(service)} class="detail-btn">Detail</button>
              <button on:click={() => editService(service)} class="edit-btn">Edit</button>
            </td>
          </tr>
        {/each}
        {#if filteredServices.length === 0}
          <tr>
            <td colspan="6" class="no-data">No services found.</td>
          </tr>
        {/if}
      </tbody>
    </table>
  </div>

  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}

  {#if showAddPopup}
    <div class="popup-overlay" on:click|self={() => (showAddPopup = false)}>
      <div class="popup">
        <h2>Add Service</h2>
        <button class="close-btn" on:click={() => (showAddPopup = false)}>×</button>
        <p>Please fill in the form to add/edit data</p>
        <div class="form-group">
          <label for="serviceName">Service Name:*</label>
          <input
            type="text"
            id="serviceName"
            bind:value={newService.name}
            placeholder="Service Name"
            required
          />
        </div>
        <div class="form-group">
          <label for="description">Description:</label>
          <textarea
            id="description"
            bind:value={newService.description}
            placeholder="Service Description"
          ></textarea>
        </div>
        <div class="form-group">
          <label for="duration">Duration (min):*</label>
          <input
            type="number"
            id="duration"
            bind:value={newService.duration}
            placeholder="Duration in minutes"
            required
          />
        </div>
        <div class="form-group">
          <label for="price">Price (IDR):*</label>
          <input
            type="number"
            id="price"
            bind:value={newService.price}
            placeholder="Price in IDR"
            required
          />
        </div>
        <button on:click={addService} class="add-service-btn">Add Service</button>
      </div>
    </div>
  {/if}
</div>

<style>
  .service-list {
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

  .controls {
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  .search-container {
    flex: 1;
    margin-right: 10px;
  }

  .search-bar {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
  }

  .add-btn {
    padding: 10px 20px;
    background-color: #2c3e50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  .add-btn:hover {
    background-color: #34495e;
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

  .popup-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
  }

  .popup {
    background: white;
    padding: 20px;
    border-radius: 8px;
    width: 300px;
    position: relative;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .popup h2 {
    font-size: 1.2rem;
    color: #2c3e50;
    margin-bottom: 10px;
  }

  .close-btn {
    position: absolute;
    top: 10px;
    right: 10px;
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    color: #7f8c8d;
  }

  .form-group {
    margin-bottom: 15px;
  }

  .form-group label {
    display: block;
    margin-bottom: 5px;
    color: #2c3e50;
    font-weight: 500;
  }

  .form-group input, .form-group textarea {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
  }

  .form-group textarea {
    height: 60px;
    resize: none;
  }

  .add-service-btn {
    padding: 10px 20px;
    background-color: #2c3e50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    width: 100%;
  }

  .add-service-btn:hover {
    background-color: #34495e;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }
</style>
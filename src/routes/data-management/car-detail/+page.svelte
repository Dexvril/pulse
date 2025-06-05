<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let car = null;
  let members = [];
  let currentService = null;
  let brands = [];
  let showChangeDriverPopup = false;
  let newDriverId = null;
  let note = '';
  let errorMessage = '';
  let successMessage = '';

  onMount(async () => {
    const carId = $page.url.searchParams.get('id');
    if (!carId) {
      errorMessage = 'Car ID not provided.';
      return;
    }

    try {
      // Ambil data car
      const { data: carData, error: carError } = await supabase
        .from('cars')
        .select('*')
        .eq('id', carId)
        .single();
      if (carError) throw carError;
      car = carData;

      // Ambil data brands untuk mapping
      const { data: brandData, error: brandError } = await supabase.from('brands').select('id, name');
      if (brandError) throw brandError;
      brands = brandData;

      // Ambil data members untuk opsi driver
      const { data: memberData, error: memberError } = await supabase
        .from('members')
        .select('id, name')
        .order('name', { ascending: true }); // Urutkan berdasarkan nama untuk kemudahan
      if (memberError) throw memberError;
      members = memberData;

      // Ambil data current service jika ada
      if (car.current_service_id) {
        const { data: serviceData, error: serviceError } = await supabase
          .from('services')
          .select('*')
          .eq('id', car.current_service_id)
          .single();
        if (serviceError) throw serviceError;
        currentService = serviceData;
      }

      // Set default newDriverId ke driver saat ini jika ada
      if (car.driver_id) {
        newDriverId = car.driver_id;
      }
    } catch (error) {
      errorMessage = 'Failed to load car details.';
    }
  });

  async function changeDriver() {
    if (!newDriverId) {
      errorMessage = 'Please select a driver.';
      return;
    }
    try {
      const { error } = await supabase
        .from('cars')
        .update({
          driver_id: newDriverId,
          updated_at: new Date().toISOString(),
          note: note || null // Simpan note jika ada
        })
        .eq('id', car.id);
      if (error) throw error;
      car.driver_id = newDriverId;
      const driver = members.find(m => m.id === newDriverId);
      car.driver_name = driver ? driver.name : 'N/A';
      showChangeDriverPopup = false;
      newDriverId = null;
      note = '';
      successMessage = 'Driver changed successfully!';
      setTimeout(() => (successMessage = ''), 2000);
    } catch (error) {
      errorMessage = 'Failed to change driver.';
    }
  }

  function goBack() {
    goto('/data-management/car-information');
  }
</script>

<div class="car-detail">
  <h1>Car Details</h1>
  <button on:click={goBack} class="back-btn">Back</button>
  {#if car}
    <div class="detail-container">
      <div class="car-image">
        <img src="/car-placeholder.png" alt="Car Image" />
      </div>
      <div class="car-info">
        <h2>{car.car_name}</h2>
        <p><strong>Status:</strong> {car.status ? 'Active' : 'Inactive'}</p>
        <p><strong>Car Information</strong></p>
        <p><strong>Chassis Number:</strong> {car.chassis_number}</p>
        <p><strong>License Plate:</strong> {car.license_plate}</p>
        <p><strong>Brand:</strong> {brands.find(b => b.id === car.brand_id)?.name || 'N/A'}</p>
        <p><strong>Model:</strong> {car.model}</p>
        <p><strong>Driver:</strong> {car.driver_name}</p>
        <p><strong>Last Mileage:</strong> {car.last_mileage.toLocaleString('id-ID')} km</p>
        <p><strong>Last Service Date:</strong> {car.last_service_date ? new Date(car.last_service_date).toLocaleDateString('id-ID') : 'N/A'}</p>
        <button on:click={() => (showChangeDriverPopup = true)} class="change-driver-btn">Change Owner</button>
      </div>
    </div>
    <div class="service-history">
      <h3>History Service</h3>
      <table>
        <thead>
          <tr>
            <th>No</th>
            <th>Captain</th>
            <th>Last Mileage</th>
            <th>Service Date</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>1</td>
            <td>Maryadi</td>
            <td>20,000 km</td>
            <td>21/04/2025</td>
            <td><button class="detail-btn">See details</button></td>
          </tr>
          <tr>
            <td>2</td>
            <td>Agus</td>
            <td>20,000 km</td>
            <td>21/03/2025</td>
            <td><button class="detail-btn">See details</button></td>
          </tr>
          <tr>
            <td>3</td>
            <td>Joko</td>
            <td>20,000 km</td>
            <td>21/02/2025</td>
            <td><button class="detail-btn">See details</button></td>
          </tr>
        </tbody>
      </table>
    </div>
    {#if currentService}
      <div class="service-detail">
        <h3>Service Detail</h3>
        <p><strong>Service Date:</strong> {new Date(currentService.created_at).toLocaleDateString('id-ID')}</p>
        <p><strong>Service Status:</strong> {currentService.status ? 'Completed' : 'In Progress'}</p>
        <p><strong>Service Name:</strong> {currentService.name}</p>
        <p><strong>Description:</strong> {currentService.description || 'N/A'}</p>
        <p><strong>Duration:</strong> {currentService.duration} minutes</p>
        <p><strong>Price:</strong> Rp {currentService.price.toLocaleString('id-ID')}</p>
      </div>
    {:else}
      <p class="no-service">No current service in progress.</p>
    {/if}
  {/if}
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
  {#if successMessage}
    <p class="success">{successMessage}</p>
  {/if}

  {#if showChangeDriverPopup}
    <div class="popup-overlay" on:click|self={() => (showChangeDriverPopup = false)}>
      <div class="popup">
        <h2>Change Owner</h2>
        <button class="close-btn" on:click={() => (showChangeDriverPopup = false)}>×</button>
        <p>Please fill in the form to change owner</p>
        <div class="form-group">
          <label for="driver">Owner Name:*</label>
          <select id="driver" bind:value={newDriverId} required>
            <option value="">Select Owner</option>
            {#each members as member}
              <option value={member.id}>{member.name}</option>
            {/each}
          </select>
        </div>
        <div class="form-group">
          <label for="note">Note:</label>
          <input type="text" id="note" bind:value={note} placeholder="Note" />
        </div>
        <button on:click={changeDriver} class="change-driver-btn">Change Owner</button>
      </div>
    </div>
  {/if}
</div>

<style>
  .car-detail {
    padding: 20px;
    background-color: #f4f6f9;
    min-height: calc(100vh - 60px);
    margin-left: 250px;
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

  .detail-container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
    display: flex;
    align-items: center;
    margin-bottom: 20px;
  }

  .car-image img {
    width: 200px;
    height: auto;
  }

  .car-info {
    margin-left: 20px;
  }

  .car-info h2 {
    font-size: 1.5rem;
    margin-bottom: 10px;
  }

  .car-info p {
    margin: 5px 0;
    color: #34495e;
  }

  .change-driver-btn {
    padding: 10px 20px;
    background-color: #2c3e50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  .change-driver-btn:hover {
    background-color: #34495e;
  }

  .service-history {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
    margin-bottom: 20px;
  }

  .service-history h3 {
    font-size: 1.2rem;
    margin-bottom: 10px;
    color: #2c3e50;
  }

  .service-history table {
    width: 100%;
    border-collapse: collapse;
  }

  .service-history th, .service-history td {
    padding: 8px;
    border-bottom: 1px solid #eee;
    text-align: left;
  }

  .service-history th {
    background-color: #2c3e50;
    color: white;
  }

  .service-detail {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
  }

  .service-detail h3 {
    font-size: 1.2rem;
    margin-bottom: 10px;
    color: #2c3e50;
  }

  .service-detail p {
    margin: 5px 0;
    color: #34495e;
  }

  .no-service {
    color: #7f8c8d;
    text-align: center;
    padding: 10px;
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

  .form-group input, .form-group select {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
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

  @media (max-width: 768px) {
    .car-detail {
      margin-left: 0;
    }
  }
</style>
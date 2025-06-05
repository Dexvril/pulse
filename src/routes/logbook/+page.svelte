<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let services = [];
  let cars = [];
  let members = [];
  let selectedBranch = '';
  let selectedCaptain = '';
  let selectedDate = '';
  let errorMessage = '';

  onMount(async () => {
    try {
      console.log('Fetching members...');
      const { data: memberData, error: memberError } = await supabase.from('members').select('id, username');
      if (memberError) throw new Error(`Gagal mengambil anggota: ${memberError.message}`);
      members = memberData;
      console.log('Members fetched:', members);

      console.log('Fetching cars...');
      const { data: carData, error: carError } = await supabase.from('cars').select('id, car_name, license_plate, driver_id, current_service_id');
      if (carError) throw new Error(`Gagal mengambil mobil: ${carError.message}`);
      cars = carData;
      console.log('Cars fetched:', cars);

      console.log('Fetching services...');
      const { data, error } = await supabase.from('services').select('*');
      if (error) throw new Error(`Gagal mengambil layanan: ${error.message}`);
      services = data.map(service => {
        const car = cars.find(c => c.current_service_id === service.id);
        const driver = car ? members.find(m => m.id === car.driver_id) : null;
        return {
          ...service,
          car_name: car ? car.car_name : 'N/A',
          license_plate: car ? car.license_plate : 'N/A',
          driver_name: driver ? driver.username : 'N/A'
        };
      });
      console.log('Services fetched:', services);
    } catch (error) {
      console.error('Error fetching data:', error.message);
      errorMessage = `Gagal memuat data layanan: ${error.message}`;
    }
  });

  $: filteredServices = services.filter(service =>
    (!selectedBranch || selectedBranch === 'Honda Depasar') &&
    (!selectedCaptain || selectedCaptain === 'Maryadi') &&
    (!selectedDate || new Date(service.created_at).toISOString().split('T')[0] === selectedDate)
  );

  function resetFilters() {
    selectedBranch = '';
    selectedCaptain = '';
    selectedDate = '';
  }

  function viewServiceDetail(service) {
    goto(`/service-information?id=${service.id}`);
  }
</script>

<div class="logbook-page">
  <h1>Logbook</h1>
  <p class="description">Daftar riwayat layanan dan status dalam waktu nyata</p>

  <div class="filter-section">
    <div class="filter-group">
      <label for="branch">Cabang:</label>
      <select id="branch" bind:value={selectedBranch}>
        <option value="">- Pilih Cabang -</option>
        <option value="Honda Depasar">Honda Depasar</option>
      </select>
    </div>
    <div class="filter-group">
      <label for="captain">Kapten:</label>
      <select id="captain" bind:value={selectedCaptain}>
        <option value="">- Pilih Kapten -</option>
        <option value="Maryadi">Maryadi</option>
      </select>
    </div>
    <div class="filter-group">
      <label for="date">Tanggal Layanan:</label>
      <input type="date" id="date" bind:value={selectedDate} />
    </div>
    <button on:click={resetFilters} class="reset-btn">Reset</button>
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Nama</th>
          <th>Tanggal Layanan</th>
          <th>Mobil</th>
          <th>Plat Nomor</th>
          <th>Cabang</th>
          <th>Kapten</th>
          <th>Aksi</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredServices as service, index}
          <tr>
            <td>{index + 1}</td>
            <td>{service.name || 'N/A'}</td>
            <td>{new Date(service.created_at).toLocaleDateString('id-ID')}</td>
            <td>{service.car_name || 'N/A'}</td>
            <td>{service.license_plate || 'N/A'}</td>
            <td>Honda Depasar</td>
            <td>Maryadi</td>
            <td>
              <button on:click={() => viewServiceDetail(service)} class="detail-btn">Lihat Detail</button>
            </td>
          </tr>
        {/each}
        {#if filteredServices.length === 0}
          <tr>
            <td colspan="8" class="no-data">Tidak ada layanan ditemukan.</td>
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
  .logbook-page {
    padding: 20px;
    background-color: #f4f6f9;
    min-height: calc(100vh - 60px);
    margin-left: 250px;
    box-sizing: border-box;
    width: calc(100% - 250px);
    transition: margin-left 0.3s ease;
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

  .filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    flex-wrap: wrap;
  }

  .filter-group {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .filter-group label {
    font-size: 0.9rem;
    color: #34495e;
  }

  .filter-group select,
  .filter-group input[type="date"] {
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 0.9rem;
    width: 200px;
  }

  .reset-btn {
    padding: 8px 16px;
    background-color: #f1c40f;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }

  .reset-btn:hover {
    background-color: #d4a017;
  }

  .table-container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
    overflow-x: auto;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    min-width: 800px;
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

  .detail-btn {
    padding: 6px 12px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.8rem;
    background-color: #3498db;
    color: white;
  }

  .detail-btn:hover {
    background-color: #2980b9;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
    font-size: 0.9rem;
  }

  @media (max-width: 768px) {
    .logbook-page {
      margin-left: 0;
      width: 100%;
    }
    .filter-group select,
    .filter-group input[type="date"] {
      width: 100%;
    }
  }
</style>
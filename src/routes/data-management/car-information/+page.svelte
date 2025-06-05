<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let cars = [];
  let members = [];
  let brands = [];
  let searchTerm = '';
  let errorMessage = '';

  onMount(async () => {
    try {
      console.log('Fetching brands...');
      const { data: brandData, error: brandError } = await supabase.from('brands').select('id, name');
      if (brandError) throw new Error(`Gagal mengambil merek: ${brandError.message}`);
      brands = brandData;
      console.log('Brands fetched:', brands);

      console.log('Fetching members...');
      const { data: memberData, error: memberError } = await supabase.from('members').select('id, username');
      if (memberError) throw new Error(`Gagal mengambil anggota: ${memberError.message}`);
      members = memberData;
      console.log('Members fetched:', members);

      console.log('Fetching cars...');
      const { data, error } = await supabase.from('cars').select('*');
      if (error) throw new Error(`Gagal mengambil mobil: ${error.message}`);
      cars = data.map(car => {
        const brand = brands.find(b => b.id === car.brand_id);
        const driver = members.find(m => m.id === car.driver_id);
        return {
          ...car,
          brand_name: brand ? brand.name : 'N/A',
          driver_name: driver ? driver.username : 'N/A'
        };
      });
      console.log('Cars fetched:', cars);
    } catch (error) {
      console.error('Error fetching data:', error.message);
      errorMessage = `Gagal memuat data mobil: ${error.message}`;
    }
  });

  $: filteredCars = cars.filter(car =>
    car.chassis_number.toLowerCase().includes(searchTerm.toLowerCase()) ||
    car.license_plate.toLowerCase().includes(searchTerm.toLowerCase()) ||
    car.car_name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  function viewCarDetail(car) {
    goto(`/data-management/car-detail?id=${car.id}`);
  }
</script>

<div class="car-information">
  <h1>Informasi Mobil</h1>
  <p class="description">Daftar Mobil | Total Mobil: {cars.length}</p>

  <div class="controls">
    <div class="search-container">
      <input
        type="text"
        bind:value={searchTerm}
        placeholder="Cari Mobil"
        class="search-bar"
      />
    </div>
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>No</th>
          <th>Nomor Sasis</th>
          <th>Plat Nomor</th>
          <th>Nama Mobil</th>
          <th>Merek</th>
          <th>Pengemudi</th>
          <th>Kilometer Terakhir</th>
          <th>Layanan Terakhir</th>
          <th>Aktif</th>
          <th>Aksi</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredCars as car, index}
          <tr>
            <td>{index + 1}</td>
            <td>{car.chassis_number}</td>
            <td>{car.license_plate}</td>
            <td>{car.car_name}</td>
            <td>{car.brand_name}</td>
            <td>{car.driver_name}</td>
            <td>{car.last_mileage.toLocaleString('id-ID')}</td>
            <td>{car.last_service_date ? new Date(car.last_service_date).toLocaleDateString('id-ID') : 'N/A'}</td>
            <td>
              <input type="checkbox" checked={car.status} disabled />
            </td>
            <td>
              <button on:click={() => viewCarDetail(car)} class="detail-btn">Lihat Detail</button>
            </td>
          </tr>
        {/each}
        {#if filteredCars.length === 0}
          <tr>
            <td colspan="10" class="no-data">Tidak ada mobil ditemukan.</td>
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
  .car-information {
    padding: 20px;
    background-color: #f4f6f9;
    min-height: calc(100vh - 60px);
    margin-left: 250px;
    transition: margin-left 0.3s ease;
    box-sizing: border-box;
    width: calc(100% - 250px);
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
    justify-content: flex-start;
    margin-bottom: 20px;
  }

  .search-container {
    flex: 1;
    max-width: 300px;
    margin-right: 10px;
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
  }

  @media (max-width: 768px) {
    .car-information {
      margin-left: 0;
      width: 100%;
    }
  }
</style>
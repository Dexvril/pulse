<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let service = null;
  let errorMessage = '';
  let car = null;
  let captain = null;

  onMount(async () => {
    const serviceId = $page.url.searchParams.get('id');
    if (!serviceId) {
      errorMessage = 'ID Layanan tidak diberikan.';
      return;
    }

    try {
      // Ambil data layanan
      const { data: serviceData, error: serviceError } = await supabase
        .from('services')
        .select('*')
        .eq('id', serviceId)
        .single();
      if (serviceError) throw serviceError;
      service = serviceData;

      // Ambil data mobil terkait jika ada
      if (service.car_id) {
        const { data: carData, error: carError } = await supabase
          .from('cars')
          .select('id, car_name, license_plate')
          .eq('id', service.car_id)
          .single();
        if (carError) throw carError;
        car = carData;
      }

      // Ambil data kapten terkait jika ada
      if (service.captain_id) {
        const { data: captainData, error: captainError } = await supabase
          .from('captains')
          .select('id, name')
          .eq('id', service.captain_id)
          .single();
        if (captainError) throw captainError;
        captain = captainData;
      }
    } catch (error) {
      errorMessage = 'Gagal memuat detail layanan.';
    }
  });

  function goBack() {
    goto('/data-management/service');
  }
</script>

<div class="service-detail-page">
  <h1>Detail Layanan</h1>
  <button on:click={goBack} class="back-btn">Kembali</button>
  {#if service}
    <div class="detail-container">
      <h2>{service.name}</h2>
      <p><strong>Status:</strong> {service.status ? 'Selesai' : 'Sedang Berlangsung'}</p>
      <p><strong>Informasi Layanan</strong></p>
      <p><strong>Tanggal Layanan:</strong> {new Date(service.created_at).toLocaleDateString('id-ID')}</p>
      <p><strong>Mobil:</strong> {car ? `${car.car_name} (${car.license_plate})` : 'N/A'}</p>
      <p><strong>Kapten:</strong> {captain ? captain.name : 'N/A'}</p>
      <p><strong>Deskripsi:</strong> {service.description || 'N/A'}</p>
      <p><strong>Durasi:</strong> {service.duration} menit</p>
      <p><strong>Harga:</strong> Rp {service.price.toLocaleString('id-ID')}</p>
    </div>
  {/if}
  {#if errorMessage}
    <p class="error">{errorMessage}</p>
  {/if}
</div>

<style>
  .service-detail-page {
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
  }

  .detail-container h2 {
    font-size: 1.5rem;
    margin-bottom: 10px;
    color: #2c3e50;
  }

  .detail-container p {
    margin: 5px 0;
    color: #34495e;
  }

  .error {
    color: #e74c3c;
    margin-top: 10px;
    text-align: center;
  }

  @media (max-width: 768px) {
    .service-detail-page {
      margin-left: 0;
    }
  }
</style>
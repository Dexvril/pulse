<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';

  let totalMembers = 0;
  let totalServices = 0;
  let recentServices = [];

  // Ambil data dari Supabase saat komponen dimount
  onMount(async () => {
    try {
      // Ambil jumlah total member
      const { count: memberCount, error: memberError } = await supabase
        .from('members')
        .select('*', { count: 'exact', head: true });

      if (memberError) throw memberError;
      totalMembers = memberCount;

      // Ambil jumlah total servis
      const { count: serviceCount, error: serviceError } = await supabase
        .from('services')
        .select('*', { count: 'exact', head: true });

      if (serviceError) throw serviceError;
      totalServices = serviceCount;

      // Ambil 5 aktivitas servis terbaru
      const { data: serviceData, error: recentError } = await supabase
        .from('services')
        .select(`
          *,
          members (name)
        `)
        .order('created_at', { ascending: false })
        .limit(5);

      if (recentError) throw recentError;
      recentServices = serviceData;
    } catch (error) {
      console.error('Error fetching data:', error.message);
    }
  });
</script>

<div class="main-content">
  <div class="header">
    <h1>Dashboard</h1>
  </div>

  <!-- Statistik -->
  <div class="stats">
    <div class="card stat-card">
      <h2>Total Members</h2>
      <p class="stat-number">{totalMembers}</p>
    </div>
    <div class="card stat-card">
      <h2>Total Services</h2>
      <p class="stat-number">{totalServices}</p>
    </div>
  </div>

  <!-- Aktivitas Terbaru -->
  <div class="recent-activities">
    <div class="card">
      <h2>Recent Service Activities</h2>
      {#if recentServices.length > 0}
        <table>
          <thead>
            <tr>
              <th>Member Name</th>
              <th>Service Type</th>
              <th>Date</th>
            </tr>
          </thead>
          <tbody>
            {#each recentServices as service}
              <tr>
                <td>{service.members?.name || 'Unknown'}</td>
                <td>{service.service_type}</td>
                <td>{new Date(service.created_at).toLocaleDateString()}</td>
              </tr>
            {/each}
          </tbody>
        </table>
      {:else}
        <p>No recent activities found.</p>
      {/if}
    </div>
  </div>
</div>

<style>
  .main-content {
    margin-left: 250px;
    padding: 20px;
    width: calc(100% - 250px);
    overflow-y: auto;
  }

  .header {
    background-color: white;
    padding: 10px 15px;
    border-bottom: 1px solid #ddd;
    margin-bottom: 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .header h1 {
    font-size: 18px;
    color: #1a3c54;
  }

  .stats {
    display: flex;
    gap: 20px;
    margin-bottom: 20px;
  }

  .card {
    background-color: white;
    padding: 15px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .stat-card {
    flex: 1;
    text-align: center;
  }

  .stat-card h2 {
    font-size: 16px;
    color: #1a3c54;
    margin-bottom: 10px;
  }

  .stat-number {
    font-size: 24px;
    font-weight: bold;
    color: #1a3c54;
  }

  .recent-activities {
    margin-top: 20px;
  }

  .recent-activities h2 {
    font-size: 16px;
    color: #1a3c54;
    margin-bottom: 10px;
  }

  table {
    width: 100%;
    border-collapse: collapse;
  }

  th, td {
    padding: 10px;
    text-align: left;
    border-bottom: 1px solid #ddd;
  }

  th {
    background-color: #f4f6f9;
    color: #1a3c54;
  }

  td {
    color: #333;
  }
</style>
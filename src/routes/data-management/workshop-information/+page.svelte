<script>
  import { supabase } from '$lib/supabaseClient';
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let brands = [];
  let branches = [];
  let searchTerm = '';
  let activeTab = 'brand';
  let errorMessage = '';

  onMount(async () => {
    try {
      const { data: brandData, error: brandError } = await supabase.from('brands').select('*');
      if (brandError) throw brandError;
      brands = brandData.map(brand => ({
        ...brand,
        services: brand.services || [],
        spares: brand.spares || []
      }));

      const { data: branchData, error: branchError } = await supabase.from('branches').select('*');
      if (branchError) throw branchError;
      branches = branchData;
    } catch (error) {
      errorMessage = 'Failed to load data.';
    }
  });

  $: filteredBrands = brands.filter(brand =>
    brand.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  $: filteredBranches = branches.filter(branch =>
    branch.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  function viewBrandDetail(brand) {
    goto(`/data-management/brand-detail?id=${brand.id}`);
  }

  function editBrand(brand) {
    goto(`/data-management/brand-edit?id=${brand.id}`);
  }

  function viewBranchDetail(branch) {
    goto(`/data-management/branch-detail?id=${branch.id}`);
  }

  function editBranch(branch) {
    goto(`/data-management/branch-edit?id=${branch.id}`);
  }
</script>

<div class="workshop-info">
  <h1>Workshop Information</h1>
  <p class="description">Centralized details of the workshop, brands, main workshops, and branches.</p>

  <div class="tabs">
    <button
      class="tab {activeTab === 'brand' ? 'active' : ''}"
      on:click={() => (activeTab = 'brand')}>Brand</button>
    <button
      class="tab {activeTab === 'branch' ? 'active' : ''}"
      on:click={() => (activeTab = 'branch')}>Branch</button>
  </div>

  <div class="content">
    <div class="search-container">
      <input
        type="text"
        bind:value={searchTerm}
        placeholder="Search {activeTab === 'brand' ? 'Brand' : 'Branch'}"
        class="search-bar"
      />
    </div>

    {#if activeTab === 'brand'}
      <div class="brand-list">
        <table>
          <thead>
            <tr>
              <th>No</th>
              <th>Brand Name</th>
              <th>Address</th>
              <th>Province</th>
              <th>Region</th>
              <th>Contact</th>
              <th>Email</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            {#each filteredBrands as brand, index}
              <tr>
                <td>{index + 1}</td>
                <td>{brand.name}</td>
                <td>{brand.address}</td>
                <td>{brand.province}</td>
                <td>{brand.region}</td>
                <td>{brand.contact}</td>
                <td>{brand.email}</td>
                <td>
                  <button on:click={() => viewBrandDetail(brand)} class="detail-btn">Detail</button>
                  <button on:click={() => editBrand(brand)} class="edit-btn">Edit</button>
                </td>
              </tr>
            {/each}
            {#if filteredBrands.length === 0}
              <tr>
                <td colspan="8" class="no-data">No brands found.</td>
              </tr>
            {/if}
          </tbody>
        </table>
      </div>
    {:else if activeTab === 'branch'}
      <div class="branch-list">
        <table>
          <thead>
            <tr>
              <th>No</th>
              <th>Branch Name</th>
              <th>Address</th>
              <th>Province</th>
              <th>Region</th>
              <th>WhatsApp Number</th>
              <th>Email</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            {#each filteredBranches as branch, index}
              <tr>
                <td>{index + 1}</td>
                <td>{branch.name}</td>
                <td>{branch.address}</td>
                <td>{branch.province}</td>
                <td>{branch.region}</td>
                <td>{branch.whatsapp}</td>
                <td>{branch.email}</td>
                <td>
                  <button on:click={() => viewBranchDetail(branch)} class="detail-btn">Detail</button>
                  <button on:click={() => editBranch(branch)} class="edit-btn">Edit</button>
                </td>
              </tr>
            {/each}
            {#if filteredBranches.length === 0}
              <tr>
                <td colspan="8" class="no-data">No branches found.</td>
              </tr>
            {/if}
          </tbody>
        </table>
      </div>
    {/if}

    {#if errorMessage}
      <p class="error">{errorMessage}</p>
    {/if}
  </div>
</div>

<style>
  .workshop-info {
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

  .tabs {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
  }

  .tab {
    padding: 10px 20px;
    border: none;
    border-radius: 4px;
    background-color: #ecf0f1;
    cursor: pointer;
    font-weight: 500;
  }

  .tab.active {
    background-color: #3498db;
    color: white;
  }

  .content {
    display: flex;
    flex-direction: column;
    gap: 20px;
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

  .brand-list, .branch-list {
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

  @media (max-width: 768px) {
    .content {
      flex-direction: column;
    }
  }
</style>
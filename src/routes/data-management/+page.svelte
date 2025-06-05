<script>
  import { goto } from '$app/navigation';

  function navigateTo(path) {
    goto(path);
  }
</script>

<div class="data-management">
  <div class="sidebar">
    <h2>Data Management</h2>
    <ul>
      <li>
        <button on:click={() => navigateTo('/data-management/workshop-information')}>
          Workshop Information
        </button>
      </li>
      <li>
        <button on:click={() => navigateTo('/data-management/member')}>
          Member
        </button>
      </li>
      <li>
        <button on:click={() => navigateTo('/data-management/captain')}>
          Captain
        </button>
      </li>
      <li>
        <button on:click={() => navigateTo('/data-management/available-brand')}>
          Available Brand
        </button>
      </li>
    </ul>
  </div>
  <div class="content">
    <slot />
  </div>
</div>

<style>
  .data-management {
    display: flex;
    min-height: calc(100vh - 60px);
    background-color: #f4f6f9;
  }

  .sidebar {
    width: 250px;
    background-color: #2c3e50;
    color: white;
    padding: 20px;
  }

  .sidebar h2 {
    font-size: 1.5rem;
    margin-bottom: 20px;
    font-weight: 600;
  }

  .sidebar ul {
    list-style: none;
    padding: 0;
  }

  .sidebar li {
    margin-bottom: 10px;
  }

  .sidebar button {
    width: 100%;
    padding: 10px;
    background-color: transparent;
    color: white;
    border: none;
    border-radius: 4px;
    text-align: left;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.2s;
  }

  .sidebar button:hover {
    background-color: #34495e;
  }

  .content {
    flex: 1;
    padding: 20px;
    background-color: #f4f6f9;
  }

  @media (max-width: 768px) {
    .data-management {
      flex-direction: column;
    }

    .sidebar {
      width: 100%;
      padding: 10px;
    }

    .content {
      padding: 10px;
    }
  }
</style>
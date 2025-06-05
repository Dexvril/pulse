<script>
  import { page } from '$app/stores'; // Pastikan dari $app/stores
  import { createEventDispatcher, onMount } from 'svelte'; // Impor onMount dari svelte

  const dispatch = createEventDispatcher();

  let isAccountOpen = false;

  function toggleAccount() {
    isAccountOpen = !isAccountOpen;
  }

  function handleOutsideClick(event) {
    const header = event.target.closest('.header');
    if (!header && isAccountOpen) {
      isAccountOpen = false;
    }
  }

  onMount(() => {
    document.addEventListener('click', handleOutsideClick);
    return () => document.removeEventListener('click', handleOutsideClick);
  });
</script>

<div class="header p-10 m-10" on:click|stopPropagation role="banner">
  <h1>{($page.url.pathname.split('/')[1] || 'dashboard').replace('-', ' ').toUpperCase()}</h1>
  <div class="right-section">
    <div class="notification" role="button" tabindex="0" on:click|preventDefault on:keydown={toggleAccount}>
      <span class="badge">12</span>
      <span class="icon">🔔</span>
    </div>
    <div class="account-drop" role="button" tabindex="0" on:click|preventDefault={toggleAccount} on:keydown={toggleAccount}>
      <img src="https://via.placeholder.com/30" alt="Profile" />
      <span class=" font-bold">Autopulse Admin</span>
      {#if isAccountOpen}
        <div class="account-dropdown" role="menu">
          <a href="/logout" on:click|preventDefault={() => dispatch('logout')}>Super Admin</a>
        </div>
      {/if}
    </div>
  </div>
</div>

<style>
  .header {
    background-color: #1a3c54;
    padding: 10px 15px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: white;
    position: fixed;
    top: 0;
    left: 250px;
    right: 0;
    z-index: 1000;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  h1 {
    font-size: 18px;
    margin: 0;
  }

  .right-section {
    display: flex;
    align-items: center;
    gap: 15px;
  }

  .notification {
    position: relative;
    cursor: pointer;
  }

  .badge {
    position: absolute;
    top: -5px;
    right: -5px;
    background-color: #e74c3c;
    color: white;
    border-radius: 50%;
    width: 18px;
    height: 18px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
  }

  .icon {
    font-size: 20px;
  }

  .account-drop {
    display: flex;
    align-items: center;
    gap: 10px;
    cursor: pointer;
  }

  .account-drop img {
    width: 30px;
    height: 30px;
    border-radius: 50%;
  }

  .account-dropdown {
    position: absolute;
    top: 100%;
    right: 0;
    background-color: #1a3c54;
    min-width: 150px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
    z-index: 1001;
    border-radius: 4px;
  }

  .account-dropdown a {
    color: white;
    padding: 10px 15px;
    display: block;
    text-decoration: none;
  }

  .account-dropdown a:hover {
    background-color: #2980b9;
  }
</style>
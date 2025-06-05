<script>
	import "../app.css"
  import { page } from '$app/stores';
  import Sidebar from '$lib/components/Sidebar.svelte';
  import Header from '$lib/components/Header.svelte';

  function handleLogout() {
    console.log('Logout clicked');
    // Tambahkan logika logout dengan Supabase di sini, misalnya: supabase.auth.signOut()
  }

  // Tentukan apakah rute saat ini adalah /login
  $: isLoginPage = $page.url.pathname === '/login';
</script>

<div class="layout">
  {#if !isLoginPage}
    <Sidebar />
    <div class="main-container">
      <Header on:logout={handleLogout} />
      <main>
        <slot />
      </main>
    </div>
  {:else}
    <main>
      <slot />
    </main>
  {/if}
</div>

<style>
  .layout {
    display: flex;
  }

  .main-container {
    margin-left: 250px;
    width: calc(100% - 250px);
  }

  main {
    margin-top: 0; /* Reset margin-top jika bukan login page */
    padding: 20px;
  }

  /* Styling khusus untuk halaman login */
  main.login-page {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #f4f6f9;
  }
</style>
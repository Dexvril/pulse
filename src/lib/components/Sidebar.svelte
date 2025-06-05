<script>
  import { page } from '$app/stores';

  let isDataManagementOpen = true;
</script>

<aside>
  <div class="logo">
    <img src="/autopulse-logo.png" alt="Autopulse Logo" />
    <h1>AUTOPULSE</h1>
  </div>
  <nav>
    <ul>
      <li class:active={$page.url.pathname === '/dashboard'}>
        <a href="/dashboard">Dashboard</a>
      </li>
      <li class:active={$page.url.pathname.startsWith('/data-management')}>
        <a href="/data-management" on:click|preventDefault={() => (isDataManagementOpen = !isDataManagementOpen)}>
          Data Management {isDataManagementOpen ? '▼' : '▶'}
        </a>
        <ul class="submenu" class:collapsed={!isDataManagementOpen}>
          <li class:active={$page.url.pathname === '/data-management/workshop-information'}>
            <a href="/data-management/workshop-information">Workshop Information</a>
          </li>
          <li class:active={$page.url.pathname === '/data-management/member'}>
            <a href="/data-management/member">Member</a>
          </li>
          <li class:active={$page.url.pathname === '/data-management/captain'}>
            <a href="/data-management/captain">Captain</a>
          </li>
          <li class:active={$page.url.pathname === '/data-management/available-brand'}>
            <a href="/data-management/available-brand">Available Brand</a>
          </li>
          <li class:active={$page.url.pathname === '/data-management/service'}>
            <a href="/data-management/service">Servis</a>
          </li>
          <li class:active={$page.url.pathname === '/data-management/spare-part'}>
            <a href="/data-management/spare-part">Spare Part</a>
          </li>
          <li class:active={$page.url.pathname === '/data-management/car-information'}>
            <a href="/data-management/car-information">Car Information</a>
          </li>
        </ul>
      </li>
      <li class:active={$page.url.pathname === '/service-information'}>
        <a href="/service-information">Informasi Servis</a>
      </li>
       <li class:active={$page.url.pathname === '/logbook'}>
        <a href="/logbook">logbook</a>
      </li>
        <li class:active={$page.url.pathname === '/promotion'}>
        <a href="/promotion">Promosi</a>
      </li>
        <li class:active={$page.url.pathname === '/brand-registation'}>
        <a href="/brand-registation">Brand Registation</a>
      </li>
    </ul>
  </nav>
</aside>

<style>
  aside {
    width: 250px;
    height: 100vh;
    background: linear-gradient(180deg, #2c3e50 0%, #3498db 100%);
    color: white;
    position: fixed;
    top: 0;
    left: 0;
    padding: 20px;
  }

  .logo {
    text-align: center;
    margin-bottom: 30px;
  }

  .logo img {
    width: 50px;
    height: auto;
  }

  h1 {
    font-size: 1.5rem;
    margin-top: 10px;
  }

  nav ul {
    list-style: none;
    padding: 0;
  }

  nav ul li {
    margin-bottom: 10px;
  }

  nav ul li a {
    color: white;
    text-decoration: none;
    display: block;
    padding: 10px;
    border-radius: 4px;
  }

  nav ul li a:hover, nav ul li.active a {
    background-color: rgba(255, 255, 255, 0.2);
  }

  .submenu {
    margin-top: 5px;
    margin-left: 20px;
    list-style: none;
    transition: max-height 0.3s ease-out;
    max-height: 500px;
  }

  .submenu.collapsed {
    max-height: 0;
    overflow: hidden;
  }

  .submenu li a {
    padding: 5px 10px;
    font-size: 0.9rem;
  }
</style>
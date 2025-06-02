<!-- src/lib/components/Sidebar.svelte -->
<script>
    export let activeMenu = '';
</script>

<div class="sidebar">
    <div class="logo">Autopulse</div>
    <ul>
        <li class:active={activeMenu === 'dashboard'}>
            <a href="/dashboard"><span>📊</span> Dashboard</a>
        </li>
        <li class:active={activeMenu === 'data-management'} class="dropdown">
            <a href="#" class="dropdown-toggle"><span>📊</span> Data Management</a>
            <div class="dropdown-content">
                <a href="/data-management" class:active={activeMenu === 'workshop'}>Workshop Information</a>
                <a href="/member" class:active={activeMenu === 'member'}>Member</a>
                <a href="/captain" class:active={activeMenu === 'captain'}>Captain</a>
                <a href="/available-brand" class:active={activeMenu === 'available-brand'}>Available Brand</a>
                <a href="/service" class:active={activeMenu === 'service'}>Service</a>
                <a href="/spare-part" class:active={activeMenu === 'spare-part'}>Spare Part</a>
                <a href="/car-information" class:active={activeMenu === 'car-information'}>Car Information</a>
            </div>
        </li>
        <li class:active={activeMenu === 'aktivitas-servis'}>
            <a href="/aktivitas-servis"><span>🔧</span> Aktivitas Servis</a>
        </li>
        <li class:active={activeMenu === 'logbook'}>
            <a href="/logbook"><span>📖</span> Logbook</a>
        </li>
        <li class:active={activeMenu === 'finance'}>
            <a href="/finance"><span>💰</span> Finance</a>
        </li>
        <li class:active={activeMenu === 'promo'}>
            <a href="/promo"><span>📣</span> Promo</a>
        </li>
        <li class:active={activeMenu === 'brand-registration'}>
            <a href="/brand-registration"><span>🏷️</span> Brand Registration</a>
        </li>
    </ul>
</div>

<style>
    .sidebar {
        width: 250px;
        height: 100vh;
        background-color: #1a3c54;
        color: white;
        position: fixed;
        padding: 20px 0;
        display: flex;
        flex-direction: column;
        z-index: 100;
    }

    .sidebar .logo {
        font-size: 20px;
        text-align: center;
        margin-bottom: 30px;
        color: #3498db;
    }

    .sidebar ul {
        list-style: none;
        flex-grow: 1;
    }

    .sidebar ul li {
        padding: 10px 20px;
        position: relative;
    }

    .sidebar ul li a {
        color: white;
        text-decoration: none;
        font-size: 14px;
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .sidebar ul li a:hover {
        background-color: #2980b9;
    }

    .sidebar ul li.active > a {
        background-color: #2980b9;
    }

    .dropdown {
        position: relative;
    }

    .dropdown-toggle {
        cursor: pointer;
    }

    .dropdown-content {
        display: none;
        position: absolute;
        left: 100%;
        top: 0;
        background-color: #1a3c54;
        min-width: 200px;
        box-shadow: 0 8px 16px rgba(0,0,0,0.2);
        z-index: 101;
    }

    .dropdown:hover .dropdown-content {
        display: block;
    }

    .dropdown-content a {
        color: white;
        padding: 10px 20px;
        text-decoration: none;
        display: block;
        font-size: 12px;
    }

    .dropdown-content a:hover {
        background-color: #2980b9;
    }

    .dropdown-content a.active {
        background-color: #2980b9;
    }
</style>
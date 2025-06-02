<!-- src/routes/dashboard.svelte -->
<script>
    import Sidebar from '$lib/components/Sidebar.svelte';
    let branch = "Kota Bogor";
</script>

<Sidebar activeMenu="dashboard" />

<div class="main-content">
    <div class="header">
        <h1>Dashboard</h1>
        <div class="stats">
            <span>12 Aktif</span>
            <span>12</span>
            <span>12</span>
            <span>12</span>
        </div>
    </div>

    <div class="filters">
        <div class="filters-left">
            <select bind:value={branch}>
                <option>Branch</option>
                <option>Kota Bogor</option>
            </select>
        </div>
    </div>

    <div class="card">
        <h2>Payment Confirmation</h2>
        <p>List data yang belum melengkapi pembayaran</p>
        <table>
            <thead>
                <tr>
                    <th>No</th>
                    <th>Name</th>
                    <th>Car</th>
                    <th>Service Date</th>
                    <th>Inspector</th>
                    <th>Payment</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>1</td>
                    <td>Mulyono</td>
                    <td>Toyota Yaris</td>
                    <td>12/05/2024</td>
                    <td>Maryadi</td>
                    <td class="status-waiting">Waiting for payment</td>
                </tr>
                <tr>
                    <td>2</td>
                    <td>Taka</td>
                    <td>Toyota Yaris</td>
                    <td>12/05/2024</td>
                    <td>Pontianak</td>
                    <td class="status-waiting">Waiting for payment</td>
                </tr>
                <tr>
                    <td>3</td>
                    <td>Vero</td>
                    <td>Toyota Yaris</td>
                    <td>12/05/2024</td>
                    <td>Jember</td>
                    <td class="status-waiting">Waiting for payment</td>
                </tr>
                <tr>
                    <td>4</td>
                    <td>Jeki</td>
                    <td>Toyota Yaris</td>
                    <td>12/05/2024</td>
                    <td>Bogor</td>
                    <td class="status-completed">Completed</td>
                </tr>
                <tr>
                    <td>5</td>
                    <td>Ana</td>
                    <td>Toyota Yaris</td>
                    <td>12/05/2024</td>
                    <td>Bogor</td>
                    <td class="status-completed">Completed</td>
                </tr>
                <tr>
                    <td>6</td>
                    <td>Bayu</td>
                    <td>Toyota Yaris</td>
                    <td>12/05/2024</td>
                    <td>Bogor</td>
                    <td class="status-completed">Completed</td>
                </tr>
            </tbody>
        </table>
        <a href="#" class="see-more">See more</a>
    </div>

    <div style="display: flex; gap: 20px;">
        <div class="card" style="flex: 1;">
            <h2>Company Revenue</h2>
            <p>Total $27 January 2024</p>
            <ul>
                <li>Honda Service <span class="amount">Rp 125,000,000</span></li>
                <li>OtoSport <span class="amount">Rp 125,000,000</span></li>
                <li>Wuling Service <span class="amount">Rp 125,000,000</span></li>
            </ul>
            <a href="#" class="see-more">See more</a>
        </div>
        <div class="card" style="flex: 1;">
            <h2>Withdrawal Confirmation</h2>
            <ul>
                <li>Auto 2000 Silwangi <span class="withdraw">Waiting for payment</span> <span>Rp 125,000,000</span></li>
                <li>Auto 2000 Silwangi <span class="withdraw">Waiting for payment</span> <span>Rp 125,000,000</span></li>
                <li>Auto 2000 Silwangi <span class="withdraw">Waiting for payment</span> <span>Rp 125,000,000</span></li>
            </ul>
            <a href="#" class="see-more">See more</a>
        </div>
    </div>
</div>

<div class="account-drop">
    <img src="https://via.placeholder.com/30" alt="User Profile">
    <span>Autopulse <br> Super Admin <span class="dropdown">▼</span></span>
</div>

<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        font-family: Arial, sans-serif;
    }

    body {
        background-color: #f4f6f9;
        display: flex;
        height: 100vh;
        overflow: hidden;
    }

    .main-content {
        margin-left: 250px;
        padding: 20px;
        width: calc(100% - 250px);
        overflow-y: auto;
        position: relative;
        z-index: 1;
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

    .header .stats {
        display: flex;
        gap: 15px;
    }

    .header .stats span {
        background-color: #1a3c54;
        color: white;
        padding: 5px 10px;
        border-radius: 4px;
        font-size: 14px;
    }

    .filters {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 10px;
    }

    .filters-left {
        display: flex;
        gap: 10px;
        align-items: center;
    }

    .filters-left select {
        padding: 5px;
        border: 1px solid #ddd;
        border-radius: 4px;
    }

    .card {
        background-color: white;
        padding: 15px;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        margin-bottom: 20px;
    }

    .card h2 {
        font-size: 16px;
        color: #1a3c54;
        margin-bottom: 10px;
    }

    .card p {
        font-size: 14px;
        color: #333;
        margin-bottom: 10px;
    }

    table {
        width: 100%;
        border-collapse: collapse;
        margin-bottom: 10px;
    }

    table, th, td {
        border: 1px solid #ddd;
    }

    th, td {
        padding: 8px;
        text-align: left;
    }

    th {
        background-color: #1a3c54;
        color: white;
    }

    td {
        background-color: #f9f9f9;
    }

    .status-waiting {
        color: #e74c3c;
        font-weight: bold;
    }

    .status-completed {
        color: #2ecc71;
        font-weight: bold;
    }

    .see-more {
        color: #1a3c54;
        text-decoration: none;
        font-size: 14px;
        display: inline-block;
        margin-top: 10px;
    }

    .see-more:hover {
        text-decoration: underline;
    }

    .card ul {
        list-style: none;
        margin-bottom: 10px;
    }

    .card ul li {
        display: flex;
        justify-content: space-between;
        padding: 8px 0;
        border-bottom: 1px solid #ddd;
    }

    .card ul li:last-child {
        border-bottom: none;
    }

    .amount, .withdraw {
        color: #333;
        font-weight: bold;
    }

    .withdraw {
        color: #e74c3c;
        margin-right: 10px;
    }

    .account-drop {
        position: fixed;
        top: 20px;
        right: 20px;
        background-color: #1a3c54;
        color: white;
        padding: 10px;
        border-radius: 8px;
        display: flex;
        align-items: center;
        gap: 10px;
        z-index: 102;
    }

    .account-drop img {
        width: 30px;
        height: 30px;
        border-radius: 50%;
    }

    .account-drop span {
        font-size: 14px;
    }

    .account-drop .dropdown {
        cursor: pointer;
    }
</style>
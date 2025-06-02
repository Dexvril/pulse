<!-- src/routes/data-management.svelte -->
<script>
    let workshops = [
        { no: 1, name: "Auto 2000 Silwangi", location: "Bogor", phone: "628123456789", status: "Active", lastService: "12/05/2024" },
        { no: 2, name: "Honda Service Center", location: "Jakarta", phone: "628987654321", status: "Active", lastService: "15/05/2024" },
        { no: 3, name: "Wuling Service", location: "Bandung", phone: "628555123456", status: "Inactive", lastService: "10/04/2024" },
        { no: 4, name: "OtoSport", location: "Surabaya", phone: "628111222333", status: "Active", lastService: "20/05/2024" },
        { no: 5, name: "Toyota Service", location: "Bogor", phone: "628444555666", status: "Active", lastService: "18/05/2024" }
    ];
    let branch = "Kota Bogor";
    let entriesPerPage = 10;
    let currentPage = 1;
</script>

<div class="header">
    <h1>Workshop Information</h1>
</div>

<div class="filters">
    <div class="filters-left">
        <select bind:value={branch}>
            <option>Branch</option>
            <option>Kota Bogor</option>
            <option>Jakarta</option>
            <option>Bandung</option>
        </select>
        <p>Total: {workshops.length} Workshops</p>
    </div>
    <div class="filters-right">
        <a href="/add-workshop">Add New Workshop</a>
    </div>
</div>

<div class="card">
    <table>
        <thead>
            <tr>
                <th>No</th>
                <th>Name</th>
                <th>Location</th>
                <th>Phone</th>
                <th>Status</th>
                <th>Last Service</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody>
            {#each workshops.slice((currentPage - 1) * entriesPerPage, currentPage * entriesPerPage) as workshop}
                <tr>
                    <td>{workshop.no}</td>
                    <td>{workshop.name}</td>
                    <td>{workshop.location}</td>
                    <td>{workshop.phone}</td>
                    <td class:status-active={workshop.status === 'Active'} class:status-inactive={workshop.status === 'Inactive'}>{workshop.status}</td>
                    <td>{workshop.lastService}</td>
                    <td class="action-buttons">
                        <a href="/workshop-detail/{workshop.no}"><img src="https://via.placeholder.com/20" alt="See Details"></a>
                    </td>
                </tr>
            {/each}
        </tbody>
    </table>
    <div class="pagination">
        <div>Show <select bind:value={entriesPerPage}>
            <option>10</option>
            <option>20</option>
            <option>50</option>
        </select> entries</div>
        <div class="pages">
            <span on:click|preventDefault={() => currentPage = Math.max(1, currentPage - 1)}></span>
            {#each Array(Math.ceil(workshops.length / entriesPerPage)) as _, i}
                <span class:active={currentPage === i + 1} on:click|preventDefault={() => currentPage = i + 1}>{i + 1}</span>
            {/each}
            <span on:click|preventDefault={() => currentPage = Math.min(Math.ceil(workshops.length / entriesPerPage), currentPage + 1)}>></span>
        </div>
    </div>
</div>

<style>
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

    .filters-right a {
        background-color: #1a3c54;
        color: white;
        padding: 5px 10px;
        border-radius: 4px;
        text-decoration: none;
    }

    .filters-right a:hover {
        background-color: #2980b9;
    }

    .card {
        background-color: white;
        padding: 15px;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    table {
        width: 100%;
        border-collapse: collapse;
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

    .status-active {
        color: #2ecc71;
        font-weight: bold;
    }

    .status-inactive {
        color: #e74c3c;
        font-weight: bold;
    }

    .action-buttons {
        display: flex;
        gap: 10px;
        align-items: center;
    }

    .action-buttons img {
        width: 20px;
        height: 20px;
        cursor: pointer;
    }

    .pagination {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-top: 10px;
    }

    .pagination select {
        padding: 5px;
        border: 1px solid #ddd;
        border-radius: 4px;
    }

    .pagination .pages {
        display: flex;
        gap: 5px;
    }

    .pagination .pages span {
        padding: 5px 10px;
        border: 1px solid #ddd;
        border-radius: 4px;
        cursor: pointer;
    }

    .pagination .pages span.active {
        background-color: #1a3c54;
        color: white;
    }
</style>
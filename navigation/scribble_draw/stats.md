---
layout: post
title: Scribble Stats
search_exclude: true
description: Stats page
permalink: /stats
Author: Max
---

<div>
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            margin: 0;
            padding: 20px;
            background: #121212;
            color: #fff;
        }
        .statistics-container {
            width: 100%;
            max-width: 1200px;
            margin: 0 auto;
            background: linear-gradient(145deg, #1e1e1e, #2a2a2a);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.6);
        }
        .statistics-header h1 {
            font-size: 2.8rem;
            text-align: center;
            margin-bottom: 40px;
            color: #ffcc00;
        }
        .stats-table {
            width: 100%;
            border-collapse: collapse;
            background: #1e1e1e;
            border-radius: 15px;
            overflow: hidden;
        }
        .stats-table th, .stats-table td {
            padding: 15px;
            text-align: center;
            border-bottom: 1px solid #333;
        }
        .stats-table th {
            background: #2d2d2d;
            color: #00ffcc;
            cursor: pointer;
            position: relative;
        }
        .stats-table th:hover {
            background: #363636;
        }
        .stats-table tr:hover {
            background: #252525;
        }
        .stats-table tbody tr:last-child td {
            border-bottom: none;
        }
        .input-form {
            margin-top: 30px;
            padding: 20px;
            background: #1e1e1e;
            border-radius: 15px;
        }
        .input-form input {
            padding: 10px;
            margin: 10px;
            background: #2d2d2d;
            border: 1px solid #333;
            color: #fff;
            border-radius: 5px;
        }
        .input-form button {
            padding: 10px 20px;
            background: #00ffcc;
            color: #121212;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .input-form button:hover {
            background: #00ccaa;
        }
        #notification {
            margin-top: 10px;
            padding: 10px;
            border-radius: 5px;
            display: none;
        }
        .delete-btn {
            background: #ff4444;
            color: white;
            border: none;
            border-radius: 5px;
            padding: 5px 10px;
            cursor: pointer;
        }
        .delete-btn:hover {
            background: #cc3333;
        }
        #user-select {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            background: #2d2d2d;
            border: 1px solid #333;
            color: #fff;
            border-radius: 5px;
        }
    </style>
    <div class="statistics-container">
        <div class="statistics-header">
            <h1>🎮 Game Statistics 🎯</h1>
        </div>
        <table class="stats-table">
            <thead>
                <tr>
                    <th onclick="sortTable(0)">Username</th>
                    <th onclick="sortTable(1)">Correct Guesses</th>
                    <th onclick="sortTable(2)">Wrong Guesses</th>
                    <th onclick="sortTable(3)">Win Rate</th>
                </tr>
            </thead>
            <tbody id="stats-body"></tbody>
        </table>
        
        <div class="input-form">
            <h2 style="color: #ffcc00;">Update Statistics</h2>
            <select id="user-select" onchange="handleUserSelect()">
                <option value="">Create New User</option>
            </select>
            <form onsubmit="return updateStatistics(event)">
                <input type="text" id="username-input" placeholder="Username" required>
                <input type="number" id="correct-guesses-input" placeholder="Correct Guesses" required>
                <input type="number" id="wrong-guesses-input" placeholder="Wrong Guesses" required>
                <button type="submit">Update Stats</button>
            </form>
            <div id="notification"></div>
        </div>
    </div>

    <script type="module">
        const pythonURI = 'http://localhost:8887';

        async function fetchStatistics() {
            try {
                const response = await fetch(`${pythonURI}/api/statistics/all`);
                if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                const data = await response.json();
                updateTable(data);
                populateUserSelect(data);
            } catch (error) {
                console.error('Error fetching statistics:', error);
                showNotification('Error fetching statistics', 'error');
            }
        }

        async function updateStatistics(event) {
            event.preventDefault();
            const username = document.getElementById('username-input').value;
            const correct = parseInt(document.getElementById('correct-guesses-input').value) || 0;
            const wrong = parseInt(document.getElementById('wrong-guesses-input').value) || 0;

            try {
                const response = await fetch(`${pythonURI}/api/statistics`, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        username: username,
                        correct: correct,
                        wrong: wrong
                    })
                });

                if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                
                const data = await response.json();
                showNotification('Stats updated successfully!', 'success');
                
                // Clear form
                document.getElementById('username-input').value = '';
                document.getElementById('correct-guesses-input').value = '';
                document.getElementById('wrong-guesses-input').value = '';
                
                // Update table with new data
                updateTable(data);
                
            } catch (error) {
                console.error('Error:', error);
                showNotification('Error updating stats', 'error');
            }
        }

        function updateTable(data) {
            const tbody = document.getElementById('stats-body');
            tbody.innerHTML = '';
            data.forEach(user => {
                const totalGuesses = user.correct_guesses + user.wrong_guesses;
                const winRate = ((user.correct_guesses / (totalGuesses || 1)) * 100).toFixed(1);
                const row = `
                    <tr>
                        <td>${user.username}</td>
                        <td>${user.correct_guesses}</td>
                        <td>${user.wrong_guesses}</td>
                        <td>${winRate}%</td>
                        <td>
                            <button onclick="deleteUser('${user.username}')" class="delete-btn">
                                Delete
                            </button>
                        </td>
                    </tr>
                `;
                tbody.innerHTML += row;
            });
        }

        // Add delete function
        async function deleteUser(username) {
            if (!confirm(`Are you sure you want to delete stats for ${username}?`)) {
                return;
            }

            try {
                const response = await fetch(`${pythonURI}/api/statistics/${username}`, {
                    method: 'DELETE',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                });

                if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                
                showNotification(`Deleted stats for ${username}`, 'success');
                fetchStatistics();
            } catch (error) {
                console.error('Error:', error);
                showNotification('Error deleting stats', 'error');
            }
        }

        // Add to window scope
        window.deleteUser = deleteUser;

        function showNotification(message, type) {
            const notification = document.getElementById('notification');
            notification.textContent = message;
            notification.style.display = 'block';
            notification.style.background = type === 'success' ? '#00ffcc' : '#ff4444';
            notification.style.color = '#121212';
            setTimeout(() => {
                notification.style.display = 'none';
            }, 3000);
        }

        function sortTable(n) {
            const table = document.querySelector('.stats-table');
            const rows = Array.from(table.querySelectorAll('tbody tr'));
            rows.sort((a, b) => {
                const aVal = a.cells[n].textContent;
                const bVal = b.cells[n].textContent;
                return aVal.localeCompare(bVal, undefined, {numeric: true});
            });
            const tbody = table.querySelector('tbody');
            rows.forEach(row => tbody.appendChild(row));
        }

        function populateUserSelect(data) {
            const userSelect = document.getElementById('user-select');
            userSelect.innerHTML = '<option value="">Create New User</option>';
            data.forEach(user => {
                const option = document.createElement('option');
                option.value = user.username;
                option.textContent = user.username;
                userSelect.appendChild(option);
            });
        }

        async function fetchUsers() {
            try {
                const response = await fetch(`${pythonURI}/api/statistics/all`);
                const data = await response.json();
                const select = document.getElementById('user-select');
                select.innerHTML = '<option value="">Create New User</option>';
                data.forEach(user => {
                    const option = document.createElement('option');
                    option.value = user.username;
                    option.textContent = user.username;
                    select.appendChild(option);
                });
            } catch (error) {
                console.error('Error fetching users:', error);
            }
        }

        async function handleUserSelect() {
            const select = document.getElementById('user-select');
            const username = document.getElementById('username-input');
            if (select.value) {
                username.value = select.value;
                username.readOnly = true;
            } else {
                username.value = '';
                username.readOnly = false;
            }
        }

        // Add to window scope and onload
        window.handleUserSelect = handleUserSelect;
        window.onload = () => {
            fetchStatistics();
            fetchUsers();
        };

        window.sortTable = sortTable;
        window.updateStatistics = updateStatistics;
    </script>
</div>





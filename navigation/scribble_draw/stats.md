---
layout: post
title: Scribble Stats
permalink: /stats
---

<table>
    <tr>
        <td><a href="{{site.baseurl}}/index">Home</a></td>
        <td><a href="{{site.baseurl}}/competition">Competitive</a></td>
        <td><a href="{{site.baseurl}}/guess">Guess Game</a></td>
        <td><a href="{{site.baseurl}}/leaderboard">LeaderBoard</a></td>
        <td><a href="{{site.baseurl}}/stats">Statistics</a></td>
        <td><a href="{{site.baseurl}}/about">About Us</a></td>
        <td><a href="{{site.baseurl}}/deploy">Deploy Blog</a></td>
    </tr>
</table>

<div class="statistics-container">
    <style>
        :root {
            --primary-color: #1a237e;
            --secondary-color: #283593;
            --background: linear-gradient(135deg, #e3f2fd, #bbdefb);
            --text-color: #2c3e50;
            --card-bg: #ffffff;
            --error: #e74c3c;
            --success: #2ecc71;
        }
        body {
            background: var(--background);
            margin: 0;
            min-height: 100vh;
            font-family: 'Poppins', -apple-system, BlinkMacSystemFont, sans-serif;
            color: var(--text-color);
        }
        .statistics-container {
            max-width: 1000px;
            margin: 3rem auto;
            padding: 2rem;
            background: linear-gradient(145deg, #1e1e1e, #2a2a2a);
            border-radius: 20px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.6);
        }
        .statistics-header h1 {
            font-size: 2.8rem;
            text-align: center;
            margin-bottom: 40px;
            color: #ffcc00;
        }
        .stats-table {
            width: 100%;
            border-collapse: separate;
            border-spacing: 0 8px;
            margin-top: 2rem;
        }
        .stats-table th {
            background: var(--primary-color);
            color: white;
            padding: 16px;
            text-align: left;
            cursor: pointer;
        }
        .stats-table td {
            background: var(--card-bg);
            padding: 16px;
            color: var(--text-color);
        }
        .input-form {
            margin-top: 30px;
            padding: 20px;
            background: #1e1e1e;
            border-radius: 15px;
        }
        .form-input {
            padding: 12px 16px;
            margin: 10px;
            background: #2d2d2d;
            border: 1px solid #333;
            color: #fff;
            border-radius: 5px;
            width: calc(33% - 20px);
        }
        .submit-button {
            padding: 12px 24px;
            background: #00ffcc;
            color: #121212;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        .delete-btn {
            background: #ff4444;
            color: white;
            border: none;
            border-radius: 5px;
            padding: 5px 10px;
            cursor: pointer;
        }
        #message {
            margin-top: 10px;
            padding: 10px;
            border-radius: 5px;
            display: none;
        }
    </style>

    <div class="statistics-header">
        <h1>🎮 Game Statistics 🎯</h1>
    </div>

    <table class="stats-table">
        <thead>
            <tr>
                <th onclick="sortTable(0)">Username</th>
                <th onclick="sortTable(1)">Correct Guesses</th>
                <th onclick="sortTable(2)">Wrong Guesses</th>
                <th onclick="sortTable(3)">Total Rounds</th>
                <th onclick="sortTable(4)">Win Rate</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody id="stats-body"></tbody>
    </table>

    <div class="input-form">
        <h2 style="color: #ffcc00;">Update Statistics</h2>
        <select id="user-select" onchange="handleUserSelect()" class="form-input">
            <option value="">Create New User</option>
        </select>
        <form onsubmit="return updateStatistics(event)">
            <input type="text" id="username-input" class="form-input" placeholder="Username" required>
            <input type="number" id="correct-guesses-input" class="form-input" placeholder="Correct Guesses" required min="0">
            <input type="number" id="wrong-guesses-input" class="form-input" placeholder="Wrong Guesses" required min="0">
            <button type="submit" class="submit-button">Update Stats</button>
        </form>
        <div id="message"></div>
    </div>
</div>

<script>
    const API_URL = 'http://localhost:8203/api';

    async function checkAuth() {
        const token = localStorage.getItem('token');
        if (!token) {
            showMessage('Please login first', 'error');
            return false;
        }
        return true;
    }

    async function fetchStatistics() {
        if (!await checkAuth()) return;

        try {
            const token = localStorage.getItem('token');
            const response = await fetch(`${API_URL}/statistics/all`, {
                headers: {
                    'Authorization': `Bearer ${token}`
                }
            });
            
            if (!response.ok) throw new Error('Failed to fetch statistics');
            const data = await response.json();
            updateTable(data.data || []);
            populateUserSelect(data.data || []);
        } catch (error) {
            console.error('Error:', error);
            showMessage('Error loading statistics', 'error');
        }
    }

    function updateTable(data) {
        const tbody = document.getElementById('stats-body');
        tbody.innerHTML = '';
        data.forEach(user => {
            const row = `
                <tr>
                    <td>${user.username}</td>
                    <td>${user.correct_guesses}</td>
                    <td>${user.wrong_guesses}</td>
                    <td>${user.total_rounds}</td>
                    <td>${user.win_rate}%</td>
                    <td>
                        <button onclick="deleteStats('${user.username}')" class="delete-btn">Delete</button>
                    </td>
                </tr>
            `;
            tbody.innerHTML += row;
        });
    }

    async function updateStatistics(event) {
        event.preventDefault();
        if (!await checkAuth()) return;

        const username = document.getElementById('username-input').value;
        const correct = parseInt(document.getElementById('correct-guesses-input').value) || 0;
        const wrong = parseInt(document.getElementById('wrong-guesses-input').value) || 0;

        try {
            const token = localStorage.getItem('token');
            const response = await fetch(`${API_URL}/statistics`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${token}`
                },
                body: JSON.stringify({
                    username: username,
                    correct: correct,
                    wrong: wrong
                })
            });

            if (!response.ok) throw new Error('Failed to update statistics');
            showMessage('Stats updated successfully!', 'success');
            clearForm();
            await fetchStatistics();
        } catch (error) {
            console.error('Error:', error);
            showMessage(error.message, 'error');
        }
    }

    async function deleteStats(username) {
        if (!await checkAuth()) return;
        if (!confirm(`Are you sure you want to delete stats for ${username}?`)) return;

        try {
            const token = localStorage.getItem('token');
            const response = await fetch(`${API_URL}/statistics`, {
                method: 'DELETE',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${token}`
                },
                body: JSON.stringify({ username: username })
            });

            if (!response.ok) throw new Error('Failed to delete statistics');
            showMessage(`Deleted stats for ${username}`, 'success');
            await fetchStatistics();
        } catch (error) {
            console.error('Error:', error);
            showMessage(error.message, 'error');
        }
    }

    function showMessage(message, type) {
        const messageEl = document.getElementById('message');
        messageEl.textContent = message;
        messageEl.style.display = 'block';
        messageEl.style.background = type === 'success' ? '#2ecc71' : '#e74c3c';
        messageEl.style.color = 'white';
        setTimeout(() => messageEl.style.display = 'none', 3000);
    }

    function clearForm() {
        document.getElementById('username-input').value = '';
        document.getElementById('correct-guesses-input').value = '';
        document.getElementById('wrong-guesses-input').value = '';
        document.getElementById('user-select').value = '';
    }

    function handleUserSelect() {
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

    function sortTable(n) {
        const table = document.querySelector('.stats-table');
        const rows = Array.from(table.querySelectorAll('tbody tr'));
        const isNumeric = n > 0 && n < 5;

        rows.sort((a, b) => {
            let aVal = a.cells[n].textContent;
            let bVal = b.cells[n].textContent;

            if (isNumeric) {
                aVal = parseFloat(aVal);
                bVal = parseFloat(bVal);
            }

            return aVal > bVal ? 1 : -1;
        });

        const tbody = table.querySelector('tbody');
        rows.forEach(row => tbody.appendChild(row));
    }

    // Initialize
    if (checkAuth()) {
        fetchStatistics();
    }

    // Add functions to window scope
    window.updateStatistics = updateStatistics;
    window.deleteStats = deleteStats;
    window.handleUserSelect = handleUserSelect;
    window.sortTable = sortTable;
</script>
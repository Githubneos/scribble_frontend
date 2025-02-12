---
layout: post
title: Leaderboard
permalink: /leaderboard
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

<div class="leaderboard-container">
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
        .leaderboard-container {
            max-width: 1000px;
            margin: 3rem auto;
            padding: 2rem;
        }
        .leaderboard-title {
            color: var(--primary-color);
            font-size: 2.5rem;
            text-align: center;
            font-weight: 700;
            margin-bottom: 2rem;
            text-transform: uppercase;
            letter-spacing: 2px;
        }
        .form-container {
            background: var(--card-bg);
            border-radius: 16px;
            padding: 2rem;
            margin-bottom: 2rem;
            box-shadow: 0 8px 30px rgba(0,0,0,0.1);
        }
        .input-group {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1rem;
            margin-bottom: 1rem;
        }
        .form-input {
            padding: 12px 16px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 1rem;
            color: var(--text-color);
            width: 100%;
            box-sizing: border-box;
        }
        .submit-button {
            background: var(--primary-color);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        .delete-btn {
            background: var(--error);
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 6px;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        .leaderboard-table {
            width: 100%;
            border-collapse: separate;
            border-spacing: 0 8px;
            margin-top: 2rem;
        }
        .leaderboard-table th {
            background: var(--primary-color);
            color: white;
            padding: 16px;
            text-align: left;
        }
        .leaderboard-table td {
            background: var(--card-bg);
            padding: 16px;
        }
        #message {
            padding: 1rem;
            border-radius: 8px;
            margin-top: 1rem;
            display: none;
        }
    </style>
    <h1 class="leaderboard-title">Scribble Masters</h1>
    <div class="form-container">
        <div class="input-group">
            <input type="text" id="profileName" class="form-input" placeholder="Profile Name" required>
            <input type="text" id="drawingName" class="form-input" placeholder="Drawing Name" required>
            <input type="number" id="score" class="form-input" placeholder="Score (0-100)" min="0" max="100" required>
            <button onclick="submitScore()" class="submit-button">Submit Score</button>
        </div>
        <div id="message"></div>
    </div>
    <table class="leaderboard-table">
        <thead>
            <tr>
                <th>Rank</th>
                <th>Player</th>
                <th>Drawing</th>
                <th>Score</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody id="leaderboard"></tbody>
    </table>
</div>

<script>
    const API_URL = 'https://scribble.stu.nighthawkcodingsociety.com/api/leaderboard_api';
    
    async function checkAuth() {
        const token = localStorage.getItem('token');
        if (!token) {
            showMessage('Please login to view the leaderboard', 'error');
            return false;
        }
        return true;
    }

    async function fetchLeaderboard() {
        if (!await checkAuth()) return;
        
        try {
            const token = localStorage.getItem('token');
            const response = await fetch(API_URL, {
                headers: {
                    'Authorization': `Bearer ${token}`
                }
            });
            
            if (!response.ok) throw new Error('Failed to fetch leaderboard');
            const data = await response.json();
            displayLeaderboard(data);
        } catch (error) {
            console.error('Error:', error);
            showMessage('Error loading leaderboard', 'error');
        }
    }

    function displayLeaderboard(entries) {
        const tbody = document.getElementById('leaderboard');
        tbody.innerHTML = '';

        entries.sort((a, b) => b.score - a.score)
            .forEach((entry, index) => {
                const row = `
                    <tr>
                        <td>${index + 1}</td>
                        <td>${entry.profile_name}</td>
                        <td>${entry.drawing_name}</td>
                        <td>${entry.score}</td>
                        <td>
                            <button onclick="deleteEntry('${entry.profile_name}', '${entry.drawing_name}')" 
                                    class="delete-btn">Delete</button>
                        </td>
                    </tr>
                `;
                tbody.innerHTML += row;
            });
    }

    async function submitScore() {
        if (!await checkAuth()) return;

        const profileName = document.getElementById('profileName').value.trim();
        const drawingName = document.getElementById('drawingName').value.trim();
        const score = parseInt(document.getElementById('score').value);

        if (!profileName || !drawingName || isNaN(score) || score < 0 || score > 100) {
            showMessage('Please fill in all fields correctly', 'error');
            return;
        }

        try {
            const token = localStorage.getItem('token');
            const response = await fetch(API_URL, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${token}`
                },
                body: JSON.stringify({
                    profile_name: profileName,
                    drawing_name: drawingName,
                    score: score
                })
            });

            const data = await response.json();
            
            if (response.ok) {
                showMessage('Score submitted successfully', 'success');
                clearForm();
                await fetchLeaderboard();
            } else {
                throw new Error(data.error || 'Failed to submit score');
            }
        } catch (error) {
            console.error('Error:', error);
            showMessage(error.message, 'error');
        }
    }

    async function deleteEntry(profileName, drawingName) {
        if (!await checkAuth()) return;
        
        if (!confirm(`Are you sure you want to delete this entry?`)) return;

        try {
            const token = localStorage.getItem('token');
            const response = await fetch(API_URL, {
                method: 'DELETE',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${token}`
                },
                body: JSON.stringify({
                    profile_name: profileName,
                    drawing_name: drawingName
                })
            });

            const data = await response.json();
            
            if (response.ok) {
                showMessage('Entry deleted successfully', 'success');
                await fetchLeaderboard();
            } else {
                throw new Error(data.error || 'Failed to delete entry');
            }
        } catch (error) {
            console.error('Error:', error);
            showMessage(error.message, 'error');
        }
    }

    function showMessage(message, type) {
    const messageEl = document.getElementById('message');
    messageEl.textContent = message;
    messageEl.style.display = 'block';
    messageEl.style.background = type === 'success' ? '#2ecc71' : '#e74c3c';  // Direct color values
    messageEl.style.color = 'white';
    setTimeout(() => messageEl.style.display = 'none', 3000);
}

    function clearForm() {
        document.getElementById('profileName').value = '';
        document.getElementById('drawingName').value = '';
        document.getElementById('score').value = '';
    }

    // Initialize the page
    if (checkAuth()) {
        fetchLeaderboard();
        setInterval(fetchLeaderboard, 30000); // Refresh every 30 seconds
    }
</script>
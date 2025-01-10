---
layout: post
title: Leaderboard
search_exclude: true
description: Leaderboard
permalink: /leaderboard
Author: Daksha
---

<div class="leaderboard-container">
    <style>
        body {
            background: linear-gradient(135deg, #e0f7fa, #80deea); /* Light blue gradient */
            background-attachment: fixed; /* Keep background fixed */
        }

        .leaderboard-container {
            font-family: 'Poppins', Arial, sans-serif;
            max-width: 800px;
            margin: 2rem auto;
            padding: 2rem;
            background: rgba(255, 255, 255, 0.9); /* Slightly transparent white */
            border-radius: 20px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.2);
        }

        .leaderboard-title {
            color: #000000;  /* Title color set to black */
            font-size: 2.5rem;
            margin-bottom: 2rem;
            text-align: center;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .form-container {
            margin: 2rem auto;
            max-width: 600px;
            display: flex;
            flex-direction: column;
            gap: 1rem;
            padding: 1.5rem;
            background: white;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
        }

        .input-group {
            display: flex;
            gap: 1rem;
            margin-bottom: 1rem;
        }

        .form-input {
            padding: 12px 15px;
            border: 2px solid #eee;
            border-radius: 8px;
            font-size: 1rem;
            transition: border-color 0.3s ease;
            flex: 1;
        }

        .form-input:focus {
            border-color: #2C3E50;
            outline: none;
        }

        .submit-button {
            padding: 12px 25px;
            background: #2C3E50;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }

        .submit-button:hover {
            background: #34495E;
        }

        .leaderboard-table {
            width: 100%;
            border-collapse: separate;
            border-spacing: 0 8px;
            margin: 20px 0;
        }

        .leaderboard-table th {
            background: #2C3E50;
            color: white;
            padding: 15px;
            text-align: left;
            font-weight: 500;
            font-size: 1.1rem;
            border-radius: 8px;
        }

        .leaderboard-table td {
            padding: 15px;
            background: white;
            border: 1px solid #eee;
            color: #000000; /* Set text color to black for all table cells */
        }

        @media (max-width: 768px) {
            .leaderboard-container {
                margin: 1rem;
                padding: 1rem;
            }

            .input-group {
                flex-direction: column;
            }
        }
    </style>

    <h1 class="leaderboard-title">Scribble Masters</h1>
    
    <!-- Manual Input Form -->
    <div class="form-container">
        <div class="input-group">
            <input type="text" id="profileName" placeholder="Profile Name" class="form-input">
            <input type="number" id="score" placeholder="Score (0-100)" class="form-input" style="color: black;"> <!-- Changed to black -->
            <button onclick="addEntry()" class="submit-button">Add to Leaderboard</button>
        </div>
    </div>

    <!-- Leaderboard Table -->
    <table class="leaderboard-table">
        <thead>
            <tr>
                <th>Rank</th>
                <th>Profile Name</th>
                <th>Score</th>
            </tr>
        </thead>
        <tbody id="leaderboard">
            <!-- Entries will be added here dynamically -->
        </tbody>
    </table>
</div>

<script>
    // API endpoint
    const API_URL = 'http://127.0.0.1:8887/api/leaderboard'; // Update this to your backend API URL

    // Fetch and display leaderboard
    async function fetchLeaderboard() {
        const tbody = document.getElementById('leaderboard');
        tbody.innerHTML = '';

        try {
            const response = await fetch(API_URL);
            if (!response.ok) throw new Error('Failed to fetch leaderboard');
            const data = await response.json();
            displayLeaderboard(data);
        } catch (error) {
            console.error('Error:', error);
            tbody.innerHTML = '<tr><td colspan="3" style="color: #000000;">Error loading leaderboard. Please try again later.</td></tr>';
        }
    }

    // Display leaderboard data
    function displayLeaderboard(data) {
        const tbody = document.getElementById('leaderboard');
        tbody.innerHTML = '';

        if (data.length === 0) {
            tbody.innerHTML = '<tr><td colspan="3" style="color: #000000;">No entries yet</td></tr>';
            return;
        }

        data.forEach((entry, index) => {
            const row = document.createElement('tr');
            row.innerHTML = `
                <td>${index + 1}</td>
                <td>${entry.profile_name}</td>
                <td>${entry.score}</td>
            `;
            tbody.appendChild(row);
        });
    }

    // Add new entry manually
    async function addEntry() {
        const profileName = document.getElementById('profileName').value.trim();
        const score = parseInt(document.getElementById('score').value);

        if (!profileName) {
            alert('Please enter a profile name');
            return;
        }

        if (isNaN(score) || score < 0 || score > 100) {
            alert('Please enter a valid score between 0 and 100');
            return;
        }

        try {
            const response = await fetch(API_URL, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    name: profileName,
                    score: score
                })
            });

            if (!response.ok) {
                const error = await response.json();
                throw new Error(error.error || 'Failed to add entry');
            }

            // Clear inputs
            document.getElementById('profileName').value = '';
            document.getElementById('score').value = '';

            // Refresh leaderboard display
            await fetchLeaderboard();
        } catch (error) {
            console.error('Error:', error);
        }
    }

    // Initial load
    fetchLeaderboard();
</script>
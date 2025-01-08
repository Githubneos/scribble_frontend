---
layout: post
title: Leaderboard
search_exclude: true
description: Leaderboard
permalink: /leaderboard
Author: Daksha
---

<div>
    <style>
        div {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #F4F4F9;
            padding: 20px;
        }
        table {
            width: 50%;
            margin: 20px auto;
            border-collapse: collapse;
            background-color: #fff;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }
        th, td {
            padding: 10px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
        tr:hover {
            background-color: #F1F1F1;
        }
        .form-container {
            margin: 20px auto;
            width: 50%;
            display: flex;
            gap: 10px;
        }
        input {
            padding: 10px;
            flex: 1;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        button {
            padding: 10px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background-color: #45A049;
        }
    </style>
    <h1>Leaderboard</h1>
    <table>
        <thead>
            <tr>
                <th>Rank</th>
                <th>Name</th>
                <th>Score</th>
            </tr>
        </thead>
        <tbody id="leaderboard">
            <!-- Rows will be dynamically inserted here -->
        </tbody>
    </table>
    <div class="form-container">
        <input type="text" id="name" placeholder="Enter name">
        <input type="number" id="score" placeholder="Enter score">
        <button id="addButton">Add to Leaderboard</button>
    </div>
</div>
<script>
    let leaderboard = [];

    async function fetchLeaderboard() {
        try {
            const response = await fetch('http://localhost:5001/api/leaderboard');
            const data = await response.json();
            leaderboard = data.map(item => ({
                name: `${item.profile_name} - ${item.drawing_name}`,
                score: item.score
            }));
            renderLeaderboard();
        } catch (error) {
            console.error('Error fetching leaderboard:', error);
        }
    }

    function renderLeaderboard() {
        const tbody = document.getElementById('leaderboard');
        tbody.innerHTML = ""; // Clear existing rows
        // Sort the leaderboard by score in descending order
        leaderboard.sort((a, b) => b.score - a.score);
        leaderboard.forEach((entry, index) => {
            const row = document.createElement('tr');
            row.innerHTML = `
                <td>${index + 1}</td>
                <td>${entry.name}</td>
                <td>${entry.score}</td>
            `;
            tbody.appendChild(row);
        });
    }

    async function addEntry() {
        const nameInput = document.getElementById('name');
        const scoreInput = document.getElementById('score');
        const name = nameInput.value.trim();
        const score = parseInt(scoreInput.value);
        
        if (name && !isNaN(score)) {
            try {
                // Send POST request to API
                const response = await fetch('http://localhost:5001/api/leaderboard', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({ name, score })
                });

                if (!response.ok) {
                    throw new Error('Failed to add entry');
                }

                // Refresh the leaderboard
                await fetchLeaderboard();

                // Clear input fields
                nameInput.value = "";
                scoreInput.value = "";
            } catch (error) {
                console.error('Error adding entry:', error);
                alert('Failed to add entry to leaderboard');
            }
        } else {
            alert('Please enter both a name and a valid score.');
        }
    }

    document.getElementById('addButton').addEventListener('click', addEntry);
    
    // Initial fetch and render
    fetchLeaderboard();
</script>



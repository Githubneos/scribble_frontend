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
        .leaderboard-container {
            font-family: 'Poppins', Arial, sans-serif;
            max-width: 800px;
            margin: 2rem auto;
            padding: 2rem;
            background: linear-gradient(145deg, #f6f8ff, #ffffff);
            border-radius: 20px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.1);
        }

        .leaderboard-title {
            color: #2C3E50;
            font-size: 2.5rem;
            margin-bottom: 2rem;
            text-align: center;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 2px;
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

        .leaderboard-table tr {
            transition: transform 0.2s ease;
        }

        .leaderboard-table tr:hover {
            transform: translateY(-2px);
        }

        .leaderboard-table td {
            padding: 15px;
            background: white;
            border-top: 1px solid #eee;
            border-bottom: 1px solid #eee;
        }

        .leaderboard-table td:first-child {
            border-left: 1px solid #eee;
            border-top-left-radius: 8px;
            border-bottom-left-radius: 8px;
        }

        .leaderboard-table td:last-child {
            border-right: 1px solid #eee;
            border-top-right-radius: 8px;
            border-bottom-right-radius: 8px;
        }

        .rank-1 {
            background: linear-gradient(145deg, #ffd700, #ffc800) !important;
            color: #2C3E50;
            font-weight: bold;
        }

        .rank-2 {
            background: linear-gradient(145deg, #C0C0C0, #B8B8B8) !important;
            color: #2C3E50;
            font-weight: bold;
        }

        .rank-3 {
            background: linear-gradient(145deg, #CD7F32, #C77730) !important;
            color: white;
            font-weight: bold;
        }

        .form-container {
            margin: 2rem auto;
            max-width: 600px;
            display: flex;
            gap: 1rem;
            padding: 1.5rem;
            background: white;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.05);
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

        @media (max-width: 768px) {
            .leaderboard-container {
                margin: 1rem;
                padding: 1rem;
            }

            .form-container {
                flex-direction: column;
            }

            .leaderboard-table {
                font-size: 0.9rem;
            }
        }
    </style>

    <h1 class="leaderboard-title">Scribble Masters</h1>
    <table class="leaderboard-table">
        <thead>
            <tr>
                <th>Rank</th>
                <th>Artist</th>
                <th>Score</th>
            </tr>
        </thead>
        <tbody id="leaderboard">
            <!-- Rows will be dynamically inserted here -->
        </tbody>
    </table>
    <div class="form-container">
        <input type="text" id="name" placeholder="Enter artist name" class="form-input">
        <input type="number" id="score" placeholder="Enter score" class="form-input">
        <button id="addButton" class="submit-button">Add Score</button>
    </div>
</div>

<script>
    let leaderboard = [];

    async function fetchLeaderboard() {
        try {
            const response = await fetch('http://localhost:5001/api/leaderboard');
            if (!response.ok) {
                throw new Error('Network response was not ok');
            }
            const data = await response.json();
            leaderboard = data.map(item => ({
                name: `${item.profile_name} - ${item.drawing_name}`,
                score: item.score
            }));
            renderLeaderboard();
        } catch (error) {
            console.error('Error fetching leaderboard:', error);
            document.getElementById('leaderboard').innerHTML = 
                '<tr><td colspan="3">Error loading leaderboard. Please try again later.</td></tr>';
        }
    }

    function renderLeaderboard() {
        const tbody = document.getElementById('leaderboard');
        tbody.innerHTML = "";
        
        if (leaderboard.length === 0) {
            tbody.innerHTML = '<tr><td colspan="3">No entries yet</td></tr>';
            return;
        }

        leaderboard.forEach((entry, index) => {
            const row = document.createElement('tr');
            const rankClass = index < 3 ? `rank-${index + 1}` : '';
            
            row.innerHTML = `
                <td class="${rankClass}">${index + 1}</td>
                <td class="${rankClass}">${entry.name}</td>
                <td class="${rankClass}">${entry.score}</td>
            `;
            tbody.appendChild(row);
        });
    }

    async function addEntry() {
        const nameInput = document.getElementById('name');
        const scoreInput = document.getElementById('score');
        const name = nameInput.value.trim();
        const score = parseInt(scoreInput.value);
        
        if (!name) {
            alert('Please enter a name');
            return;
        }
        
        if (isNaN(score) || score < 0 || score > 100) {
            alert('Please enter a valid score between 0 and 100');
            return;
        }

        try {
            const response = await fetch('http://localhost:5001/api/leaderboard', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ name, score })
            });

            if (!response.ok) {
                const errorData = await response.json();
                throw new Error(errorData.error || 'Failed to add entry');
            }

            await fetchLeaderboard();
            nameInput.value = "";
            scoreInput.value = "";
        } catch (error) {
            console.error('Error adding entry:', error);
            alert(error.message || 'Failed to add entry to leaderboard');
        }
    }

    document.getElementById('addButton').addEventListener('click', addEntry);
    document.getElementById('score').addEventListener('keypress', function(e) {
        if (e.key === 'Enter') {
            addEntry();
        }
    });
    
    fetchLeaderboard();
</script>



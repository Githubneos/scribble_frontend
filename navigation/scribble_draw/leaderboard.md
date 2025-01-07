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
        body {
            font-family: 'Poppins', sans-serif;
            margin: 0;
            padding: 0;
            background: #121212;
            color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .leaderboard-container {
            width: 90%;
            max-width: 1000px;
            background: linear-gradient(145deg, #1e1e1e, #2a2a2a);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 0 50px rgba(0,0,0,0.7);
            text-align: center;
        }
        h1 {
            font-size: 3rem;
        }
        table {
            width: 100%;
            margin-top: 30px;
            border-collapse: collapse;
            border-radius: 15px;
            overflow: hidden;
        }
        th, td {
            padding: 15px;
            text-align: center;
            border-bottom: 1px solid #ddd;
        }
        th {
            background: #00ffcc;
            color: #121212;
            font-size: 1.2rem;
        }
        tr:nth-child(even) {
            background: #2d2d2d;
        }
        tr:hover {
            background: #00ffcc;
            color: #121212;
            transition: 0.3s;
        }
        .form-container {
            margin-top: 30px;
            display: flex;
            justify-content: center;
            gap: 15px;
        }
        input, button {
            padding: 15px;
            border-radius: 10px;
            border: none;
            font-size: 1rem;
        }
        button {
            background: #00ffcc;
            cursor: pointer;
            color: #121212;
        }
        button:hover {
            background: #ffcc00;
            transition: 0.3s;
        }
    </style>
    <div class="leaderboard-container">
        <h1>🏆 Leaderboard 🏆</h1>
        <table>
            <thead>
                <tr>
                    <th>Rank</th>
                    <th>Name</th>
                    <th>Score</th>
                </tr>
            </thead>
            <tbody id="leaderboard">
                <!-- Leaderboard rows will be populated here -->
            </tbody>
        </table>
        <div class="form-container">
            <input type="text" id="name" placeholder="Enter Name">
            <input type="number" id="score" placeholder="Enter Score">
            <button id="addButton">Add Entry</button>
        </div>
    </div>
    <script>
        const leaderboard = [
            { name: "Alice", score: 150 },
            { name: "Bob", score: 200 },
            { name: "Charlie", score: 100 }
        ];
        function renderLeaderboard() {
            const tbody = document.getElementById('leaderboard');
            tbody.innerHTML = "";
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
        function addEntry() {
            const nameInput = document.getElementById('name').value.trim();
            const scoreInput = parseInt(document.getElementById('score').value);
            if (nameInput && !isNaN(scoreInput)) {
                leaderboard.push({ name: nameInput, score: scoreInput });
                renderLeaderboard();
            } else {
                alert('Please enter a valid name and score!');
            }
        }
        document.getElementById('addButton').addEventListener('click', addEntry);
        renderLeaderboard();
    </script>
</div>

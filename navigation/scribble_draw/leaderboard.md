---
layout: post
title: Leaderboard
search_exclude: true
description: Leaderboard
permalink: /leaderboard
menu: /nav/scribble_draw
Author: Max
---

<div>
    <style>
        div {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f4f4f9;
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
            background-color: #f1f1f1;
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
            background-color: #45a049;
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
    const leaderboard = [
        { name: "Alice", score: 150 },
        { name: "Bob", score: 200 },
        { name: "Charlie", score: 100 }
    ];

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

    function addEntry() {
        const nameInput = document.getElementById('name');
        const scoreInput = document.getElementById('score');
        const name = nameInput.value.trim();
        const score = parseInt(scoreInput.value);

        if (name && !isNaN(score)) {
            leaderboard.push({ name, score });
            renderLeaderboard();

            // Clear input fields
            nameInput.value = "";
            scoreInput.value = "";
        } else {
            alert('Please enter both a name and a valid score.');
        }
    }

    document.getElementById('addButton').addEventListener('click', addEntry);

    // Initial render
    renderLeaderboard();
</script>

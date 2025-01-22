---
layout: post
title: Leaderboard
search_exclude: true
description: Leaderboard
permalink: /leaderboard
Author: Daksha
---

<div class="leaderboard-container">
    <!-- Existing style block remains unchanged -->
    <style>
        // ...existing styles...
    </style>

    <h1 class="leaderboard-title">Scribble Masters</h1>
    
    <div class="form-container">
        <div class="input-group">
            <input type="text" id="profileName" placeholder="Profile Name" class="form-input" required>
            <input type="text" id="drawingName" placeholder="Drawing Name" class="form-input" required>
            <input type="number" id="score" placeholder="Score (0-100)" class="form-input" min="0" max="100" required>
            <button onclick="submitScore()" class="submit-button">Submit Score</button>
        </div>
        <div id="message" style="text-align: center; color: #2C3E50; margin-top: 10px;"></div>
    </div>

    <table class="leaderboard-table">
        <thead>
            <tr>
                <th>Rank</th>
                <th>Player</th>
                <th>Drawing</th>
                <th>Score</th>
            </tr>
        </thead>
        <tbody id="leaderboard"></tbody>
    </table>
</div>

<script>
const API_URL = 'http://127.0.0.1:8887/api/leaderboard';

function showMessage(message, isError = false) {
    const messageEl = document.getElementById('message');
    messageEl.style.color = isError ? '#e74c3c' : '#2ecc71';
    messageEl.textContent = message;
    setTimeout(() => messageEl.textContent = '', 3000);
}

async function fetchLeaderboard() {
    try {
        const response = await fetch(API_URL);
        if (!response.ok) throw new Error('Failed to fetch leaderboard');
        const data = await response.json();
        displayLeaderboard(data);
    } catch (error) {
        console.error('Error:', error);
        document.getElementById('leaderboard').innerHTML = 
            '<tr><td colspan="4" style="text-align: center;">Error loading leaderboard</td></tr>';
    }
}

function displayLeaderboard(data) {
    const tbody = document.getElementById('leaderboard');
    tbody.innerHTML = '';

    if (!data || data.length === 0) {
        tbody.innerHTML = '<tr><td colspan="4" style="text-align: center;">No entries yet</td></tr>';
        return;
    }

    data.sort((a, b) => b.score - a.score)
        .forEach((entry, index) => {
            const row = document.createElement('tr');
            row.innerHTML = `
                <td>${index + 1}</td>
                <td>${entry.profile_name}</td>
                <td>${entry.drawing_name}</td>
                <td>${entry.score}</td>
            `;
            tbody.appendChild(row);
        });
}

async function submitScore() {
    const profileName = document.getElementById('profileName').value.trim();
    const drawingName = document.getElementById('drawingName').value.trim();
    const score = parseInt(document.getElementById('score').value);

    if (!profileName || !drawingName) {
        showMessage('Please fill in all fields', true);
        return;
    }

    if (isNaN(score) || score < 0 || score > 100) {
        showMessage('Please enter a valid score between 0 and 100', true);
        return;
    }

    try {
        const response = await fetch(API_URL, {
            method: 'PUT',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({
                profile_name: profileName,
                drawing_name: drawingName,
                score: score
            })
        });

        const data = await response.json();

        if (response.ok) {
            showMessage(data.message);
            document.getElementById('profileName').value = '';
            document.getElementById('drawingName').value = '';
            document.getElementById('score').value = '';
            await fetchLeaderboard();
        } else {
            throw new Error(data.error || 'Failed to submit score');
        }
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, true);
    }
}

// Initialize
fetchLeaderboard();
setInterval(fetchLeaderboard, 30000);
</script>
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
            background: linear-gradient(135deg, #e0f7fa, #80deea);
            background-attachment: fixed;
        }

        .leaderboard-container {
            font-family: 'Poppins', Arial, sans-serif;
            max-width: 800px;
            margin: 2rem auto;
            padding: 2rem;
            background: rgba(255, 255, 255, 0.9);
            border-radius: 20px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.2);
        }

        .leaderboard-title {
            color: #000000;
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

        .submit-button, .delete-btn {
            padding: 12px 25px;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }

        .submit-button {
            background: #2C3E50;
        }

        .submit-button:hover {
            background: #34495E;
        }

        .delete-btn {
            background: #e74c3c;
            padding: 5px 10px;
            font-size: 0.9rem;
        }

        .delete-btn:hover {
            background: #c0392b;
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
            color: #000000;
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
                <th>Actions</th>
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
            '<tr><td colspan="5" style="text-align: center;">Error loading leaderboard</td></tr>';
    }
}

function displayLeaderboard(data) {
    const tbody = document.getElementById('leaderboard');
    tbody.innerHTML = '';

    if (!data || data.length === 0) {
        tbody.innerHTML = '<tr><td colspan="5" style="text-align: center;">No entries yet</td></tr>';
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
                <td>
                    <button onclick="deleteEntry('${entry.profile_name}', '${entry.drawing_name}')" 
                            class="delete-btn">Delete</button>
                </td>
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

async function deleteEntry(profileName, drawingName) {
    if (!confirm('Are you sure you want to delete this entry?')) return;
    
    try {
        const response = await fetch(`${API_URL}/${encodeURIComponent(profileName)}/${encodeURIComponent(drawingName)}`, {
            method: 'DELETE'
        });

        const data = await response.json();
        
        if (response.ok) {
            showMessage('Entry deleted successfully');
            await fetchLeaderboard();
        } else {
            throw new Error(data.error || 'Failed to delete entry');
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
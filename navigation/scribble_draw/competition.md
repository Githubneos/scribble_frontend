---
layout: post
title: Competition
permalink: /competition
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
        <td><a href="{{site.baseurl}}/posts">Posts</a></td>
    </tr>
</table>

<div class="game-container">
    <style>
        .game-container {
            max-width: 1000px;
            margin: 0 auto;
            padding: 20px;
        }
        canvas {
            background: white;
            border: 2px solid #333;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        .controls {
            margin: 20px 0;
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            justify-content: center;
        }
        .button {
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            background: #4CAF50;
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        .button:hover {
            background: #45a049;
        }
        .button:disabled {
            background: #cccccc;
            cursor: not-allowed;
        }
        .timer {
            font-size: 2em;
            margin: 20px 0;
            text-align: center;
        }
        .message {
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            display: none;
        }
        .success { background: #dff0d8; }
        .error { background: #f2dede; }
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
        }
        th, td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }
        th {
            background: #4CAF50;
            color: white;
        }
    </style>

    <h1>🎨 Competitive Drawing</h1>
    
    <div id="message" class="message"></div>
    
    <canvas id="drawingCanvas" width="800" height="500"></canvas>
    
    <div class="controls">
        <input type="color" id="colorPicker" value="#000000">
        <input type="range" id="brushSize" min="1" max="20" value="5">
        <button class="button" id="clearButton">Clear Canvas</button>
        <input type="number" id="timerInput" placeholder="Time in seconds" min="1">
        <button class="button" id="startTimer">Start Timer</button>
        <button class="button" id="submitDrawing">Submit Drawing</button>
    </div>

    <div id="timer" class="timer">Time: Not Started</div>

    <table>
        <thead>
            <tr>
                <th>Username</th>
                <th>Time</th>
                <th>Drawings</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody id="entriesTable"></tbody>
    </table>
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

function showMessage(text, type) {
    const msgEl = document.getElementById('message');
    msgEl.textContent = text;
    msgEl.className = `message ${type}`;
    msgEl.style.display = 'block';
    setTimeout(() => msgEl.style.display = 'none', 3000);
}

let isDrawing = false;
const canvas = document.getElementById('drawingCanvas');
const ctx = canvas.getContext('2d');

// Drawing functions
canvas.addEventListener('mousedown', startDrawing);
canvas.addEventListener('mousemove', draw);
canvas.addEventListener('mouseup', stopDrawing);
canvas.addEventListener('mouseout', stopDrawing);

function startDrawing(e) {
    isDrawing = true;
    draw(e);
}

function draw(e) {
    if (!isDrawing) return;
    const rect = canvas.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    
    ctx.lineWidth = document.getElementById('brushSize').value;
    ctx.strokeStyle = document.getElementById('colorPicker').value;
    ctx.lineCap = 'round';
    ctx.lineTo(x, y);
    ctx.stroke();
    ctx.beginPath();
    ctx.moveTo(x, y);
}

function stopDrawing() {
    isDrawing = false;
    ctx.beginPath();
}

// Timer functionality
document.getElementById('startTimer').addEventListener('click', async () => {
    if (!await checkAuth()) return;

    const duration = parseInt(document.getElementById('timerInput').value);
    if (!duration || duration <= 0) {
        showMessage('Please enter a valid duration', 'error');
        return;
    }

    try {
        const token = localStorage.getItem('token');
        const response = await fetch(`${API_URL}/competition/timer`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${token}`
            },
            body: JSON.stringify({ duration })
        });

        if (!response.ok) throw new Error('Failed to start timer');
        showMessage('Timer started!', 'success');
    } catch (error) {
        showMessage(error.message, 'error');
    }
});

// Submit drawing
document.getElementById('submitDrawing').addEventListener('click', async () => {
    if (!await checkAuth()) return;

    const username = prompt('Enter your username:');
    if (!username) return;

    try {
        const token = localStorage.getItem('token');
        const response = await fetch(`${API_URL}/competition`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${token}`
            },
            body: JSON.stringify({
                users_name: username,
                timer: document.getElementById('timer').textContent,
                amount_drawn: 1
            })
        });

        if (!response.ok) throw new Error('Failed to submit drawing');
        showMessage('Drawing submitted successfully!', 'success');
        fetchEntries();
    } catch (error) {
        showMessage(error.message, 'error');
    }
});

async function fetchEntries() {
    if (!await checkAuth()) return;

    try {
        const token = localStorage.getItem('token');
        const response = await fetch(`${API_URL}/competition/all`, {
            headers: {
                'Authorization': `Bearer ${token}`
            }
        });

        if (!response.ok) throw new Error('Failed to fetch entries');
        
        const entries = await response.json();
        updateTable(entries);
    } catch (error) {
        showMessage(error.message, 'error');
    }
}

function updateTable(entries) {
    const tbody = document.getElementById('entriesTable');
    tbody.innerHTML = '';

    entries.forEach(entry => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>${entry.users_name}</td>
            <td>${entry.timer}</td>
            <td>${entry.amount_drawn}</td>
            <td>
                <button onclick="deleteEntry(${entry.id})" class="button">Delete</button>
            </td>
        `;
        tbody.appendChild(row);
    });
}

async function deleteEntry(id) {
    if (!await checkAuth()) return;
    if (!confirm('Are you sure you want to delete this entry?')) return;

    try {
        const token = localStorage.getItem('token');
        const response = await fetch(`${API_URL}/competition`, {
            method: 'DELETE',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${token}`
            },
            body: JSON.stringify({ id })
        });

        if (!response.ok) throw new Error('Failed to delete entry');
        showMessage('Entry deleted successfully', 'success');
        fetchEntries();
    } catch (error) {
        showMessage(error.message, 'error');
    }
}

// Initialize
if (checkAuth()) {
    fetchEntries();
}

// Poll timer status
setInterval(async () => {
    if (!await checkAuth()) return;
    
    try {
        const token = localStorage.getItem('token');
        const response = await fetch(`${API_URL}/competition/timer`, {
            headers: {
                'Authorization': `Bearer ${token}`
            }
        });
        
        if (!response.ok) throw new Error('Failed to fetch timer status');
        
        const status = await response.json();
        document.getElementById('timer').textContent = 
            status.is_active ? `Time: ${status.time_remaining}s` : 'Time: Not Started';
    } catch (error) {
        console.error('Timer status error:', error);
    }
}, 1000);
</script>
---
layout: needsAuth
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

<style>
    body {
        background: linear-gradient(145deg, #BAD8B6, #E1EACD, #F9F6E6, #8D77AB);
    }
    .game-container {
        max-width: 1000px;
        margin: 0 auto;
        padding: 20px;
    }
    .drawing-section {
        text-align: center;
        margin-bottom: 20px;
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
        color: #333;
    }
    .word-display {
        font-size: 1.5em;
        margin: 10px 0;
        color: #2c3e50;
        font-weight: bold;
    }
    .message {
        padding: 10px;
        margin: 10px 0;
        border-radius: 5px;
        display: none;
        text-align: center;
    }
    .entries-table {
        width: 100%;
        border-collapse: collapse;
        margin: 20px 0;
        background: rgba(255, 255, 255, 0.9);
    }
    .entries-table th, 
    .entries-table td {
        padding: 12px;
        text-align: left;
        border-bottom: 1px solid #ddd;
    }
    .entries-table th {
        background: #4CAF50;
        color: white;
    }
</style>

<div class="game-container">
    <div class="drawing-section">
        <h1>🎨 Competitive Drawing</h1>
        <div id="message" class="message"></div>
        <div id="word-display" class="word-display">Word to draw: Waiting for timer...</div>
        <div id="timer" class="timer">Time: Not Started</div>
        
        <canvas id="drawingCanvas" width="800" height="500"></canvas>
        
        <div class="controls">
            <input type="color" id="colorPicker" value="#000000">
            <input type="range" id="brushSize" min="1" max="20" value="5">
            <button class="button" id="clearButton">Clear Canvas</button>
            <input type="number" id="timerInput" placeholder="Time in seconds" min="1">
            <button class="button" id="startTimer">Start Timer</button>
            <button class="button" id="submitDrawing" disabled>Submit Drawing</button>
        </div>
    </div>

    <table class="entries-table">
        <thead>
            <tr>
                <th>Player</th>
                <th>Time Taken (seconds)</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody id="entriesTable"></tbody>
    </table>
</div>

<script type="module">
import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

const fetchConfig = {
    credentials: 'include',
    headers: {
        'Content-Type': 'application/json',
        'X-Origin': 'client'
    }
};

let currentWord = '';
let isDrawing = false;
const canvas = document.getElementById('drawingCanvas');
const ctx = canvas.getContext('2d');

// Drawing functions - Define these before adding event listeners
function startDrawing(e) {
    isDrawing = true;
    const rect = canvas.getBoundingClientRect();
    ctx.beginPath();
    ctx.moveTo(e.clientX - rect.left, e.clientY - rect.top);
}

function draw(e) {
    if (!isDrawing) return;
    const rect = canvas.getBoundingClientRect();
    ctx.lineTo(e.clientX - rect.left, e.clientY - rect.top);
    ctx.strokeStyle = document.getElementById('colorPicker').value;
    ctx.lineWidth = document.getElementById('brushSize').value;
    ctx.lineCap = 'round';
    ctx.stroke();
}

function stopDrawing() {
    isDrawing = false;
    ctx.closePath();
}

// Add event listeners after functions are defined
canvas.addEventListener('mousedown', startDrawing);
canvas.addEventListener('mousemove', draw);
canvas.addEventListener('mouseup', stopDrawing);
canvas.addEventListener('mouseout', stopDrawing);

// Clear canvas function
document.getElementById('clearButton').addEventListener('click', () => {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
});

// Timer functionality
let timerInterval;

document.getElementById('startTimer').addEventListener('click', async () => {
    const duration = parseInt(document.getElementById('timerInput').value);
    if (!duration || duration <= 0) {
        showMessage('Please enter a valid duration', 'error');
        return;
    }

    try {
        const response = await fetch(`${pythonURI}/api/competition/timer`, {
            ...fetchConfig,
            method: 'POST',
            body: JSON.stringify({ duration })
        });

        const data = await response.json();
        if (!response.ok) throw new Error(data.message || 'Failed to start timer');

        currentWord = data.word;
        document.getElementById('word-display').textContent = `Word to draw: ${currentWord}`;
        showMessage(`Timer started! Draw: ${currentWord}`, 'success');
        document.getElementById('submitDrawing').disabled = false;
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
});

// Update the submit drawing event listener
document.getElementById('submitDrawing').addEventListener('click', async () => {
    try {
        // Get current timer value
        const timerText = document.getElementById('timer').textContent;
        const timeMatch = timerText.match(/\d+/);
        
        if (!timeMatch) {
            showMessage('Invalid timer value', 'error');
            return;
        }

        // Save the drawing as JPEG
        const imageData = canvas.toDataURL('image/jpeg');
        const link = document.createElement('a');
        link.download = `drawing_${Date.now()}.jpg`;
        link.href = imageData;
        link.click();

        // Create payload matching the API requirements
        const payload = {
            time_taken: parseInt(timeMatch[0])
        };

        const response = await fetch(`${pythonURI}/api/competition`, {
            method: 'POST',
            credentials: 'include',
            headers: {
                'Content-Type': 'application/json',
                'X-Origin': 'client'
            },
            body: JSON.stringify(payload)
        });

        const data = await response.json();
        if (!response.ok) {
            throw new Error(data.message || 'Failed to save time');
        }

        showMessage('Drawing saved and time recorded!', 'success');
        
        // Reset game state
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        document.getElementById('submitDrawing').disabled = true;
        
        // Refresh the entries table
        await fetchEntries();
    } catch (error) {
        console.error('Submission error:', error);
        showMessage(error.message, 'error');
    }
});

async function fetchEntries() {
    try {
        const response = await fetch(`${pythonURI}/api/competition`, {
            method: 'GET'
        });

        if (!response.ok) throw new Error('Failed to fetch entries');
        const entries = await response.json();
        updateTable(entries);
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
}

// Update the table update function
function updateTable(entries) {
    const tbody = document.getElementById('entriesTable');
    tbody.innerHTML = '';

    entries.forEach(entry => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>${entry.users_name}</td>
            <td>${entry.time_taken}</td>
            <td>${entry.created_by ? 
                `<button onclick="deleteEntry(${entry.id})" class="button">Delete</button>` 
                : ''}</td>
        `;
        tbody.appendChild(row);
    });
}

window.deleteEntry = async function(id) {
    if (!confirm('Are you sure you want to delete this entry?')) return;

    try {
        const response = await fetch(`${pythonURI}/api/competition`, {
            ...fetchConfig,
            method: 'DELETE',
            body: JSON.stringify({ id })
        });

        const data = await response.json();
        if (!response.ok) throw new Error(data.message || 'Failed to delete entry');

        showMessage('Entry deleted successfully', 'success');
        await fetchEntries();
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
};

function showMessage(text, type) {
    const msgEl = document.getElementById('message');
    msgEl.textContent = text;
    msgEl.className = `message ${type}`;
    msgEl.style.display = 'block';
    msgEl.style.backgroundColor = type === 'error' ? '#fee2e2' : '#dcfce7';
    msgEl.style.color = type === 'error' ? '#ef4444' : '#10b981';
    setTimeout(() => msgEl.style.display = 'none', 3000);
}

// Update timer status
setInterval(async () => {
    try {
        const response = await fetch(`${pythonURI}/api/competition/timer`, {
            ...fetchConfig,
            method: 'GET'
        });
        
        if (!response.ok) throw new Error('Failed to fetch timer status');
        const status = await response.json();
        
        document.getElementById('timer').textContent = 
            status.is_active ? `Time: ${status.time_remaining}s` : 'Time: Not Started';
        
        if (status.is_active && status.time_remaining === 0) {
            showMessage('Time\'s up!', 'error');
        }
    } catch (error) {
        console.error('Timer status error:', error);
    }
}, 1000);

// Initialize
document.addEventListener('DOMContentLoaded', fetchEntries);
</script>
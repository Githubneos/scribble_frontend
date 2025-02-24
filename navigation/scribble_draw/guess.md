---
layout: needsAuth
title: Picture Guessing Game
permalink: /guess
menu: nav/home.html
search_exclude: true
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
        background: linear-gradient(145deg, #F7CFD8, #F4F8D3, #A6F1E0, #73C7C7);
    }
    .game-container {
        max-width: 800px;
        margin: 0 auto;
        padding: 20px;
    }
    .word-card {
        background: #2C3E50;
        padding: 20px;
        border-radius: 10px;
        margin-bottom: 20px;
        color: white;
    }
    .hint-section {
        margin: 20px 0;
        padding: 15px;
        background: #34495E;
        border-radius: 8px;
    }
    .input-field {
        padding: 8px;
        border-radius: 4px;
        border: 1px solid #34495E;
        width: 200px;
    }
    .button {
        padding: 8px 20px;
        background: #3498DB;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        margin: 5px;
    }
    .button:hover {
        background: #2980B9;
    }
    .stats-container {
        margin-top: 20px;
        padding: 20px;
        background: #34495E;
        border-radius: 10px;
        color: white;
    }
    .image-container {
        margin: 20px 0;
    }
    #guess-canvas {
        border: 2px solid #34495E;
        border-radius: 8px;
    }
</style>

<div class="game-container">
    <h2>Picture Guessing Game</h2>
    <div id="image-container">
        <canvas id="guess-canvas" width="400" height="400"></canvas>
    </div>
    <div class="hint-section">
        <h3>Hints:</h3>
        <div id="hint-list"></div>
        <button class="button" id="hint-button">Get Next Hint</button>
    </div>
    <form id="guess-form">
        <input type="text" id="guess-input" class="input-field" placeholder="Enter your guess" required>
        <button type="submit" class="button">Submit Guess</button>
    </form>
    <p id="message-container"></p>
    <div class="stats-container">
        <h3>Your Guess Statistics</h3>
        <table id="stats-table" border="1">
            <thead>
                <tr>
                    <th>User</th>
                    <th>Your Guess</th>
                    <th>Correct?</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody id="stats-body">
                <tr><td colspan="4">Loading stats...</td></tr>
            </tbody>
        </table>
    </div>
</div>

<script type="module">
    const API_BASE = "https://scribble.stu.nighthawkcodingsociety.com/api";

    let currentImageIndex = 0;
    let hintsUsed = 0;
    let currentHintIndex = 0;

    const images = [
        {
            label: "car",
            drawing: function(ctx) {
                ctx.fillStyle = "#3498db";
                ctx.fillRect(100, 150, 200, 50);
                ctx.fillStyle = "#2ecc71";
                ctx.beginPath();
                ctx.arc(130, 200, 20, 0, Math.PI * 2);
                ctx.arc(270, 200, 20, 0, Math.PI * 2);
                ctx.fill();
            },
            hints: ["It has wheels", "Used for transportation", "Has engine"]
        },
        {
            label: "house",
            drawing: function(ctx) {
                ctx.fillStyle = "#e74c3c";
                ctx.fillRect(100, 100, 200, 150);
                ctx.fillStyle = "#f39c12";
                ctx.beginPath();
                ctx.moveTo(100, 100);
                ctx.lineTo(200, 50);
                ctx.lineTo(300, 100);
                ctx.fill();
            },
            hints: ["People live in it", "Has a roof", "Home"]
        }
    ];

    function loadNewImage() {
        const image = images[currentImageIndex];
        const canvas = document.getElementById('guess-canvas');
        const ctx = canvas.getContext('2d');
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        image.drawing(ctx);
        document.getElementById('hint-list').innerHTML = '';
        hintsUsed = 0;
        currentHintIndex = 0;
    }

    async function submitGuess(event) {
        event.preventDefault();
        const guess = document.getElementById('guess-input').value;
        const isCorrect = guess.toLowerCase() === images[currentImageIndex].label.toLowerCase();

        try {
            const response = await fetch(`${API_BASE}/guess`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${localStorage.getItem('token')}`
                },
                body: JSON.stringify({ user_guess: guess, is_correct: isCorrect, hint_used: hintsUsed })
            });

            const result = await response.json();
            if (response.ok) {
                showMessage('Guess submitted successfully!', 'success');
                loadStats();
            } else {
                showMessage(result.message, 'error');
            }
        } catch (error) {
            showMessage('Error submitting guess', 'error');
        }
    }

    async function loadStats() {
        const statsBody = document.getElementById('stats-body');
        statsBody.innerHTML = '';

        try {
            const response = await fetch(`${API_BASE}/guess/stats`, {
                method: 'GET',
                headers: { 'Authorization': `Bearer ${localStorage.getItem('token')}` }
            });

            const data = await response.json();
            if (response.ok && data.recent_guesses.length > 0) {
                data.recent_guesses.forEach(stat => {
                    statsBody.innerHTML += `
                        <tr>
                            <td>${stat.guesser_name}</td>
                            <td>${stat.user_guess}</td>
                            <td>${stat.is_correct ? '✅' : '❌'}</td>
                            <td><button onclick="deleteGuess(${stat.guess_id})">Delete</button></td>
                        </tr>`;
                });
            } else {
                statsBody.innerHTML = '<tr><td colspan="4">No guesses found</td></tr>';
            }
        } catch (error) {
            showMessage('Error loading stats', 'error');
        }
    }

    function showMessage(msg, type) {
        const msgBox = document.getElementById('message-container');
        msgBox.textContent = msg;
        msgBox.className = type;
    }
    function getNextHint() {
    const hintList = document.getElementById('hint-list');
    const image = images[currentImageIndex];

    if (currentHintIndex < image.hints.length) {
        const hint = document.createElement('p');
        hint.textContent = image.hints[currentHintIndex];
        hintList.appendChild(hint);
        hintsUsed++;
        currentHintIndex++;
    } else {
        showMessage('No more hints available!', 'error');
        document.getElementById('hint-button').disabled = true; // Disable button when no more hints
    }
}

    document.getElementById('guess-form').addEventListener('submit', submitGuess);
    document.getElementById('hint-button').addEventListener('click', () => getNextHint());
    loadNewImage();
    loadStats();
</script>

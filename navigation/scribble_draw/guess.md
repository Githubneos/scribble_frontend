---
layout: needsAuth
title: Word Guessing Game
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

    .guess-form {
        margin-top: 20px;
    }

    .input-field {
        padding: 8px;
        margin-right: 10px;
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

    .error {
        color: #E74C3C;
        margin-top: 10px;
    }

    .success {
        color: #2ECC71;
        margin-top: 10px;
    }
</style>

<div class="game-container">
    <!-- Picture Guessing Section -->
    <div class="image-card">
        <h2>Picture Guessing Game</h2>
        <div id="image-container">
            <img id="guess-image" src="" alt="Guess the image" width="300" height="300">
        </div>
        <div class="hint-section">
            <h3>Hints:</h3>
            <div id="hint-list"></div>
            <button class="button" id="hint-button">Get Next Hint</button>
        </div>
        <form class="guess-form" onsubmit="submitGuess(event)">
            <input type="text" id="guess-input" class="input-field" placeholder="Enter your guess" required>
            <button type="submit" class="button">Submit Guess</button>
        </form>
        <p id="result-message"></p>
    </div>

    <!-- Stats Table (with Update and Delete) -->
    <div class="stats-container">
        <h3>Your Guess Statistics</h3>
        <table id="stats-table" border="1">
            <thead>
                <tr>
                    <th>Guess ID</th>
                    <th>Image</th>
                    <th>Your Guess</th>
                    <th>Correct?</th>
                    <th>Hints Used</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody id="stats-body">
                <tr><td colspan="6">Loading stats...</td></tr>
            </tbody>
        </table>
    </div>
</div>

<script type="module">
import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

let currentImageIndex = 0;
let hintsUsed = 0;
let currentHintIndex = 0;

// Built-in images and hints
const images = [
    { label: "car", src: "assets/images/car.png", hints: ["It has wheels", "Used for transportation"] },
    { label: "house", src: "assets/images/house.png", hints: ["People live in it", "Has a roof"] },
    { label: "sun", src: "assets/images/sun.png", hints: ["It's bright", "Appears during the day"] },
    { label: "mountain", src: "assets/images/mountain.png", hints: ["It's tall", "Covered with snow"] },
    { label: "ocean", src: "assets/images/ocean.png", hints: ["It's large", "Salty water"] }
];

// Show a message
function showMessage(text, type) {
    const messageDiv = document.getElementById('result-message');
    messageDiv.textContent = text;
    messageDiv.className = `message ${type}`;
    setTimeout(() => messageDiv.textContent = '', 3000);
}

// Load a new image from built-in images
function loadNewImage() {
    const image = images[currentImageIndex];
    document.getElementById('guess-image').src = image.src;
    document.getElementById('hint-list').innerHTML = '';
    document.getElementById('guess-input').value = '';
    hintsUsed = 0;
    currentHintIndex = 0;
}

// Submit a guess to the stats table
async function submitGuess(event) {
    event.preventDefault();
    const guess = document.getElementById('guess-input').value;
    const image = images[currentImageIndex];

    try {
        const response = await fetch(`${pythonURI}/api/guess`, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({
                image_label: image.label,
                guess: guess,
                hints_used: hintsUsed
            })
        });

        if (!response.ok) throw new Error('Failed to store guess');
        const result = await response.json();

        if (result.correct) {
            showMessage('Correct! Loading next image...', 'success');
            await loadStats();
            currentImageIndex = (currentImageIndex + 1) % images.length;
            setTimeout(loadNewImage, 1500);
        } else {
            showMessage('Incorrect, try again!', 'error');
        }
    } catch (error) {
        console.error('Error:', error);
        showMessage('Error submitting guess', 'error');
    }
}

// Load guess statistics from backend
async function loadStats() {
    try {
        const response = await fetch(`${pythonURI}/api/guess/stats`, {
            method: 'GET',
            headers: { 'Content-Type': 'application/json' }
        });
        if (!response.ok) throw new Error('Failed to fetch stats');

        const stats = await response.json();
        const tbody = document.getElementById('stats-body');
        tbody.innerHTML = '';

        stats.forEach(stat => {
            const row = document.createElement('tr');
            row.innerHTML = `
                <td>${stat.guess_id}</td>
                <td><img src="${images.find(img => img.label === stat.image_label)?.src}" alt="${stat.image_label}" width="50"></td>
                <td><input type="text" value="${stat.user_guess}" id="guess-${stat.guess_id}"></td>
                <td>${stat.correct ? '✅' : '❌'}</td>
                <td>${stat.hints_used}</td>
                <td>
                    <button onclick="updateGuess(${stat.guess_id})">Update</button>
                    <button onclick="deleteGuess(${stat.guess_id})">Delete</button>
                </td>
            `;
            tbody.appendChild(row);
        });
    } catch (error) {
        console.error('Error:', error);
        document.getElementById('stats-body').innerHTML = '<tr><td colspan="6">Failed to load stats</td></tr>';
    }
}

// Update a guess
async function updateGuess(guessId) {
    const newGuess = document.getElementById(`guess-${guessId}`).value;

    try {
        const response = await fetch(`${pythonURI}/api/guess/${guessId}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ guess: newGuess })
        });

        if (!response.ok) throw new Error('Update failed');
        showMessage('Guess updated!', 'success');
        await loadStats();
    } catch (error) {
        console.error('Error:', error);
        showMessage('Failed to update guess', 'error');
    }
}

// Delete a guess
async function deleteGuess(guessId) {
    try {
        const response = await fetch(`${pythonURI}/api/guess/${guessId}`, {
            method: 'DELETE',
            headers: { 'Content-Type': 'application/json' }
        });

        if (!response.ok) throw new Error('Delete failed');
        showMessage('Guess deleted!', 'success');
        await loadStats();
    } catch (error) {
        console.error('Error:', error);
        showMessage('Failed to delete guess', 'error');
    }
}

// Get next hint from built-in hints
function getNextHint() {
    const image = images[currentImageIndex];
    if (currentHintIndex < image.hints.length) {
        const hint = document.createElement('p');
        hint.textContent = image.hints[currentHintIndex];
        document.getElementById('hint-list').appendChild(hint);
        hintsUsed++;
        currentHintIndex++;
    } else {
        showMessage('No more hints available!', 'error');
        document.getElementById('hint-button').disabled = true;
    }
}

// Initial load
document.addEventListener('DOMContentLoaded', () => {
    loadNewImage();
    loadStats();
    document.getElementById('hint-button').addEventListener('click', getNextHint);
});
</script>

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

let currentImageIndex = 0;
let hintsUsed = 0;
let currentHintIndex = 0;

// Custom images data (labels, hints, and basic drawings for simplicity)
const images = [
    {
        label: "car",
        drawing: function(ctx) {
            // Drawing a basic representation of a car on canvas
            ctx.fillStyle = "#3498db";
            ctx.fillRect(100, 150, 200, 50); // Body of the car
            ctx.fillStyle = "#2ecc71";
            ctx.beginPath();
            ctx.arc(130, 200, 20, 0, Math.PI * 2); // Left wheel
            ctx.arc(270, 200, 20, 0, Math.PI * 2); // Right wheel
            ctx.fill();
        },
        hints: ["It has wheels", "Used for transportation", "Has engine"]
    },
    {
        label: "house",
        drawing: function(ctx) {
            // Drawing a basic house
            ctx.fillStyle = "#e74c3c";
            ctx.fillRect(100, 100, 200, 150); // House body
            ctx.fillStyle = "#f39c12";
            ctx.beginPath();
            ctx.moveTo(100, 100);
            ctx.lineTo(200, 50); // Roof
            ctx.lineTo(300, 100);
            ctx.fill();
        },
        hints: ["People live in it", "Has a roof", "Home"]
    },
    {
        label: "sun",
        drawing: function(ctx) {
            // Drawing a simple sun
            ctx.fillStyle = "#f1c40f";
            ctx.beginPath();
            ctx.arc(200, 200, 50, 0, Math.PI * 2); // Sun
            ctx.fill();
        },
        hints: ["It's bright", "Appears during the day", "Star of our solar system"]
    },
    {
        label: "mountain",
        drawing: function(ctx) {
            // Drawing a simple mountain
            ctx.fillStyle = "#2c3e50";
            ctx.beginPath();
            ctx.moveTo(100, 300);
            ctx.lineTo(200, 100); // Peak of the mountain
            ctx.lineTo(300, 300);
            ctx.closePath();
            ctx.fill();
        },
        hints: ["It's tall", "Covered with snow", "People hike on this"]
    },
    {
        label: "ocean",
        drawing: function(ctx) {
            // Drawing a simple ocean (blue rectangle)
            ctx.fillStyle = "#3498db";
            ctx.fillRect(50, 250, 300, 100);
        },
        hints: ["It's large", "Salty water", "People use boats on it"]
    }
];

// Show a message
function showMessage(text, type) {
    const messageDiv = document.getElementById('result-message');
    messageDiv.textContent = text;
    messageDiv.className = `message ${type}`;
    setTimeout(() => messageDiv.textContent = '', 3000);
}

// Load a new image (canvas drawing) from custom images
function loadNewImage() {
    const image = images[currentImageIndex];
    const canvas = document.getElementById('guess-canvas');
    const ctx = canvas.getContext('2d');
    canvas.width = 400;
    canvas.height = 400;
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    image.drawing(ctx);
    document.getElementById('guess-input').value = '';
    hintsUsed = 0;
    currentHintIndex = 0;
}

// Submit a guess to the stats table
async function submitGuess(event) {
    event.preventDefault();
    const guess = document.getElementById('guess-input').value;
    const image = images[currentImageIndex];

    const guesserName = "Anonymous";  // Replace with actual user name if applicable
    const isCorrect = checkIfCorrect(guess, image.label);  // Implement this function to check correctness

    try {
        // Logic for submitting the guess (to be adapted for frontend)
        showMessage(isCorrect ? 'Correct! Loading next image...' : 'Incorrect, try again!', isCorrect ? 'success' : 'error');
        await loadStats();
        currentImageIndex = (currentImageIndex + 1) % images.length;
        setTimeout(loadNewImage, 1500);
    } catch (error) {
        console.error('Error:', error);
        showMessage('Error submitting guess', 'error');
    }
}

// Load guess statistics (strictly frontend)
function loadStats() {
    const statsBody = document.getElementById('stats-body');
    statsBody.innerHTML = '';

    const stats = [
        { guesser_name: 'User1', user_guess: 'car', correct: true },
        { guesser_name: 'User2', user_guess: 'house', correct: false },
        { guesser_name: 'User3', user_guess: 'mountain', correct: true }
    ];

    stats.forEach(stat => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>${stat.guesser_name}</td>
            <td><input type="text" value="${stat.user_guess}" id="guess-${stat.guess_id}"></td>
            <td>${stat.correct ? '✅' : '❌'}</td>
            <td>
                <button onclick="updateGuess(${stat.guess_id})">Update</button>
                <button onclick="deleteGuess(${stat.guess_id})">Delete</button>
            </td>
        `;
        statsBody.appendChild(row);
    });
}

// Update a Guess using guess ID
async function updateGuess(guessId) {
    const newGuess = document.getElementById(`guess-${guessId}`).value;

    try {
        // Update logic here (not connected to backend in this version)
        showMessage('Guess updated!', 'success');
        await loadStats();
    } catch (error) {
        console.error('Error:', error);
        showMessage('Failed to update guess', 'error');
    }
}

// Delete a Guess using guess ID
async function deleteGuess(guessId) {
    try {
        // Delete logic here (not connected to backend in this version)
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

// Check if the guess is correct (simple comparison for demo purposes)
function checkIfCorrect(guess, correctLabel) {
    return guess.trim().toLowerCase() === correctLabel.toLowerCase();
}

// Initial load
document.addEventListener('DOMContentLoaded', () => {
    loadNewImage();
    loadStats();
    document.getElementById('hint-button').addEventListener('click', getNextHint);
});
</script>

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
    #guess-canvas {
        border: 2px solid #34495E;
        border-radius: 8px;
        margin-bottom: 20px;
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
    <button id="reset-button" class="button">Reset</button>
    <form id="guess-form">
        <input type="text" id="guess-input" class="input-field" placeholder="Enter your guess" required>
        <button type="submit" class="button">Submit Guess</button>
    </form>
    <p id="message-container"></p>
    <div class="stats-container">
        <h3>Past Guesses</h3>
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

<script>
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
        },
        {
            label: "sun",
            drawing: function(ctx) {
                ctx.fillStyle = "#f1c40f";
                ctx.beginPath();
                ctx.arc(200, 200, 50, 0, Math.PI * 2); // Sun
                ctx.fill();
            },
            hints: ["It's bright", "Appears during the day", "Star of our solar system"]
        },
        {
            label: "tree",
            drawing: function(ctx) {
                ctx.fillStyle = "#8B4513";
                ctx.fillRect(175, 180, 50, 100); // Trunk
                ctx.fillStyle = "#228B22";
                ctx.beginPath();
                ctx.arc(200, 150, 50, 0, Math.PI * 2); // Leaves
                ctx.fill();
            },
            hints: ["It has leaves", "Found in forests", "Grows tall"]
        }
    ];

    let currentImageIndex = 0;
    let hintsUsed = 0;
    let currentHintIndex = 0;

    function loadNewImage() {
        const image = images[currentImageIndex];
        const canvas = document.getElementById('guess-canvas');
        const ctx = canvas.getContext('2d');
        const hintList = document.getElementById('hint-list');
        
        // Clear previous image and hints
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        hintList.innerHTML = '';

        // Draw the new image
        image.drawing(ctx);

        // Reset hint and counter
        hintsUsed = 0;
        currentHintIndex = 0;
    }

    document.getElementById('reset-button').addEventListener('click', () => {
        currentImageIndex = Math.floor(Math.random() * images.length); // Randomize image
        loadNewImage();
    });

    document.getElementById('hint-button').addEventListener('click', () => {
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
    });
    const pythonURI = "https://scribble.stu.nighthawkcodingsociety.com/api/guess"; // Ensure the correct backend API URL

async function submitGuess(event) {
    event.preventDefault();
    const guess = document.getElementById('guess-input').value;

    try {
        const response = await fetch(pythonURI, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${localStorage.getItem('token')}`
            },
            body: JSON.stringify({ user_guess: guess })
        });

        const result = await response.json();
        if (response.ok) {
            showMessage('Guess submitted successfully!', 'success');
            loadStats(); // Refresh stats after submission
        } else {
            showMessage(result.message, 'error');
        }
    } catch (error) {
        showMessage('Error submitting guess', 'error');
    }
}

async function loadStats() {
    const statsBody = document.getElementById('stats-body');
    statsBody.innerHTML = ''; // Clear previous stats

    try {
        const response = await fetch(pythonURI, {
            method: 'GET',
            // Remove Authorization header to not use the token
            headers: {
                'Content-Type': 'application/json'
            }
        });

        const data = await response.json();
        if (response.ok && data.recent_guesses && data.recent_guesses.length > 0) {
            data.recent_guesses.forEach(stat => {
                statsBody.innerHTML += `
                    <tr>
                        <td>${stat.guesser_name}</td>
                        <td>${stat.user_guess}</td>
                        <td>${stat.is_correct ? '✅' : '❌'}</td>
                        <td>
                            <button onclick="deleteGuess(${stat.guess_id})">Delete</button>
                            <button onclick="updateGuess(${stat.guess_id}, '${stat.user_guess}')">Update</button>
                        </td>
                    </tr>`;
            });
        } else {
            statsBody.innerHTML = '<tr><td colspan="4">No guesses found</td></tr>';
        }
    } catch (error) {
        showMessage('Error loading stats', 'error');
    }
}


async function updateGuess(guessId, currentGuess) {
    const updatedGuess = prompt("Update your guess:", currentGuess);
    if (updatedGuess === null || updatedGuess === "") return;

    try {
        const response = await fetch(pythonURI, {
            method: 'PUT',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${localStorage.getItem('token')}`
            },
            body: JSON.stringify({ id: guessId, user_guess: updatedGuess })
        });

        const result = await response.json();
        if (response.ok) {
            showMessage('Guess updated successfully!', 'success');
            loadStats(); // Refresh the stats after update
        } else {
            showMessage(result.message, 'error');
        }
    } catch (error) {
        showMessage('Error updating guess', 'error');
    }
}

async function deleteGuess(guessId) {
    if (!confirm('Are you sure you want to delete this guess?')) return;

    try {
        const response = await fetch(pythonURI, {
            method: 'DELETE',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${localStorage.getItem('token')}`
            },
            body: JSON.stringify({ id: guessId })
        });

        const result = await response.json();
        if (response.ok) {
            showMessage('Guess deleted successfully!', 'success');
            loadStats(); // Refresh the stats after deletion
        } else {
            showMessage(result.message, 'error');
        }
    } catch (error) {
        showMessage('Error deleting guess', 'error');
    }
}

function showMessage(message, type) {
    const messageElement = document.createElement('div');
    messageElement.classList.add(type === 'success' ? 'alert-success' : 'alert-error');
    messageElement.textContent = message;
    document.body.appendChild(messageElement);
    setTimeout(() => {
        messageElement.remove();
    }, 3000);
}

    function showMessage(msg, type) {
        const msgBox = document.getElementById('message-container');
        msgBox.textContent = msg;
        msgBox.className = type;
    }
    document.getElementById('guess-form').addEventListener('submit', submitGuess);
    loadNewImage(); // Load the first image on page load
    loadStats(); // Load past guesses initially

    function showMessage(msg, type) {
        const msgBox = document.getElementById('message-container');
        msgBox.textContent = msg;
        msgBox.className = type;
    }

    document.getElementById('guess-form').addEventListener('submit', submitGuess);
    loadNewImage(); // Load the first image on page load
    loadStats(); // Load past guesses
</script>

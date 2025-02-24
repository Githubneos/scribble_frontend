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

    .image-container {
        margin: 20px 0;
    }

    #guess-canvas {
        border: 2px solid #34495E;
        border-radius: 8px;
    }
</style>

<div class="game-container">
    <!-- Picture Guessing Section -->
    <div class="image-card">
        <h2>Picture Guessing Game</h2>
        <div id="image-container">
            <!-- Add a canvas for drawing -->
            <canvas id="guess-canvas" width="300" height="300"></canvas>
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
                ctx.fillStyle = "#3498db";
                ctx.fillRect(50, 250, 300, 100); // Ocean
            },
            hints: ["It's large", "Salty water", "People use boats on it"]
        }
    ];

    function showMessage(text, type) {
        const messageDiv = document.getElementById('result-message');
        messageDiv.textContent = text;
        messageDiv.className = `message ${type}`;
        setTimeout(() => messageDiv.textContent = '', 3000);
    }

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

    document.addEventListener('DOMContentLoaded', function() {
    loadStats();  // Load stats on page load
});

// Submit a new guess
async function submitGuess(guess, isCorrect, hintsUsed, guesserName = "Anonymous") {
    try {
        const response = await fetch('https://scribble.stu.nighthawkcodingsociety.com/api/guess', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${localStorage.getItem('token')}`
            },
            body: JSON.stringify({
                user_guess: guess,
                is_correct: isCorrect,
                hint_used: hintsUsed,
                guesser_name: guesserName
            })
        });

        const result = await response.json();
        if (response.ok) {
            showMessage('Guess submitted successfully!', 'success');
            return result; 
        } else {
            showMessage(result.message, 'error');
            return null;
        }
    } catch (error) {
        console.error('Error submitting guess:', error);
        showMessage('Error submitting guess', 'error');
        return null;
    }
}


// Load and display stats
async function loadStats() {
    const statsBody = document.getElementById('stats-body');
    statsBody.innerHTML = '';  // Clear any existing rows
    try {
        const response = await fetch('https://scribble.stu.nighthawkcodingsociety.com/api/guess/stats', {
            method: 'GET',
            headers: {
                'Authorization': `Bearer ${localStorage.getItem('token')}`
            }
        });
        const data = await response.json();

        if (response.ok) {
            if (data.recent_guesses && data.recent_guesses.length === 0) {
                statsBody.innerHTML = '<tr><td colspan="4">No guesses found</td></tr>';
                return;
            }
            data.recent_guesses.forEach(stat => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${stat.guesser_name}</td>
                    <td><input type="text" value="${stat.user_guess}" id="guess-${stat.guess_id}"></td>
                    <td>${stat.is_correct ? '✅' : '❌'}</td>
                    <td>
                        <button onclick="updateGuess(${stat.guess_id})">Update</button>
                        <button onclick="deleteGuess(${stat.guess_id})">Delete</button>
                    </td>
                `;
                statsBody.appendChild(row);
            });
        } else {
            showMessage(data.message, 'error');
        }
    } catch (error) {
        console.error('Error loading stats:', error);
        showMessage('Error loading stats', 'error');
    }
}

// Update a guess
async function updateGuess(guessId) {
    const newGuess = document.getElementById(`guess-${guessId}`).value;
    try {
        const response = await fetch(`https://scribble.stu.nighthawkcodingsociety.com/api/guess/stats/${guessId}/update`, {
            method: 'PUT',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${localStorage.getItem('token')}`
            },
            body: JSON.stringify({ user_guess: newGuess })
        });
        const result = await response.json();
        if (response.ok) {
            showMessage(result.message, 'success');
            loadStats();  // Reload stats after updating a guess
        } else {
            showMessage(result.message, 'error');
        }
    } catch (error) {
        console.error('Error updating guess:', error);
        showMessage('Failed to update guess', 'error');
    }
}

// Delete a guess
async function deleteGuess(guessId) {
    try {
        const response = await fetch(`https://scribble.stu.nighthawkcodingsociety.com/api/guess/stats/${guessId}/delete`, {
            method: 'DELETE',
            headers: {
                'Authorization': `Bearer ${localStorage.getItem('token')}`
            }
        });
        const result = await response.json();
        if (response.ok) {
            showMessage(result.message, 'success');
            loadStats();  // Reload stats after deleting a guess
        } else {
            showMessage(result.message, 'error');
        }
    } catch (error) {
        console.error('Error deleting guess:', error);
        showMessage('Failed to delete guess', 'error');
    }
}

// Utility function to display messages
function showMessage(message, type) {
    const messageContainer = document.getElementById('message-container');
    messageContainer.innerHTML = message;
    messageContainer.className = type; // 'success' or 'error'
}
    function checkIfCorrect(guess, correctLabel) {
        return guess.trim().toLowerCase() === correctLabel.toLowerCase();
    }

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

    document.getElementById('hint-button').addEventListener('click', getNextHint);

    loadNewImage();  
    loadStats();
</script>


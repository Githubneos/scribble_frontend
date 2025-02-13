---
layout: post
title: Scribble Guess
permalink: /guess
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
    </tr>
</table>

<div class="game-container">
    <style>
        .game-container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        canvas {
            display: block;
            margin: 20px auto;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        .controls {
            text-align: center;
            margin: 20px 0;
        }
        button {
            padding: 10px 20px;
            margin: 0 5px;
            border: none;
            border-radius: 5px;
            background: #4CAF50;
            color: white;
            cursor: pointer;
        }
        button:hover {
            background: #45a049;
        }
        #guessForm {
            display: flex;
            gap: 10px;
            justify-content: center;
            margin: 20px 0;
        }
        input {
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        .message {
            text-align: center;
            margin: 10px 0;
            padding: 10px;
            border-radius: 5px;
        }
        .success { background: #dff0d8; color: #3c763d; }
        .error { background: #f2dede; color: #a94442; }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            padding: 10px;
            border: 1px solid #ddd;
            text-align: left;
        }
        th { background: #f5f5f5; }
    </style>

    <canvas id="drawingCanvas" width="500" height="400"></canvas>

    <div class="controls">
        <button id="hintButton">Get Hint</button>
        <button id="resetButton">Reset Game</button>
    </div>

    <form id="guessForm">
        <input type="text" id="username" placeholder="Your Username" required>
        <input type="text" id="guess" placeholder="Your Guess" required>
        <button type="submit">Submit Guess</button>
    </form>

    <div id="hint" class="message"></div>
    <div id="message" class="message"></div>

    <table id="guessTable">
        <thead>
            <tr>
                <th>Username</th>
                <th>Guess</th>
                <th>Result</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody></tbody>
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

let currentDrawing = null;
const drawings = [
    { label: "car", hints: ["It has wheels", "Used for transportation"] },
    { label: "house", hints: ["People live in it", "Has a roof"] },
    { label: "tree", hints: ["It grows", "Has leaves"] }
];

function showMessage(text, type) {
    const messageDiv = document.getElementById('message');
    messageDiv.textContent = text;
    messageDiv.className = `message ${type}`;
    messageDiv.style.display = 'block';
    messageDiv.style.backgroundColor = type === 'error' ? '#FBFBFB' : '#C4D9FF';
    messageDiv.style.color = type === 'error' ? '#E8F9FF' : '#C5BAFF';
    setTimeout(() => messageDiv.style.display = 'none', 3000);
}

async function fetchGuesses() {
    if (!await checkAuth()) return;

    try {
        const token = localStorage.getItem('token');
        const response = await fetch(`${API_URL}/guess`, {
            headers: {
                'Authorization': `Bearer ${token}`
            }
        });
        
        if (!response.ok) throw new Error('Failed to fetch guesses');
        const guesses = await response.json();
        updateGuessTable(guesses);
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
}

function updateGuessTable(guesses) {
    const tbody = document.querySelector('#guessTable tbody');
    tbody.innerHTML = '';
    
    guesses.forEach(guess => {
        const row = `
            <tr>
                <td>${guess.guesser_name}</td>
                <td>${guess.guess}</td>
                <td>${guess.is_correct ? 'Correct! ✅' : 'Wrong ❌'}</td>
                <td>
                    <button onclick="deleteGuess(${guess.id})">Delete</button>
                </td>
            </tr>
        `;
        tbody.innerHTML += row;
    });
}

async function submitGuess(event) {
    event.preventDefault();
    if (!await checkAuth()) return;

    const username = document.getElementById('username').value;
    const guess = document.getElementById('guess').value;
    const isCorrect = guess.toLowerCase() === currentDrawing.label.toLowerCase();

    try {
        const token = localStorage.getItem('token');
        const response = await fetch(`${API_URL}/guess`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${token}`
            },
            body: JSON.stringify({
                guesser_name: username,
                guess: guess,
                is_correct: isCorrect
            })
        });

        if (!response.ok) throw new Error('Failed to submit guess');
        
        document.getElementById('guess').value = '';
        showMessage(isCorrect ? 'Correct guess!' : 'Try again!', isCorrect ? 'success' : 'error');
        await fetchGuesses();
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
}

async function deleteGuess(id) {
    if (!await checkAuth()) return;
    if (!confirm('Are you sure you want to delete this guess?')) return;

    try {
        const token = localStorage.getItem('token');
        const response = await fetch(`${API_URL}/guess`, {
            method: 'DELETE',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${token}`
            },
            body: JSON.stringify({ id: id })
        });

        if (!response.ok) throw new Error('Failed to delete guess');
        showMessage('Guess deleted successfully');
        await fetchGuesses();
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
}

function startGame() {
    currentDrawing = drawings[Math.floor(Math.random() * drawings.length)];
    document.getElementById('hint').textContent = '';
    showMessage('New game started!');
    fetchGuesses();
}

document.getElementById('guessForm').addEventListener('submit', submitGuess);
document.getElementById('resetButton').addEventListener('click', startGame);
document.getElementById('hintButton').addEventListener('click', () => {
    const hint = currentDrawing.hints[Math.floor(Math.random() * currentDrawing.hints.length)];
    document.getElementById('hint').textContent = `Hint: ${hint}`;
});

// Initialize the game
if (checkAuth()) {
    startGame();
}
</script>
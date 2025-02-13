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
    <div class="word-card">
        <h2>Word Guessing Game</h2>
        <div id="current-word">
            <p>Loading word...</p>
        </div>
        <div class="hint-section" id="hints">
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
    
    <div class="stats-container">
        <h3>Your Statistics</h3>
        <div id="stats-display">
            <p>Loading stats...</p>
        </div>
    </div>
</div>

<script type="module">
import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

<<<<<<< HEAD
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
=======
let currentWord = '';
let currentHintIndex = 0;
let hintsUsed = 0;
>>>>>>> 213d098706552fe06b1e73ffeabe037bb2ce5cdd

async function loadNewWord() {
    try {
        const response = await fetch(`${pythonURI}/api/guess`, {
            method: 'GET',
            credentials: 'include'
        });
        
        if (!response.ok) throw new Error('Failed to fetch word');
        
        const data = await response.json();
        currentWord = data.word;
        currentHintIndex = 0;
        hintsUsed = 0;
        
        document.getElementById('current-word').innerHTML = `
            <p>Word Length: ${data.word_length}</p>
            <p>First Hint: ${data.first_hint}</p>
        `;
        
        document.getElementById('hint-list').innerHTML = '';
        document.getElementById('result-message').textContent = '';
        document.getElementById('guess-input').value = '';
    } catch (error) {
        console.error('Error:', error);
        document.getElementById('current-word').innerHTML = 
            '<p class="error">Failed to load word</p>';
    }
}

// Make getNextHint available globally
window.getNextHint = async function() {
    try {
        const nextHintIndex = currentHintIndex + 1;
        const response = await fetch(
            `${pythonURI}/api/guess/hint/${currentWord}?hint_number=${nextHintIndex}`, {
            method: 'GET',
            credentials: 'include',
            headers: {
                'Content-Type': 'application/json',
                'X-Origin': 'client'
            }
        });
        
        if (!response.ok) {
            document.getElementById('hint-button').disabled = true;
            throw new Error('No more hints available');
        }
        
        currentHintIndex = nextHintIndex;
        hintsUsed++;
        
        const data = await response.json();
        const hintList = document.getElementById('hint-list');
        const hintElement = document.createElement('p');
        hintElement.textContent = data.hint;
        hintList.appendChild(hintElement);
    } catch (error) {
        console.error('Error:', error);
        document.getElementById('hint-button').disabled = true;
    }
}

window.submitGuess = async function(event) {
    event.preventDefault();
    const guess = document.getElementById('guess-input').value;
    
    try {
        const response = await fetch(`${pythonURI}/api/guess`, {
            method: 'POST',
            credentials: 'include',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                word: currentWord,
                guess: guess,
                hint_used: hintsUsed
            })
        });
        
        const result = await response.json();
        const messageElement = document.getElementById('result-message');
        
        if (result.correct) {
            messageElement.className = 'success';
            messageElement.textContent = 'Correct! Loading new word...';
            await loadStats();
            setTimeout(loadNewWord, 2000);
        } else {
            messageElement.className = 'error';
            messageElement.textContent = 'Incorrect. Try again!';
        }
    } catch (error) {
        console.error('Error:', error);
        document.getElementById('result-message').textContent = 'Failed to submit guess';
    }
};

async function loadStats() {
    try {
        const response = await fetch(`${pythonURI}/api/guess/stats`, {
            credentials: 'include'
        });
        
        if (!response.ok) throw new Error('Failed to fetch stats');
        
        const stats = await response.json();
        document.getElementById('stats-display').innerHTML = `
            <p>Total Guesses: ${stats.total_guesses}</p>
            <p>Correct Guesses: ${stats.correct_guesses}</p>
            <p>Accuracy: ${(stats.accuracy * 100).toFixed(1)}%</p>
            <p>Average Hints Used: ${stats.avg_hints.toFixed(1)}</p>
        `;
    } catch (error) {
        console.error('Error:', error);
        document.getElementById('stats-display').innerHTML = 
            '<p class="error">Failed to load statistics</p>';
    }
}

// Add event listener in the DOMContentLoaded handler
document.addEventListener('DOMContentLoaded', () => {
    loadNewWord();
    loadStats();
    
    // Add click handler for hint button
    document.getElementById('hint-button').addEventListener('click', getNextHint);
});
</script>
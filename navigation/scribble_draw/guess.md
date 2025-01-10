---
layout: post
title: Scribble Guess
search_exclude: true
description: Scribble Guess
permalink: /guess
author: Keerthan
---

<div>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f9f9f9;
            color: #333;
            text-align: center;
            padding: 20px;
        }
        canvas {
            border: 2px solid #000;
            display: block;
            margin: 20px auto;
            background-color: #fff;
        }
        .controls {
            text-align: center;
            margin: 10px;
        }
        .hint {
            font-size: 1.2em;
            color: #555;
            text-align: center;
            margin-top: 10px;
        }
        .message {
            font-size: 1.2em;
            margin-top: 10px;
        }
    </style>
    <canvas id="drawingCanvas" width="500" height="500"></canvas>
    <div class="controls">
        <input type="text" id="guessInput" placeholder="Your Guess" autofocus>
        <button id="submitGuess">Submit Guess</button>
        <button id="hintButton">Get Hint</button>
        <button id="resetGame">Reset Game</button>
    </div>
    <div class="hint" id="hintArea">Hint: ???</div>
    <div class="message" id="messageArea"></div>

    <div>
        <h1>Submit Your Guess</h1>
        <form id="guessForm">
            <label for="user">User:</label>
            <input type="text" id="user" name="user" required><br><br>
            
            <label for="guess">Guess:</label>
            <input type="text" id="guess" name="guess" required><br><br>
            
            <label for="is_correct">Is Correct:</label>
            <input type="checkbox" id="is_correct" name="is_correct"><br><br>
            
            <label for="drawing_id">Drawing ID:</label>
            <input type="number" id="drawing_id" name="drawing_id" required><br><br>
            
            <button type="submit">Submit Guess</button>
        </form>
    </div>
</div>

<script>
const canvas = document.getElementById('drawingCanvas');
const ctx = canvas.getContext('2d');
const guessInput = document.getElementById('guessInput');
const submitGuessButton = document.getElementById('submitGuess');
const hintButton = document.getElementById('hintButton');
const resetGameButton = document.getElementById('resetGame');
const hintArea = document.getElementById('hintArea');
const messageArea = document.getElementById('messageArea');

// Game setup
const drawings = [
    {
        label: "cat",
        hintSteps: [
            "It is a common pet.",
            "It says 'meow'.",
            "It has whiskers and pointy ears."
        ],
        draw: () => {
            ctx.beginPath();
            ctx.arc(250, 200, 50, 0, Math.PI * 2); // Head
            ctx.moveTo(220, 180);
            ctx.lineTo(240, 140); // Left ear
            ctx.lineTo(260, 180);
            ctx.moveTo(280, 180);
            ctx.lineTo(300, 140); // Right ear
            ctx.lineTo(320, 180);
            ctx.stroke();
            ctx.beginPath();
            ctx.arc(240, 210, 5, 0, Math.PI * 2); // Left eye
            ctx.arc(260, 210, 5, 0, Math.PI * 2); // Right eye
            ctx.moveTo(250, 220);
            ctx.lineTo(250, 230); // Nose to mouth
            ctx.moveTo(250, 230);
            ctx.lineTo(240, 240); // Left mouth
            ctx.moveTo(250, 230);
            ctx.lineTo(260, 240); // Right mouth
            ctx.stroke();
        }
    },
    {
        label: "tree",
        hintSteps: [
            "It is tall and green.",
            "It has leaves and a trunk.",
            "It provides shade in summer."
        ],
        draw: () => {
            ctx.fillStyle = '#8B4513';
            ctx.fillRect(230, 300, 40, 100); // Trunk
            ctx.beginPath();
            ctx.arc(250, 250, 50, 0, Math.PI * 2); // Foliage
            ctx.fillStyle = '#228B22';
            ctx.fill();
        }
    },
    {
        label: "house",
        hintSteps: [
            "It is where people live.",
            "It has a roof and walls.",
            "It often has a door and windows."
        ],
        draw: () => {
            ctx.fillStyle = '#FF6347';
            ctx.fillRect(200, 250, 100, 100); // Base
            ctx.beginPath();
            ctx.moveTo(200, 250);
            ctx.lineTo(250, 200); // Roof
            ctx.lineTo(300, 250);
            ctx.closePath();
            ctx.fillStyle = '#FFD700';
            ctx.fill();
        }
    }
];

// Game state
let currentDrawing = null;
let hintIndex = 0;

function startGame() {
    currentDrawing = drawings[Math.floor(Math.random() * drawings.length)];
    hintIndex = 0;

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    currentDrawing.draw();
    hintArea.textContent = "Hint: ???";
    messageArea.textContent = "";
    guessInput.value = "";
}

hintButton.addEventListener('click', () => {
    if (!currentDrawing) return;
    if (hintIndex < currentDrawing.hintSteps.length) {
        hintArea.textContent = `Hint: ${currentDrawing.hintSteps[hintIndex]}`;
        hintIndex++;
    } else {
        hintArea.textContent = "No more hints available.";
    }
});

submitGuessButton.addEventListener('click', async () => {
    const guess = guessInput.value.trim().toLowerCase();

    if (!guess) {
        messageArea.textContent = "Please enter a guess!";
        return;
    }

    if (guess === currentDrawing.label) {
        messageArea.textContent = "Correct! You guessed it!";
        messageArea.style.color = "green";
    } else {
        messageArea.textContent = "Wrong guess! Try again.";
        messageArea.style.color = "red";
    }

    const user = document.getElementById('user').value;
    const isCorrect = guess === currentDrawing.label;
    const drawingId = drawings.indexOf(currentDrawing) + 1; // For example, if cat is selected, the ID will be 1

    const payload = { user, guess, is_correct: isCorrect, drawing_id: drawingId };

    try {
        const response = await fetch('http://127.0.0.1:5000/api/submit_guess', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(payload),
        });

        const data = await response.json();
        if (response.ok) {
            console.log('Guess submitted successfully:', data);
        } else {
            console.error('Error:', data.error);
        }
    } catch (error) {
        console.error('Error submitting guess:', error);
    }
});

resetGameButton.addEventListener('click', startGame);

// Start the first game
startGame();
</script>

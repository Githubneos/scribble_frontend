---
layout: post
title: Competition
search_exclude: true
description: Competition
permalink: /competition
menu: nav/scribble_draw.html
Author: Ian
---

<div>
    <style>
        div {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #282c34;
            color: white;
            padding: 20px;
            height: 100vh;
        }

        #drawing-board {
            border: 2px solid white;
            background-color: #444;
            margin: 20px auto;
            display: block;
        }

        .controls {
            margin: 20px auto;
            display: flex;
            justify-content: center;
            gap: 10px;
        }

        .controls input, .controls button {
            padding: 10px;
            border: none;
            border-radius: 5px;
            font-size: 16px;
        }

        .controls input {
            width: 150px;
        }

        .controls button {
            background-color: #4CAF50;
            color: white;
            cursor: pointer;
        }

        .controls button:hover {
            background-color: #45a049;
        }
    </style>

    <h1>Drawing Board</h1>
    <canvas id="drawing-board" width="600" height="400"></canvas>
    <div class="controls">
        <input type="number" id="timer-input" placeholder="Enter time (s)">
        <button id="start-button">Start Timer</button>
    </div>
    <p id="timer-display">Timer: Not started</p>
</div>

<script>
    const canvas = document.getElementById('drawing-board');
    const ctx = canvas.getContext('2d');
    const timerInput = document.getElementById('timer-input');
    const timerDisplay = document.getElementById('timer-display');
    const startButton = document.getElementById('start-button');

    let drawingAllowed = false;
    let timer = null;

    // Setup drawing
    canvas.addEventListener('mousedown', () => drawingAllowed = true);
    canvas.addEventListener('mouseup', () => drawingAllowed = false);
    canvas.addEventListener('mousemove', draw);

    function draw(event) {
        if (!drawingAllowed) return;

        const rect = canvas.getBoundingClientRect();
        const x = event.clientX - rect.left;
        const y = event.clientY - rect.top;

        ctx.fillStyle = "white";
        ctx.beginPath();
        ctx.arc(x, y, 5, 0, Math.PI * 2);
        ctx.fill();
    }

    // Timer functionality
    startButton.addEventListener('click', () => {
        const timeInSeconds = parseInt(timerInput.value);

        if (isNaN(timeInSeconds) || timeInSeconds <= 0) {
            alert('Please enter a valid time in seconds.');
            return;
        }

        if (timer) {
            clearTimeout(timer);
        }

        drawingAllowed = true;
        timerDisplay.textContent = `Timer: ${timeInSeconds} seconds left`;

        let timeRemaining = timeInSeconds;
        const interval = setInterval(() => {
            timeRemaining--;
            timerDisplay.textContent = `Timer: ${timeRemaining} seconds left`;

            if (timeRemaining <= 0) {
                clearInterval(interval);
                drawingAllowed = false;
                timerDisplay.textContent = "Timer: Time's up!";
                alert('Time is up! Thanks for drawing.');
            }
        }, 1000);
    });
</script>



---
layout: post
title: Competition
search_exclude: true
description: Competition
permalink: /competition
Author: Ian
---
<div>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Arial', sans-serif;
        }
        body {
            background-color: #282c34;
            color: white;
            text-align: center;
            height: 100vh;
        }
         h1 {
            margin: 20px 0;
            font-size: 2.5em;
            color: #61dafb;
        }
         #drawing-board {
            border: 3px solid #61dafb;
            background-color: #444;
            display: block;
            margin: 20px auto;
            border-radius: 10px;
        }
        .controls {
            margin: 20px auto;
            display: flex;
            justify-content: center;
            gap: 15px;
            flex-wrap: wrap;
        }
         .controls input, .controls button, .controls select {
            padding: 10px;
            border: none;
            border-radius: 5px;
            font-size: 16px;
        }
        .controls input[type="color"] {
            height: 40px;
            width: 60px;
            padding: 0;
            cursor: pointer;
            border: 2px solid #61dafb;
            border-radius: 5px;
        }
        .controls button {
            background-color: #4CAF50;
            color: white;
            cursor: pointer;
            transition: background 0.3s ease;
        }
        .controls button:hover {
            background-color: #45a049;
        }
        #timer-display {
            margin: 15px auto;
            font-size: 1.2em;
        }
        .footer {
            margin-top: 20px;
            font-size: 0.9em;
            color: #aaa;
        }
    </style>
    <h1>🎨 Welcome to Scribble Art 🎨</h1>
    <canvas id="drawing-board" width="800" height="500"></canvas>
    <div class="controls">
        <input type="color" id="color-picker" value="#ffffff" title="Choose a color">
        <select id="line-width" title="Brush size">
            <option value="2">Thin</option>
            <option value="5" selected>Medium</option>
            <option value="10">Thick</option>
            <option value="15">Extra Thick</option>
        </select>
        <button id="erase-button">Erase</button>
        <button id="clear-button">Clear Board</button>
        <input type="number" id="timer-input" placeholder="Time (s)">
        <button id="start-button">Start Timer</button>
    </div>
    <p id="timer-display">Timer: Not started</p>
    <div class="footer">Enjoy your time creating art and having fun!</div>
</div>

<script>
    const canvas = document.getElementById('drawing-board');
    const ctx = canvas.getContext('2d');
    const colorPicker = document.getElementById('color-picker');
    const lineWidthPicker = document.getElementById('line-width');
    const eraseButton = document.getElementById('erase-button');
    const clearButton = document.getElementById('clear-button');
    const timerInput = document.getElementById('timer-input');
    const timerDisplay = document.getElementById('timer-display');
    const startButton = document.getElementById('start-button');

    let drawingAllowed = false;
    let currentColor = colorPicker.value;
    let lineWidth = parseInt(lineWidthPicker.value);
    let erasing = false;

    let timer = null; // Timer reference for setTimeout (if used)
    let interval = null; // Reference for the interval (setInterval)

    // Setup drawing
    canvas.addEventListener('mousedown', startDrawing);
    canvas.addEventListener('mouseup', stopDrawing);
    canvas.addEventListener('mousemove', draw);

    function startDrawing() {
        drawingAllowed = true;
        ctx.beginPath();
    }

    function stopDrawing() {
        drawingAllowed = false;
        ctx.beginPath(); // Reset path
    }

    function draw(event) {
        if (!drawingAllowed) return;

        const rect = canvas.getBoundingClientRect();
        const x = event.clientX - rect.left;
        const y = event.clientY - rect.top;

        ctx.lineWidth = lineWidth;
        ctx.lineCap = "round";

        if (erasing) {
            ctx.strokeStyle = "#444"; // Match background color
        } else {
            ctx.strokeStyle = currentColor;
        }

        ctx.lineTo(x, y);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(x, y);
    }

    // Color Picker
    colorPicker.addEventListener('input', () => {
        currentColor = colorPicker.value;
        erasing = false; // Stop erasing if a new color is chosen
    });

    // Line Width Picker
    lineWidthPicker.addEventListener('change', () => {
        lineWidth = parseInt(lineWidthPicker.value);
    });

    // Erase Button
    eraseButton.addEventListener('click', () => {
        erasing = true;
    });

    // Clear Button
    clearButton.addEventListener('click', () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        erasing = false;
    });

    // Timer Functionality
    startButton.addEventListener('click', () => {
        const timeInSeconds = parseInt(timerInput.value);

        if (isNaN(timeInSeconds) || timeInSeconds <= 0) {
            alert('Please enter a valid time in seconds.');
            return;
        }

        // Clear any existing timer or interval to prevent overlap
        if (interval) {
            clearInterval(interval);
        }

        drawingAllowed = true;
        timerDisplay.textContent = `Timer: ${timeInSeconds} seconds left`;

        let timeRemaining = timeInSeconds;

        // Start a new interval for the countdown
        interval = setInterval(() => {
            timeRemaining--;
            timerDisplay.textContent = `Timer: ${timeRemaining} seconds left`;

            if (timeRemaining <= 0) {
                clearInterval(interval);
                drawingAllowed = false;
                timerDisplay.textContent = "Timer: Time's up!";
                alert('Time is up! Hope you enjoyed drawing!');
            }
        }, 1000);
    });
</script>

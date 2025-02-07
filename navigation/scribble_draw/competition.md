---
layout: post
title: Competition
search_exclude: true
description: Competition
permalink: /competition
Author: Ian
---
### Nav:
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
        .entry-form {
            margin: 20px auto;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
        }
        .entry-form input {
            padding: 10px;
            border: 2px solid #61dafb;
            border-radius: 5px;
            font-size: 16px;
        }
        .entry-form button {
            background-color: #4CAF50;
            color: white;
            cursor: pointer;
            transition: background 0.3s ease;
        }
        .entry-form button:hover {
            background-color: #45a049;
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
<button id="save-button">Save Drawing</button>

<div class="entry-form">
    <input type="text" id="user-name" placeholder="Enter your name">
    <input type="number" id="time-spent" placeholder="Enter the time spent (in minutes)">
    <input type="number" id="drawings-count" placeholder="Enter the number of drawings">
    <button id="submit-entry">Submit Entry</button>
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
    const saveButton = document.getElementById('save-button');
    const submitEntryButton = document.getElementById('submit-entry');
    const userNameInput = document.getElementById('user-name');
    const timeSpentInput = document.getElementById('time-spent');
    const drawingsCountInput = document.getElementById('drawings-count');

    let drawingAllowed = false;
    let currentColor

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

    // Timer functionality (handles both random and user-defined time)
    function startTimer(timeInSeconds) {
        // Start timer only if it's not already started
        if (timerStarted) return;

        timerStarted = true;
        startButton.disabled = true;  // Disable the start button after the timer starts
        timerDisplay.textContent = `Timer: ${timeInSeconds} seconds left`;

        let timeRemaining = timeInSeconds;

        // Start a new interval for the countdown
        interval = setInterval(() => {
            timeRemaining--;
            timerDisplay.textContent = `Timer: ${timeRemaining} seconds left`;

            if (timeRemaining <= 0) {
                clearInterval(interval);
                drawingAllowed = false;  // Disable drawing after the timer ends
                timerDisplay.textContent = "Timer: Time's up!";
                alert('Time is up! Hope you enjoyed drawing!');

                // Disable drawing and prevent re-enabling of the timer
                canvas.style.pointerEvents = "none";  // Disable canvas interaction
                startButton.disabled = true;  // Disable the timer button again after the timer ends
                eraseButton.disabled = true;  // Disable the erase button
                clearButton.disabled = true;  // Disable the clear board button

                // Save the drawing and submit the entry
                saveDrawing();
            }
        }, 1000);
    }

    function saveDrawing() {
        const canvasData = canvas.toDataURL(); // Base64 string of the canvas

        // Prompt the user for their name, time spent, and number of drawings
        const userName = prompt("Enter your name:");
        const timeSpent = parseInt(prompt("Enter the time spent (in minutes):"));
        const drawingsCount = parseInt(prompt("Enter the number of drawings:"));

        if (!userName || isNaN(timeSpent) || isNaN(drawingsCount)) {
            alert("Invalid input. Please try again.");
            return;
        }

        // Step 1: Save the drawing on the server
        fetch('/api/save_drawing', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ canvasData, userName, timeSpent, drawingsCount })
        })
            .then(response => response.json())
            .then(data => {
                console.log("Saved on server:", data);
                alert('Drawing and entry submitted successfully!');
            })
            .catch(error => {
                console.error("Error saving on server:", error);
                alert('Error submitting entry. Please try again later.');
            });

        // Step 2: Prompt the user to save the drawing locally
        const link = document.createElement('a');
        link.href = canvasData;
        link.download = 'drawing.png'; // Default filename
        link.click(); // Trigger the download
    }
</script>
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
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            background: linear-gradient(145deg, #FBFBFB, #E8F9FF, #C4D9FF, #C5BAFF);
            color: white;
            text-align: center;
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
        #user-table {
            margin: 20px auto;
            border-collapse: collapse;
            width: 80%;
        }
        #user-table th, #user-table td {
            border: 1px solid #ddd;
            padding: 8px;
        }
        #user-table th {
            background-color: #4CAF50;
            color: white;
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
        <button id="stop-button">Stop Timer</button>
    </div>
    <p id="timer-display">Timer: Not started</p>
    <div class="footer">Enjoy your time creating art and having fun!</div>
    <table id="user-table">
        <thead>
            <tr>
                <th>User</th>
                <th>Timer</th>
                <th>Drawn</th>
            </tr>
        </thead>
        <tbody></tbody>
    </table>
</div>
<button id="save-button">Save Drawing</button>

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
    const stopButton = document.getElementById('stop-button');
    const userTableBody = document.querySelector('#user-table tbody');

    let drawingAllowed = false;
    let currentColor = colorPicker.value;
    let lineWidth = parseInt(lineWidthPicker.value);
    let erasing = false;

    let interval = null; // Reference for the interval (setInterval)
    let timerStarted = false; // Track if the timer has already started

    // Setup drawing
    canvas.addEventListener('mousedown', startDrawing);
    canvas.addEventListener('mouseup', stopDrawing);
    canvas.addEventListener('mousemove', draw);

    // Getting button from html
    document.getElementById('save-button').addEventListener('click', saveDrawing);

    function startDrawing() {
        // If timer hasn't started yet, start it automatically
        if (!timerStarted) {
            let timeInSeconds = parseInt(timerInput.value);

            if (isNaN(timeInSeconds) || timeInSeconds <= 0) {
                // If no valid timer value entered, pick a random value between 20-100 seconds
                timeInSeconds = Math.floor(Math.random() * (30 - 20 + 1)) + 20;
            }

            startTimer(timeInSeconds);
        }
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

    // Timer functionality (handles both random and user-defined time)
    function startTimer(timeInSeconds) {
        // Start timer only if it's not already started
        if (timerStarted) return;

        timerStarted = true;
        startButton.disabled = true;  // Disable the start button after the timer starts
        stopButton.disabled = false;  // Enable the stop button after the timer starts
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
                stopButton.disabled = true;  // Disable the stop button
                eraseButton.disabled = true;  // Disable the erase button
                clearButton.disabled = true;  // Disable the clear board button

                // Save the drawing and submit the entry
                saveDrawing();
            }
        }, 1000);
    }

    function stopTimer() {
        if (interval) {
            clearInterval(interval);
            timerStarted = false;
            startButton.disabled = false;  // Re-enable the start button
            stopButton.disabled = true;  // Disable the stop button
            timerDisplay.textContent = "Timer: Stopped";
        }
    }

    stopButton.addEventListener('click', stopTimer);

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

        // Step 2: Prompt the user to save the drawing locally
        const link = document.createElement('a');
        link.href = canvasData;
        link.download = 'drawing.png'; // Default filename
        link.click(); // Trigger the download

        // Add the user data to the table
        addUserToTable(userName, timeSpent, drawingsCount);
    }

    function addUserToTable(user, timer, drawn) {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>${user}</td>
            <td>${timer}</td>
            <td>${drawn}</td>
        `;
        userTableBody.appendChild(row);
    }

    // Function to fetch the timer status from the server
    function fetchTimerStatus() {
        fetch('/api/timer_status')
            .then(response => response.json())
            .then(data => {
                if (data.is_active) {
                    timerDisplay.textContent = `Timer: ${data.time_remaining} seconds left`;
                } else {
                    timerDisplay.textContent = "Timer: Not started";
                }
            })
            .catch(error => {
                console.error("Error fetching timer status:", error);
            });
    }

    // Set an interval to fetch the timer status every second
    setInterval(fetchTimerStatus, 1000);
</script>
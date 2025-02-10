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

<style>
    body {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        min-height: 100vh;
        margin: 0;
        background: #f4f4f4;
        font-family: Arial, sans-serif;
    }

    .toolbar {
        display: flex;
        align-items: center;
        gap: 10px;
        background: #ffffff;
        padding: 12px;
        border-radius: 10px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        margin-bottom: 15px;
    }

    .toolbar button, .toolbar input {
        padding: 10px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
        transition: 0.3s ease-in-out;
    }

    .toolbar button:hover {
        transform: scale(1.1);
    }

    .toolbar input[type="color"] {
        width: 40px;
        height: 40px;
        border: none;
        cursor: pointer;
    }

    #eraser {
        background: white;
        color: black;
        border: 2px solid black;
    }

    #eraser.active {
        background: black;
        color: white;
    }

    #canvas-container {
        display: flex;
        justify-content: center;
        align-items: center;
        background: white;
        border: 3px solid black;
        border-radius: 10px;
        overflow: hidden;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    }

    canvas {
        cursor: crosshair;
        display: block;
    }

    #timer {
        font-size: 20px;
        font-weight: bold;
        margin-bottom: 10px;
    }

    .user-inputs {
        display: flex;
        flex-direction: column;
        gap: 10px;
        margin-bottom: 20px;
    }

    .user-inputs input {
        padding: 10px;
        border: 1px solid #ccc;
        border-radius: 5px;
    }

    .user-inputs button {
        padding: 10px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
        background: #007BFF;
        color: white;
        transition: 0.3s ease-in-out;
    }

    .user-inputs button:hover {
        transform: scale(1.1);
    }

    .user-table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 20px;
    }

    .user-table th, .user-table td {
        border: 1px solid #ddd;
        padding: 8px;
    }

    .user-table th {
        background-color: #f2f2f2;
        text-align: left;
    }
</style>

<div id="timer">Time left: 20s</div>
<div class="toolbar">
    <input type="color" id="colorPicker" value="#000000">
    <input type="range" id="brushSize" min="1" max="30" value="5">
    <button id="eraser">Eraser</button>
    <button id="undo" style="background: #FFC107; color: white;">Undo</button>
    <button id="clear" style="background: #DC3545; color: white;">Clear</button>
    <button id="save" style="background: #28A745; color: white;">Save</button>
    <button id="newAttempt" style="background: #007BFF; color: white;">New Attempt</button>
</div>

<div class="user-inputs">
    <input type="text" id="userName" placeholder="User Name">
    <input type="number" id="attemptsMade" placeholder="Attempts Made">
    <input type="number" id="timeTaken" placeholder="Time Taken (s)">
    <button id="submitData">Submit</button>
</div>

<div class="user-inputs">
    <input type="text" id="variableName" placeholder="Variable Name">
    <input type="text" id="variableValue" placeholder="Variable Value">
    <button id="submitVariable">Go</button>
</div>

<table class="user-table">
    </thead>
    <tbody id="userTableBody">
    </tbody>
</table>

<div id="canvas-container">
    <canvas id="drawingCanvas" width="800" height="500"></canvas>
</div>

<script>
    const canvas = document.getElementById("drawingCanvas");
    const ctx = canvas.getContext("2d");
    const colorPicker = document.getElementById("colorPicker");
    const brushSize = document.getElementById("brushSize");
    const eraserButton = document.getElementById("eraser");
    const undoButton = document.getElementById("undo");
    const clearButton = document.getElementById("clear");
    const saveButton = document.getElementById("save");
    const newAttemptButton = document.getElementById("newAttempt");
    const timerDisplay = document.getElementById("timer");
    const userNameInput = document.getElementById("userName");
    const attemptsMadeInput = document.getElementById("attemptsMade");
    const timeTakenInput = document.getElementById("timeTaken");
    const submitDataButton = document.getElementById("submitData");
    const userTableBody = document.getElementById("userTableBody");

    let drawing = false;
    let currentColor = colorPicker.value;
    let currentSize = brushSize.value;
    let isEraser = false;
    let undoStack = [];
    let timer;
    let timeLeft = 20;
    let attemptCount = 1;

    ctx.fillStyle = "white";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    function startDrawing(e) {
        if (timeLeft > 0) {
            drawing = true;
            ctx.beginPath();
            ctx.moveTo(e.offsetX, e.offsetY);
        }
    }

    function draw(e) {
        if (!drawing) return;
        ctx.strokeStyle = isEraser ? "white" : currentColor;
        ctx.lineWidth = currentSize;
        ctx.lineCap = "round";
        ctx.lineTo(e.offsetX, e.offsetY);
        ctx.stroke();
    }

    function stopDrawing() {
        if (drawing) {
            drawing = false;
            ctx.closePath();
            undoStack.push(canvas.toDataURL());
        }
    }

    function startTimer() {
        clearInterval(timer);
        timeLeft = 20;
        timerDisplay.textContent = `Time left: ${timeLeft}s`;
        timer = setInterval(() => {
            timeLeft--;
            timerDisplay.textContent = `Time left: ${timeLeft}s`;
            if (timeLeft <= 0) {
                clearInterval(timer);
                drawing = false;
            }
        }, 1000);
    }

    function newAttempt() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = "white";
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        undoStack = [];
        attemptCount++;
        startTimer();
        timerDisplay.textContent = `Attempt ${attemptCount}`;
    }

    function fetchUserAttempts() {
        fetch('/api/user_attempts')
            .then(response => response.json())
            .then(data => {
                userTableBody.innerHTML = '';
                data.forEach(user => {
                    const row = document.createElement('tr');
                    row.innerHTML = `
                        <td>${user.user_name}</td>
                        <td>${user.attempts_made}</td>
                        <td>${user.time_taken}</td>
                    `;
                    userTableBody.appendChild(row);
                });
            })
            .catch(error => console.error('Error fetching user attempts:', error));
    }

    function submitUserData() {
        const userName = userNameInput.value;
        const attemptsMade = attemptsMadeInput.value;
        const timeTaken = timeTakenInput.value;

        fetch('/api/user_attempts', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                user_name: userName,
                attempts_made: attemptsMade,
                time_taken: timeTaken
            })
        })
        .then(response => response.json())
        .then(data => {
            console.log('Success:', data);
            fetchUserAttempts();
        })
        .catch(error => console.error('Error:', error));
    }

    canvas.addEventListener("mousedown", startDrawing);
    canvas.addEventListener("mousemove", draw);
    canvas.addEventListener("mouseup", stopDrawing);
    canvas.addEventListener("mouseleave", stopDrawing);

    colorPicker.addEventListener("input", () => {
        currentColor = colorPicker.value;
        isEraser = false;
        eraserButton.classList.remove("active");
    });

    brushSize.addEventListener("input", () => {
        currentSize = brushSize.value;
    });

    eraserButton.addEventListener("click", () => {
        isEraser = !isEraser;
        eraserButton.classList.toggle("active", isEraser);
    });

    undoButton.addEventListener("click", () => {
        if (undoStack.length > 0) {
            undoStack.pop();
            const lastImage = undoStack.length > 0 ? undoStack[undoStack.length - 1] : null;
            const img = new Image();
            img.src = lastImage || '';
            img.onload = () => {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                ctx.drawImage(img, 0, 0);
            };
        }
    });

    clearButton.addEventListener("click", () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = "white";
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        undoStack = [];
    });

    saveButton.addEventListener("click", () => {
        const link = document.createElement("a");
        link.download = "drawing.png";
        link.href = canvas.toDataURL("image/png");
        link.click();
    });

    newAttemptButton.addEventListener("click", newAttempt);

    submitDataButton.addEventListener("click", submitUserData);

    fetchUserAttempts();
    startTimer();
</script>

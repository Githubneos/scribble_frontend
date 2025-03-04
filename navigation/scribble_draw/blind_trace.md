---
layout: needsAuth
title: Blind Trace
permalink: /blind_trace
menu: nav/home.html
search_exclude: true
---

<table>
    <tr>
        <td><a href="{{site.baseurl}}/index">Home</a></td>
        <td><a href="{{site.baseurl}}/competition">Competitive</a></td>
        <td><a href="{{site.baseurl}}/guess">Guess Game</a></td>
        <td><a href="{{site.baseurl}}/blind_trace">Blind Trace</a></td>
        <td><a href="{{site.baseurl}}/leaderboard">LeaderBoard</a></td>
        <td><a href="{{site.baseurl}}/stats">Statistics</a></td>
        <td><a href="{{site.baseurl}}/about">About Us</a></td>
        <td><a href="{{site.baseurl}}/deploy">Deploy Blog</a></td>
        <td><a href="{{site.baseurl}}/posts">Posts</a></td>
    </tr>
</table>

<style>
:root {
    --background: linear-gradient(145deg, #A6AEBF, #C5D3E8, #D0E8C5, #FFF8DE);
}

body {
    background: var(--background);
    min-height: 100vh;
    margin: 0;
    padding: 0;
}

.container {
    max-width: 1200px;
    margin: 2rem auto;
    padding: 1rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.canvas-container {
    text-align: center;
    margin-bottom: 2rem;
}

.canvas {
    border: 2px solid #ccc;
    background-color: #fafafa;
    cursor: crosshair;
}

.tool-panel {
    display: flex;
    justify-content: center;
    gap: 1rem;
    margin-bottom: 1rem;
}

.tool-btn {
    padding: 0.5rem 1rem;
    border: none;
    background: #2196F3;
    color: white;
    font-size: 1rem;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s;
}

.tool-btn:hover {
    background: #1976D2;
}

.color-picker {
    margin-top: 1rem;
}

.image-modal {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.8);
    z-index: 1000;
    justify-content: center;
    align-items: center;
}

.image-modal img {
    max-width: 80%;
    max-height: 80%;
    border: 5px solid white;
    border-radius: 8px;
}

</style>

<div class="container">
    <h2>Blind Trace Drawing Game</h2>
    <div id="image-display-container" class="canvas-container">
        <img id="reference-image" src="" alt="Reference Image" class="canvas">
        <button id="start-game-btn" class="tool-btn">Start Game</button>
    </div>
    <div id="canvas-section" class="canvas-container" style="display: none;">
        <canvas id="drawing-canvas" class="canvas"></canvas>
        <div class="tool-panel">
            <button id="clear-btn" class="tool-btn">Clear Canvas</button>
            <button id="reset-btn" class="tool-btn">Reset Drawing</button>
            <button id="view-btn" class="tool-btn">View Image</button>
        </div>
    </div>
    <div class="color-picker">
        <label>Select Color:</label>
        <input type="color" id="color-picker" value="#000000">
    </div>
    <div class="tool-panel">
        <button id="eraser-btn" class="tool-btn">Eraser</button>
        <button id="submit-btn" class="tool-btn">Submit Drawing</button>
    </div>
    <div id="score-container">
        <p id="score">Score: 0</p>
    </div>
    <div id="message" class="message"></div>
    <div id="submissions-container"></div>
</div>

<div class="container mt-4">
    <h2>Past Blind Trace Submissions</h2>
    <table id="blindTraceTable" class="table table-striped">
        <thead>
            <tr>
                <th>User</th>
                <th>Score</th>
                <th>Drawing</th>
                <th>Submission Time</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody></tbody> 
    </table>
</div>

<style>
:root {
    --background: linear-gradient(145deg, #A6AEBF, #C5D3E8, #D0E8C5, #FFF8DE);
}

body {
    background: var(--background);
    min-height: 100vh;
    margin: 0;
    padding: 0;
}

.container {
    max-width: 1200px;
    margin: 2rem auto;
    padding: 1rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.table {
    width: 100%;
    border-collapse: collapse;
}

.table th, .table td {
    padding: 10px;
    border: 1px solid #ddd;
    text-align: center;
}

.tool-panel {
    display: flex;
    justify-content: center;
    gap: 1rem;
}

.tool-btn {
    padding: 0.5rem 1rem;
    border: none;
    background: #2196F3;
    color: white;
    font-size: 1rem;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s;
}

.tool-btn:hover {
    background: #1976D2;
}

.color-picker {
    margin-top: 1rem;
}
</style>

<div class="container">
    <h2>Blind Trace Drawing Game</h2>
    <div id="image-display-container" class="canvas-container">
        <img id="reference-image" src="" alt="Reference Image" class="canvas hidden">
        <button id="start-game-btn" class="tool-btn">Start Game</button>
        <p id="view-count">View Image: 3 left</p>
    </div>
    <div id="canvas-section" class="canvas-container" style="display: none;">
        <canvas id="drawing-canvas" class="canvas"></canvas>
        <div class="tool-panel">
            <button id="clear-btn" class="tool-btn">Clear Canvas</button>
            <button id="reset-btn" class="tool-btn">Reset Drawing</button>
            <button id="view-btn" class="tool-btn">View Image</button>
        </div>
    </div>
    <div class="color-picker">
        <label>Select Color:</label>
        <input type="color" id="color-picker" value="#000000">
    </div>
    <div class="tool-panel">
        <button id="eraser-btn" class="tool-btn">Eraser</button>
        <button id="submit-btn" class="tool-btn">Submit Drawing</button>
    </div>
    <div id="score-container">
        <p id="score">Score: 0</p>
    </div>
    <div id="message" class="message"></div>
    <div id="submissions-container"></div>
</div>

<script>
let viewCount = 3;
let startTime;
const canvas = document.getElementById("drawing-canvas");
const ctx = canvas.getContext("2d");
let isDrawing = false;
canvas.width = 400;
canvas.height = 400;

// Initialize canvas
document.addEventListener('DOMContentLoaded', () => {
    loadReferenceImage();
    loadPastSubmissions();
});

// Load a random reference image
function loadReferenceImage() {
    const imageElement = document.getElementById("reference-image");
    const imageList = [
        "images/Bridge.jpg",
        "images/car.png",
        "images/colloseum.jpg",
        "images/french.jpg",
        "images/House.png",
        "images/school_logo.png",
        "images/stonehenge.jpg",
        "images/taj_mahal.jpg",
        "images.tower.jpg"
    ];
    imageElement.src = imageList[Math.floor(Math.random() * imageList.length)];
}

// Start game
document.getElementById("start-game-btn").addEventListener("click", function () {
    document.getElementById("image-display-container").style.display = "none";
    document.getElementById("canvas-section").style.display = "block";
    startTime = Date.now();
});

// View reference image with limit
document.getElementById("view-btn").addEventListener("click", function () {
    if (viewCount > 0) {
        document.getElementById("reference-image").classList.toggle("hidden");
        viewCount--;
        document.getElementById("view-count").innerText = `View Image: ${viewCount} left`;
    } else {
        alert("No more views left!");
    }
});

// Drawing functionality
canvas.addEventListener("mousedown", () => { isDrawing = true; });
canvas.addEventListener("mouseup", () => { isDrawing = false; ctx.beginPath(); });
canvas.addEventListener("mousemove", draw);

function draw(event) {
    if (!isDrawing) return;
    ctx.lineWidth = 2;
    ctx.lineCap = "round";
    ctx.strokeStyle = document.getElementById("color-picker").value;

    ctx.lineTo(event.offsetX, event.offsetY);
    ctx.stroke();
    ctx.beginPath();
    ctx.moveTo(event.offsetX, event.offsetY);
}

// Eraser functionality
document.getElementById("eraser-btn").addEventListener("click", () => {
    ctx.strokeStyle = "#ffffff";  // White for erasing
});

// Clear and Reset
document.getElementById("clear-btn").addEventListener("click", () => {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
});

document.getElementById("reset-btn").addEventListener("click", () => {
    loadReferenceImage();
    ctx.clearRect(0, 0, canvas.width, canvas.height);
});

// Submit drawing with random score
document.getElementById("submit-btn").addEventListener("click", function () {
    const drawingDataURL = canvas.toDataURL("image/png");
    const timeSpent = (Date.now() - startTime) / 1000;  // Time spent in seconds
    const score = Math.floor(Math.max(0, 100 - timeSpent * 2)); // Decreases score over time

    document.getElementById("score").innerText = `Score: ${score}`;

    const submissionData = {
        username: localStorage.getItem("username") || "Guest",
        drawing: drawingDataURL,
        score: score,
        submission_time: new Date().toISOString()
    };

    // Get past submissions from localStorage or initialize an empty array
    let submissions = JSON.parse(localStorage.getItem("blindTraceSubmissions")) || [];
    submissions.push(submissionData);

    // Save updated submissions to localStorage
    localStorage.setItem("blindTraceSubmissions", JSON.stringify(submissions));

    loadPastSubmissions();
});

// Load past submissions
function loadPastSubmissions() {
    const submissions = JSON.parse(localStorage.getItem("blindTraceSubmissions")) || [];
    displayPastSubmissions(submissions);
}

// Display past submissions
function displayPastSubmissions(submissions) {
    const tableBody = document.querySelector('#blindTraceTable tbody');
    tableBody.innerHTML = '';

    submissions.forEach((submission, index) => {
        const row = document.createElement("tr");
        row.innerHTML = `
            <td>${submission.username}</td>
            <td>${submission.score}</td>
            <td>
                <a href="${submission.drawing}" target="_blank">
                    <img src="${submission.drawing}" alt="Drawing" width="50">
                </a>
            </td>
            <td>${new Date(submission.submission_time).toLocaleString()}</td>
            <td>
                <button class="delete-btn" data-index="${index}">Delete</button>
            </td>
        `;
        tableBody.appendChild(row);
    });

    // Add event listeners for delete buttons
    document.querySelectorAll('.delete-btn').forEach(button => {
        button.addEventListener('click', deleteSubmission);
    });
}

// Delete a submission
function deleteSubmission(event) {
    const index = event.target.getAttribute("data-index");
    let submissions = JSON.parse(localStorage.getItem("blindTraceSubmissions")) || [];
    submissions.splice(index, 1); // Remove the submission

    localStorage.setItem("blindTraceSubmissions", JSON.stringify(submissions));
    loadPastSubmissions();  // Re-render the submissions
}
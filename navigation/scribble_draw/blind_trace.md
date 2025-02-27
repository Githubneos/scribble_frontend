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
    margin-bottom: 1rem;
    cursor: crosshair;
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

.tool-btn:active {
    background: #0d47a1;
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
    overflow: auto;
}

.image-modal img {
    max-width: 80%;
    max-height: 80%;
    border: 5px solid white;
    border-radius: 8px;
}

#start-button-container {
    text-align: center;
    margin-top: 2rem;
}

.tool-btn,
#view-btn {
    margin-top: 1rem;
}
</style>

<div class="container">
    <h2>Blind Trace Drawing Game</h2>
    <!-- Image Display Section -->
    <div id="image-display-container" class="canvas-container">
        <img id="reference-image" src="" alt="Reference Image" class="canvas">
        <button id="start-game-btn" class="tool-btn">Start Game</button>
    </div>
    <!-- Drawing Canvas Section (Hidden initially) -->
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
    <div id="image-modal" class="image-modal">
        <img id="modal-reference-image" />
        <button id="close-modal-btn" class="tool-btn" style="margin-top: 10px;">Close Image</button>
    </div>
</div>

<script type="module">
let referenceImageUrl = "";
let imageViewCount = 0;
let rotationAngle = 0;
let isImageRotating = false;
let drawing = false;
let ctx, canvas;

const referenceImages = [
    'images/Bridge.jpg',
    'images/car.jpg',
    'images/colloseum.jpg',
    'images/french.jpg',
    'images/House.jpg',
    'images/stonehenge.jpg',
    'images/taj_mahal.jpg',
    'images/tower.jpg',
];

document.addEventListener('DOMContentLoaded', () => {
    canvas = document.getElementById('drawing-canvas');
    ctx = canvas.getContext('2d');

    canvas.width = window.innerWidth * 0.8;
    canvas.height = canvas.width * (3 / 4);

    canvas.addEventListener('mousedown', startDrawing);
    canvas.addEventListener('mousemove', draw);
    canvas.addEventListener('mouseup', stopDrawing);
    canvas.addEventListener('mouseout', stopDrawing);

    document.getElementById('start-game-btn').addEventListener('click', startGame);
    document.getElementById('view-btn').addEventListener('click', viewImage);
    document.getElementById('close-modal-btn').addEventListener('click', closeImageModal);
    document.getElementById('eraser-btn').addEventListener('click', toggleEraser);
    document.getElementById('clear-btn').addEventListener('click', clearCanvas);
    document.getElementById('reset-btn').addEventListener('click', resetDrawing);
    document.getElementById('color-picker').addEventListener('input', changeColor);
    document.getElementById('submit-btn').addEventListener('click', submitDrawing);

    showReferenceImage();
    loadPastSubmissions();
});

// Show a random reference image for the user to trace
function showReferenceImage() {
    const randomIndex = Math.floor(Math.random() * referenceImages.length);
    referenceImageUrl = referenceImages[randomIndex];

    const referenceImage = document.getElementById('reference-image');
    referenceImage.src = referenceImageUrl;
    referenceImage.style.display = "block"; 
}

// Start the game and hide the reference image
function startGame() {
    setTimeout(() => {
        document.getElementById('image-display-container').style.display = 'none';
        document.getElementById('canvas-section').style.display = 'block';
        imageViewCount = 0;
    }, 3000);
}

// Open the modal to view the reference image
function viewImage() {
    if (imageViewCount < 3) {
        document.getElementById('image-modal').style.display = 'flex';
        document.getElementById('modal-reference-image').src = referenceImageUrl;
        imageViewCount++;
    } else {
        alert("❌ You have viewed the image 3 times already.");
    }
}

// Close the modal
function closeImageModal() {
    document.getElementById('image-modal').style.display = 'none';
}

// Start drawing on the canvas
function startDrawing(event) {
    drawing = true;
    ctx.beginPath();
    ctx.moveTo(event.offsetX, event.offsetY);
}

// Draw on the canvas as the mouse moves
function draw(event) {
    if (!drawing) return;
    ctx.lineTo(event.offsetX, event.offsetY);
    ctx.strokeStyle = document.getElementById('color-picker').value;
    ctx.lineWidth = 3;
    ctx.lineCap = 'round';
    ctx.stroke();
}

// Stop drawing when mouse is released or leaves the canvas
function stopDrawing() {
    drawing = false;
}

// Clear the canvas
function clearCanvas() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
}

// Reset the drawing (clear canvas)
function resetDrawing() {
    clearCanvas();
    showReferenceImage();
}

let drawingStartTime = null; // Store the start time when the user starts drawing

// Function to start tracking the drawing time
function startDrawing() {
    drawingStartTime = new Date(); // Set the start time when the user starts drawing
}

// Function to handle submitting the drawing
async function submitDrawing() {
    const drawingData = canvas.toDataURL(); // Convert canvas to base64 data URL

    // If the drawing start time is null, it means the user hasn't started drawing yet
    if (!drawingStartTime) {
        alert('Please start drawing before submitting!');
        return;
    }

    const drawingEndTime = new Date(); // Get the current time when the user submits the drawing
    const timeSpent = (drawingEndTime - drawingStartTime) / 1000; // Time spent in seconds

    // Generate a random score based on the time spent
    let score = generateScore(timeSpent);

    const requestData = {
        image_url: referenceImageUrl,
        drawing: drawingData,
        score: score, // Include the score in the request
    };

    try {
        const response = await fetch('/api/submission', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${localStorage.getItem('auth_token')}`, // Use JWT stored in localStorage
            },
            body: JSON.stringify(requestData),
        });

        const result = await response.json();
        if (response.ok) {
            alert(`Drawing submitted successfully! Score: ${score}`);
            loadPastSubmissions(); // Reload past submissions after submission
        } else {
            alert(`Error: ${result.message}`);
        }
    } catch (error) {
        alert('Failed to submit drawing. Please try again later.');
    }
}

// Function to generate a random score based on time spent (in seconds)
function generateScore(timeSpent) {
    // Adjust this formula based on how you want time to affect the score
    // For example, let's say 1 second equals 1 point, and the score is capped at 1000.
    let score = Math.min(timeSpent * 10, 1000); // Multiplies time by 10 for score
    score = Math.floor(score); // Ensure the score is an integer

    // Optionally add some random variance to the score
    const randomVariance = Math.floor(Math.random() * 50); // Random variance between 0 and 50
    score += randomVariance;

    // Ensure the score is between 0 and 1000
    return Math.min(Math.max(score, 0), 1000);
}

// Start the drawing when the user begins
document.getElementById('drawing-canvas').addEventListener('mousedown', startDrawing);

// Fetch past submissions for the current user (GET)
async function loadPastSubmissions() {
    try {
        const response = await fetch('/api/submissions', {
            method: 'GET',
            headers: {
                'Authorization': `Bearer ${localStorage.getItem('auth_token')}`, // Use JWT stored in localStorage
            },
        });

        const result = await response.json();
        if (response.ok) {
            displayPastSubmissions(result.submissions); // Display submissions if GET is successful
        } else {
            alert(`Error: ${result.message}`);
        }
    } catch (error) {
        alert('Failed to load past submissions. Please try again later.');
    }
}

// Display past submissions in a table
function displayPastSubmissions(submissions) {
    const submissionsContainer = document.getElementById('submissions-container');
    submissionsContainer.innerHTML = ''; // Clear existing content

    // Create a table to display the submissions
    const table = document.createElement('table');
    table.classList.add('submissions-table');
    table.innerHTML = `
        <tr>
            <th>User</th>
            <th>Score</th>
            <th>Actions</th>
        </tr>
    `;

    // Loop through submissions and display each one
    submissions.forEach(submission => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>${submission.username}</td>
            <td>${submission.score}</td>
            <td>
                <button onclick="deleteSubmission(${submission.id})">Delete</button>
            </td>
        `;
        table.appendChild(row);
    });

    submissionsContainer.appendChild(table);
}

// Delete a specific submission (DELETE)
async function deleteSubmission(submissionId) {
    if (!confirm('Are you sure you want to delete this submission?')) return;

    const requestData = { id: submissionId };

    try {
        const response = await fetch('/api/submission', {
            method: 'DELETE',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${localStorage.getItem('auth_token')}`, // Use JWT stored in localStorage
            },
            body: JSON.stringify(requestData),
        });

        const result = await response.json();
        if (response.ok) {
            alert('Submission deleted successfully');
            loadPastSubmissions(); // Reload the submissions after deletion
        } else {
            alert(`Error: ${result.message}`);
        }
    } catch (error) {
        alert('Failed to delete submission. Please try again later.');
    }
}

// Automatically load past submissions on page load
document.addEventListener('DOMContentLoaded', loadPastSubmissions);


// Toggle eraser mode
function toggleEraser() {
    ctx.globalCompositeOperation = ctx.globalCompositeOperation === 'source-over' ? 'destination-out' : 'source-over';
}

// Change drawing color
function changeColor(event) {
    ctx.strokeStyle = event.target.value;
}
</script>

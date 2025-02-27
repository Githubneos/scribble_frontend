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
    width: 80%;
    height: 400px;
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

.message {
    padding: 1rem;
    border-radius: 4px;
    margin: 1rem 0;
    display: none;
    transition: opacity 0.3s;
}

.success {
    background: #d4edda;
    color: #155724;
}

.error {
    background: #f8d7da;
    color: #721c24;
}

.image-modal {
    display: flex; 
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

.image-container {
    margin-bottom: 2rem;
    text-align: center;
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
    <!-- Canvas Container (Initially Hidden) -->
    <div class="canvas-container" id="canvas-container">
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
    <!-- Modal to display the reference image -->
    <div id="image-modal" class="image-modal" style="display: none;">
        <img id="modal-reference-image" />
        <button id="close-modal-btn" class="tool-btn" style="margin-top: 10px;">Close Image</button>
    </div>
</div>

<script type="module">
import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

let currentColor = "#000000";
let drawingMode = true;
let score = 0;
let referenceImageUrl = "";
let canvas, ctx;
let imageWidth = 0;
let imageHeight = 0;
let imageIndex = 0;  // Used to cycle through images
let imageViewCount = 0;  // Counter to track how many times the image has been viewed

// Image generation functions (cityscape, bridge, etc.)
function drawCityscape() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = '#8e9fbc'; // Buildings color
    ctx.fillRect(0, canvas.height - 50, canvas.width, 50); // Ground
    ctx.fillStyle = '#708090'; // Windows color
    ctx.fillRect(100, 150, 50, 100); // Building 1
    ctx.fillRect(200, 100, 50, 150); // Building 2
    ctx.fillRect(300, 130, 50, 120); // Building 3
    ctx.fillStyle = '#f0f0f0'; // Sky color
    ctx.fillRect(0, 0, canvas.width, canvas.height - 50); // Sky
}

function drawBridge() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = '#654321'; // Bridge color
    ctx.fillRect(50, 200, 300, 20); // Bridge base
    ctx.fillRect(50, 180, 20, 20); // Left pillar
    ctx.fillRect(330, 180, 20, 20); // Right pillar
    ctx.fillStyle = '#c0c0c0'; // Road color
    ctx.fillRect(50, 220, 300, 20); // Road
    ctx.fillStyle = '#87CEEB'; // Sky color
    ctx.fillRect(0, 0, canvas.width, 180); // Sky
}

function drawForest() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = '#228B22'; // Tree leaves color
    ctx.beginPath();
    ctx.arc(100, 200, 50, 0, Math.PI * 2); // Tree 1
    ctx.fill();
    ctx.beginPath();
    ctx.arc(250, 200, 50, 0, Math.PI * 2); // Tree 2
    ctx.fill();
    ctx.fillStyle = '#8B4513'; // Tree trunk color
    ctx.fillRect(90, 250, 20, 40); // Trunk 1
    ctx.fillRect(240, 250, 20, 40); // Trunk 2
    ctx.fillStyle = '#7CFC00'; // Grass color
    ctx.fillRect(0, canvas.height - 50, canvas.width, 50); // Grass
}

function drawCoralReef() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = '#20B2AA'; // Water color
    ctx.fillRect(0, 0, canvas.width, canvas.height); // Water
    ctx.fillStyle = '#FF4500'; // Coral color
    ctx.beginPath();
    ctx.arc(150, 350, 30, 0, Math.PI * 2); // Coral 1
    ctx.fill();
    ctx.beginPath();
    ctx.arc(250, 320, 30, 0, Math.PI * 2); // Coral 2
    ctx.fill();
    ctx.fillStyle = '#2E8B57'; // Seaweed color
    ctx.fillRect(50, 380, 10, 40); // Seaweed 1
    ctx.fillRect(200, 380, 10, 40); // Seaweed 2
}

function drawSolarSystem() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = '#FFD700'; // Sun color
    ctx.beginPath();
    ctx.arc(300, 200, 50, 0, Math.PI * 2); // Sun
    ctx.fill();
    ctx.fillStyle = '#4B0082'; // Planet color
    ctx.beginPath();
    ctx.arc(150, 200, 20, 0, Math.PI * 2); // Planet 1
    ctx.fill();
    ctx.fillStyle = '#00008B'; // Planet 2 color
    ctx.beginPath();
    ctx.arc(400, 200, 30, 0, Math.PI * 2); // Planet 2
    ctx.fill();
}

const drawings = [drawCityscape, drawBridge, drawForest, drawCoralReef, drawSolarSystem];

document.addEventListener('DOMContentLoaded', () => {
    canvas = document.getElementById('drawing-canvas');
    ctx = canvas.getContext('2d');
    canvas.addEventListener('mousedown', startDrawing);
    canvas.addEventListener('mousemove', draw);
    canvas.addEventListener('mouseup', stopDrawing);
    canvas.addEventListener('mouseout', stopDrawing);

    document.getElementById('color-picker').addEventListener('input', changeColor);
    document.getElementById('eraser-btn').addEventListener('click', toggleEraser);
    document.getElementById('clear-btn').addEventListener('click', clearCanvas);
    document.getElementById('reset-btn').addEventListener('click', resetDrawing);
    document.getElementById('submit-btn').addEventListener('click', submitDrawing);
    document.getElementById('view-btn').addEventListener('click', viewImage);
    document.getElementById('close-modal-btn').addEventListener('click', closeImageModal);

    // Start the game and load the reference image
    startGame();
});

function changeColor(event) {
    currentColor = event.target.value;
}

function toggleEraser() {
    drawingMode = !drawingMode;
    document.getElementById('eraser-btn').textContent = drawingMode ? "Eraser" : "Drawing";
}

function startDrawing(event) {
    if (!drawingMode) return;

    ctx.beginPath();
    ctx.moveTo(event.offsetX, event.offsetY);
    canvas.addEventListener('mousemove', draw);
}

function draw(event) {
    if (!drawingMode || !ctx) return;

    ctx.lineTo(event.offsetX, event.offsetY);
    ctx.strokeStyle = currentColor;
    ctx.lineWidth = 3;
    ctx.lineCap = 'round';
    ctx.stroke();
}

function stopDrawing() {
    ctx.closePath();
}

function clearCanvas() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
}

function resetDrawing() {
    clearCanvas();
    score = 0;
    document.getElementById('score').textContent = `Score: ${score}`;
    startGame();
}

function viewImage() {
    if (imageViewCount < 3) {
        document.getElementById('image-modal').style.display = 'flex';
        document.getElementById('modal-reference-image').src = referenceImageUrl;
        imageViewCount++;
    } else {
        alert("You have viewed the image 3 times already.");
    }
}

function closeImageModal() {
    document.getElementById('image-modal').style.display = 'none';
}

function startGame() {
    // Randomly select an image from the referenceImages array
    const randomImage = referenceImages[Math.floor(Math.random() * referenceImages.length)];
    referenceImageUrl = randomImage;

    document.getElementById('canvas-container').style.display = 'block';

    canvas.width = 800; // You can adjust canvas size as needed
    canvas.height = 600;

    // Draw the reference image on the canvas
    let img = new Image();
    img.src = referenceImageUrl;
    img.onload = function() {
        ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
    };

    document.getElementById('score').textContent = `Score: ${score}`;
}
</script>

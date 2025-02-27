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

#reference-image {
    transition: transform 0.5s ease-in-out;
}
</style>

<div class="container">
    <h2>Blind Trace Drawing Game</h2>

    <!-- Image Display Section -->
    <div id="image-display-container" class="canvas-container">
        <img id="reference-image" src="" alt="Reference Image" class="canvas">
        <button id="start-game-btn" class="tool-btn">Start Game</button>
    </div>

    <!-- Drawing Canvas Section -->
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

    <div id="image-modal" class="image-modal">
        <img id="modal-reference-image" />
        <button id="close-modal-btn" class="tool-btn">Close Image</button>
    </div>
</div>

<script type="module">
let referenceImageUrl = "";
let imageViewCount = 0;
let rotationAngle = 0;
let isImageRotating = false;
let drawing = false;
let erasing = false;
let ctx, canvas;

const referenceImages = [
    'assets/images/Bridge.jpg',
    'assets/images/car.jpg',
    'assets/images/colloseum.jpg',
    'assets/images/french.jpg',
    'assets/images/House.jpg',
    'assets/images/stonehenge.jpg',
    'assets/images/taj_mahal.jpg',
    'assets/images/tower.jpg',
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

    showReferenceImage();
});

function showReferenceImage() {
    const randomImage = referenceImages[Math.floor(Math.random() * referenceImages.length)];
    referenceImageUrl = randomImage;
    document.getElementById('reference-image').src = referenceImageUrl;
}

function startGame() {
    setTimeout(() => {
        rotateReferenceImage();
        isImageRotating = true;
        setTimeout(() => {
            document.getElementById('image-display-container').style.display = 'none';
            document.getElementById('canvas-section').style.display = 'block';
        }, 1000);
    }, 3000);
}

function rotateReferenceImage() {
    if (isImageRotating) return;
    rotationAngle += 90;
    rotationAngle %= 360;
    document.getElementById('reference-image').style.transform = `rotate(${rotationAngle}deg)`;
}

function viewImage() {
    if (imageViewCount < 3) {
        rotateReferenceImage();
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

function startDrawing(event) {
    drawing = true;
    ctx.beginPath();
    ctx.moveTo(event.offsetX, event.offsetY);
}

function draw(event) {
    if (!drawing) return;

    ctx.strokeStyle = erasing ? "#ffffff" : document.getElementById('color-picker').value;
    ctx.lineWidth = erasing ? 20 : 3;
    ctx.lineCap = "round";
    
    ctx.lineTo(event.offsetX, event.offsetY);
    ctx.stroke();
}

function stopDrawing() {
    drawing = false;
    ctx.closePath();
}

function toggleEraser() {
    erasing = !erasing;
    document.getElementById('eraser-btn').textContent = erasing ? "Drawing Mode" : "Eraser";
}

function clearCanvas() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
}

function resetDrawing() {
    clearCanvas();
    showReferenceImage();
}
</script>

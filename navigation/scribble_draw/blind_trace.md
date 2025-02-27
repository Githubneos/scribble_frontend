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

#start-button-container {
    text-align: center;
    margin-top: 2rem;
}

.tool-btn,
#view-btn {
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
    overflow: auto;
}

.image-modal img {
    max-width: 80%;
    max-height: 80%;
    border: 5px solid white;
    border-radius: 8px;
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
    <div id="score-container">
        <p id="score">Score: 0</p>
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
    document.getElementById('start-game-btn').addEventListener('click', startGame);
    document.getElementById('view-btn').addEventListener('click', viewImage);
    document.getElementById('close-modal-btn').addEventListener('click', closeImageModal);
    
    showReferenceImage();
});

function showReferenceImage() {
    const randomImage = referenceImages[Math.floor(Math.random() * referenceImages.length)];
    referenceImageUrl = randomImage;
    document.getElementById('reference-image').src = referenceImageUrl;
}

function startGame() {
    document.getElementById('image-display-container').style.display = 'none';
    document.getElementById('canvas-section').style.display = 'block';

    setTimeout(() => {
        rotateReferenceImage();
        isImageRotating = true;
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
</script>

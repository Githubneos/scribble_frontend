---
layout: needsAuth
title: Blind Trace Drawing Game
permalink: /blind-trace
search_exclude: true
---

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
    border: 1px solid #ccc;
    background: white;
    width: 80%;
    height: 400px;
    margin-bottom: 1rem;
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

.image-container {
    margin-bottom: 2rem;
    text-align: center;
}

.image-container img {
    max-width: 80%;
    margin-bottom: 1rem;
    display: block;
}
</style>

<div class="container">
    <h2>Blind Trace Drawing Game</h2>
    <div class="image-container">
        <img id="reference-image" src="" alt="Reference Image" style="display: none;">
    </div>
    <div class="canvas-container">
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
const imageUrls = [
    "https://media.istockphoto.com/id/512495588/photo/ah-shi-sle-pah.jpg?s=612x612&w=0&k=20&c=CImiuqBcipYsSkNCmAF3gXU0jOuoSwTxGw2zC_hrtLI=",
    "https://loveincorporated.blob.core.windows.net/contentimages/gallery/b8c7260b-8c13-4944-a006-6bfa005fdbb1-Landmark_Stonehenge.jpg",
    "https://worldwildschooling.com/wp-content/uploads/2024/05/Most-Famous-Landmarks-in-the-World-Taj-Mahal-Agra-India_©-AlexAnton_Adobe-Stock-Photo_344552598.jpg"
    // Add more direct URLs here
];

document.addEventListener('DOMContentLoaded', async () => {
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

    fetchReferenceImage();
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
    ctx.lineWidth = 5;
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
    fetchReferenceImage();
}

function viewImage() {
    const image = document.getElementById('reference-image');
    image.style.display = image.style.display === 'none' ? 'block' : 'none';
}

async function submitDrawing() {
    const drawingData = canvas.toDataURL('image/png');

    try {
        const response = await fetch(`${pythonURI}/api/submission`, {
            method: 'POST',
            credentials: 'include',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({
                image_url: referenceImageUrl,
                drawing: drawingData
            })
        });

        const data = await response.json();
        if (response.ok) {
            score = data.score;
            document.getElementById('score').textContent = `Score: ${score}`;
            showMessage('Drawing submitted and scored successfully!', 'success');
        } else {
            throw new Error(data.message);
        }
    } catch (error) {
        console.error('Error submitting drawing:', error);
        showMessage('Failed to submit drawing.', 'error');
    }
}

function showMessage(message, type) {
    const messageContainer = document.getElementById('message');
    messageContainer.textContent = message;
    messageContainer.className = `message ${type}`;
    messageContainer.style.display = 'block';

    setTimeout(() => {
        messageContainer.style.display = 'none';
    }, 5000);
}

function fetchReferenceImage() {
    // Cycle through the images in the array
    referenceImageUrl = imageUrls[imageIndex];
    document.getElementById('reference-image').src = referenceImageUrl;
    document.getElementById('reference-image').style.display = 'block'; // Make sure the image is visible

    // Adjust the image index for the next round
    imageIndex = (imageIndex + 1) % imageUrls.length;
}
</script>

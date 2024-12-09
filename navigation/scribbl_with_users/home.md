---
layout: post 
title: Scribbl With users
search_exclude: true
permalink: /home
menu: nav/scribbl_with_users.html
author: Zach
---

## Welcome to Scribbl With Users


<script>
document.addEventListener('DOMContentLoaded', () => {
  // Create the container for the drawing app
  const app = document.createElement('div');
  document.body.appendChild(app);

  app.style.cssText = `
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    background: linear-gradient(90deg, red, orange, yellow, green, blue, indigo, violet);
    margin: 0;
    overflow: hidden;
  `;

  // Create the canvas with a white background and black border
  const canvas = document.createElement('canvas');
  canvas.width = 500;
  canvas.height = 400;
  canvas.style.cssText = `
    background: white;
    border: 5px solid black;
    display: block;
    margin: 20px auto;
  `;
  app.appendChild(canvas);

  const ctx = canvas.getContext('2d');
  ctx.lineWidth = 5;
  ctx.lineCap = 'round';
  ctx.strokeStyle = 'black';

  const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'purple', 'pink', 'gray', 'brown', 'black'];
  let currentColor = 'black';
  let isDrawing = false;
  let lastX = 0;
  let lastY = 0;
  let drawingHistory = [];
  let isEraser = false;

  // Create the toolbar
  const toolbar = document.createElement('div');
  toolbar.style.cssText = `
    display: flex;
    justify-content: center;
    gap: 10px;
    flex-wrap: wrap;
    margin-top: 10px;
  `;
  app.appendChild(toolbar);

  // Add color buttons
  colors.forEach(color => {
    const button = document.createElement('button');
    button.style.cssText = `
      background-color: ${color};
      border: none;
      width: 30px;
      height: 30px;
      border-radius: 50%;
      cursor: pointer;
      outline: none;
    `;
    button.addEventListener('click', () => changeColor(color));
    toolbar.appendChild(button);
  });

  // Add Undo button
  const undoButton = document.createElement('button');
  undoButton.textContent = 'Undo';
  undoButton.style.cssText = `
    padding: 5px 15px;
    background: #444;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  `;
  undoButton.addEventListener('click', undo);
  toolbar.appendChild(undoButton);

  // Add Clear button
  const clearButton = document.createElement('button');
  clearButton.textContent = 'Clear All';
  clearButton.style.cssText = `
    padding: 5px 15px;
    background: #ff6a00;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  `;
  clearButton.addEventListener('click', clearCanvas);
  toolbar.appendChild(clearButton);

  // Add Eraser button
  const eraserButton = document.createElement('button');
  eraserButton.textContent = 'Eraser';
  eraserButton.style.cssText = `
    padding: 5px 15px;
    background: #666;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  `;
  eraserButton.addEventListener('click', toggleEraser);
  toolbar.appendChild(eraserButton);

  // Event listeners for drawing
  canvas.addEventListener('mousedown', (e) => {
    isDrawing = true;
    [lastX, lastY] = [e.offsetX, e.offsetY];
  });

  canvas.addEventListener('mousemove', (e) => {
    if (!isDrawing) return;
    ctx.beginPath();
    ctx.moveTo(lastX, lastY);
    ctx.lineTo(e.offsetX, e.offsetY);
    ctx.stroke();
    [lastX, lastY] = [e.offsetX, e.offsetY];
  });

  canvas.addEventListener('mouseup', () => {
    if (isDrawing) {
      isDrawing = false;
      saveDrawingState();
    }
  });

  canvas.addEventListener('mouseout', () => {
    if (isDrawing) {
      isDrawing = false;
      saveDrawingState();
    }
  });

  // Function to change color
  function changeColor(color) {
    isEraser = false;
    currentColor = color;
    ctx.strokeStyle = color;
  }

  // Function to toggle eraser
  function toggleEraser() {
    isEraser = !isEraser;
    ctx.strokeStyle = isEraser ? 'white' : currentColor;
  }

  // Function to save drawing state
  function saveDrawingState() {
    drawingHistory.push(canvas.toDataURL());
  }

  // Function to undo the last action
  function undo() {
    if (drawingHistory.length === 0) return;
    drawingHistory.pop();
    const lastState = drawingHistory[drawingHistory.length - 1];
    const img = new Image();
    img.src = lastState || '';
    img.onload = () => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.drawImage(img, 0, 0);
    };
  }

  // Function to clear the canvas
  function clearCanvas() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawingHistory = [];
  }
});
</script>

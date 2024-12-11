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
<<<<<<< HEAD
    const app = document.querySelector('#app');
    const toolbar = document.createElement('div');
    toolbar.style.cssText = `
        display: flex;
        justify-content: center;
        align-items: center;
        margin-bottom: 10px;
        background: rgba(255, 255, 255, 0.3);
        padding: 10px;
        border-radius: 10px;
        gap: 10px;
        flex-wrap: wrap;
=======
  // Create the container for the drawing app
  const apdp = document.createElement('div');
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
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0,0,0,0.5);
    display: block;
    margin: 20px auto;
  `;
  app.appendChild(canvas);

  const ctx = canvas.getContext('2d');
  ctx.lineWidth = 5;
  ctx.lineCap = 'round';
  ctx.strokeStyle = 'black';

  const colors = [
    '#FF0000', '#FF4500', '#FFA500', '#FFD700', // reds & oranges
    '#32CD32', '#00FF00', '#008000', '#00FA9A', // greens
    '#87CEEB', '#1E90FF', '#0000FF', '#000080', // blues
    '#800080', '#FF00FF', '#FF69B4', '#FFC0CB', // purples & pinks
    '#8B4513', '#A0522D', '#000000', '#FFFFFF'  // browns & basics
  ];
  let currentColor = 'black';
  let isDrawing = false;
  let lastX = 0;
  let lastY = 0;
  let drawingHistory = [];
  let isEraser = false;

  // Add brush size control
  let brushSizes = [2, 5, 10, 15, 20];
  let currentSize = 5;

  // Create the toolbar
  const toolbar = document.createElement('div');
  toolbar.style.cssText = `
    display: flex;
    justify-content: center;
    gap: 10px;
    flex-wrap: wrap;
    margin-top: 10px;
    background: rgba(255,255,255,0.2);
    padding: 15px;
    border-radius: 10px;
  `;
  app.appendChild(toolbar);

  // Add brush size buttons
  const brushSizeContainer = document.createElement('div');
  brushSizeContainer.style.cssText = `
    display: flex;
    gap: 5px;
    align-items: center;
    margin-left: 10px;
  `;
  
  brushSizes.forEach(size => {
    const button = document.createElement('button');
    button.style.cssText = `
      width: ${size + 10}px;
      height: ${size + 10}px;
      border-radius: 50%;
      background: #444;
      border: none;
      cursor: pointer;
>>>>>>> 3910c34bd943331787e9bdde7cf95cf0db516cc0
    `;
    button.addEventListener('click', () => changeBrushSize(size));
    brushSizeContainer.appendChild(button);
  });
  toolbar.appendChild(brushSizeContainer);

<<<<<<< HEAD
    const colorPicker = document.createElement('input');
    colorPicker.type = 'color';
    colorPicker.value = '#000000';
    colorPicker.style.cssText = `
        width: 40px;
        height: 40px;
        border: none;
        cursor: pointer;
    `;
    toolbar.appendChild(colorPicker);

    let currentColor = colorPicker.value;
    let isEraser = false;

    colorPicker.addEventListener('input', () => {
        currentColor = colorPicker.value;
        isEraser = false;
    });

    const brushSize = document.createElement('input');
    brushSize.type = 'range';
    brushSize.min = '1';
    brushSize.max = '50';
    brushSize.value = '5';
    brushSize.style.cssText = 'margin: 0 10px;';
    toolbar.appendChild(brushSize);

    const eraserButton = document.createElement('button');
    eraserButton.textContent = 'Eraser';
    eraserButton.style.cssText = `
        background: white;
        color: black;
        border: 2px solid #000;
        padding: 10px;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
    `;
    eraserButton.addEventListener('click', () => {
        isEraser = true;
    });
    toolbar.appendChild(eraserButton);

    const backgroundToggle = document.createElement('button');
    backgroundToggle.textContent = 'Toggle Background';
    backgroundToggle.style.cssText = `
        background: #000;
        color: white;
        border: none;
        padding: 10px;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
    `;
    toolbar.appendChild(backgroundToggle);

    const saveButton = document.createElement('button');
    saveButton.textContent = 'Save';
    saveButton.style.cssText = `
        background: #28a745;
        color: white;
        border: none;
        padding: 10px;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
    `;
    saveButton.addEventListener('click', saveDrawing);
    toolbar.appendChild(saveButton);

    const resetButton = document.createElement('button');
    resetButton.textContent = 'Reset';
    resetButton.style.cssText = `
        background: #dc3545;
        color: white;
        border: none;
        padding: 10px;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
    `;
    resetButton.addEventListener('click', resetCanvas);
    toolbar.appendChild(resetButton);

    const canvas = document.createElement('canvas');
    canvas.width = 800;
    canvas.height = 600;
    canvas.style.cssText = `
        border: 2px solid black;
        background: white;
        cursor: crosshair;
    `;
    const ctx = canvas.getContext('2d');
    let drawing = false;

    canvas.addEventListener('mousedown', (e) => {
        drawing = true;
        ctx.beginPath();
        ctx.moveTo(e.offsetX, e.offsetY);
    });

    canvas.addEventListener('mousemove', (e) => {
        if (drawing) {
            ctx.strokeStyle = isEraser ? 'white' : currentColor;
            ctx.lineWidth = brushSize.value;
            ctx.lineCap = 'round';
            ctx.lineTo(e.offsetX, e.offsetY);
            ctx.stroke();
        }
    });

    canvas.addEventListener('mouseup', () => {
        drawing = false;
        ctx.closePath();
    });

    canvas.addEventListener('mouseleave', () => {
        drawing = false;
    });

    function resetCanvas() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    }
=======
  // Update color buttons styling
  colors.forEach(color => {
    const button = document.createElement('button');
    button.style.cssText = `
      background-color: ${color};
      border: 2px solid ${color === '#FFFFFF' ? '#000000' : color};
      width: 30px;
      height: 30px;
      border-radius: 50%;
      cursor: pointer;
      outline: none;
      transition: transform 0.2s;
      &:hover {
        transform: scale(1.1);
      }
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
>>>>>>> 3910c34bd943331787e9bdde7cf95cf0db516cc0

  // Add touch support for mobile devices
  canvas.addEventListener('touchstart', handleTouchStart, false);
  canvas.addEventListener('touchmove', handleTouchMove, false);
  canvas.addEventListener('touchend', handleTouchEnd, false);

  function handleTouchStart(e) {
    e.preventDefault();
    const touch = e.touches[0];
    const rect = canvas.getBoundingClientRect();
    isDrawing = true;
    [lastX, lastY] = [
      touch.clientX - rect.left,
      touch.clientY - rect.top
    ];
  }

  function handleTouchMove(e) {
    e.preventDefault();
    if (!isDrawing) return;
    const touch = e.touches[0];
    const rect = canvas.getBoundingClientRect();
    const x = touch.clientX - rect.left;
    const y = touch.clientY - rect.top;
    
    ctx.beginPath();
    ctx.moveTo(lastX, lastY);
    ctx.lineTo(x, y);
    ctx.stroke();
    [lastX, lastY] = [x, y];
  }

  function handleTouchEnd(e) {
    e.preventDefault();
    if (isDrawing) {
      isDrawing = false;
      saveDrawingState();
    }
  }

<<<<<<< HEAD
    backgroundToggle.addEventListener('click', () => {
        canvas.style.background = canvas.style.background === 'black' ? 'white' : 'black';
    });

    app.appendChild(toolbar);
    app.appendChild(canvas);
});
</script>
<div id="app"></div>
=======
  // Function to change color
  function changeColor(color) {
    isEraser = false;
    currentColor = color;
    ctx.strokeStyle = color;
    ctx.lineWidth = currentSize;
  }

  // Function to toggle eraser
  function toggleEraser() {
    isEraser = !isEraser;
    if (isEraser) {
      ctx.strokeStyle = 'white';
      ctx.lineWidth = 20; // Larger size for eraser
    } else {
      ctx.strokeStyle = currentColor;
      ctx.lineWidth = currentSize;
    }
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

  // Add function to change brush size
  function changeBrushSize(size) {
    currentSize = size;
    ctx.lineWidth = size;
  }
});
</script>
>>>>>>> 3910c34bd943331787e9bdde7cf95cf0db516cc0

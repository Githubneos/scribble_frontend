---
layout: post 
title: Scribbl With Users
search_exclude: true
permalink: /home
menu: nav/scribbl_with_users.html
author: Zach
---

## Welcome to Scribbl With Users

<table>
    <tr>
        <td><a href="{{site.baseurl}}/chatroom">Chat with Friends</a></td>
        <td><a href="{{site.baseurl}}/Competition">Competitive</a></td>
        <td><a href="{{site.baseurl}}/leaderboard">Leaderboard</a></td>
    </tr>
</table>
<script>
document.addEventListener('DOMContentLoaded', () => {
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
  const toolbar = document.createElement('div');
  toolbar.style.cssText = `
    display: flex;
    justify-content: center;
    gap: 10px;
    flex-wrap: wrap;
    margin-top: 10px;
  `;
  app.appendChild(toolbar);
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
  const clearButton = document.createElement('button');
  clearButton.textContent = 'Clear All';
  clearButton.style.cssText = `
    padding: 5px 15px;
    background: #FF6A00;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  `;
  clearButton.addEventListener('click', clearCanvas);
  toolbar.appendChild(clearButton);
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
  function changeColor(color) {
    isEraser = false;
    currentColor = color;
    ctx.strokeStyle = color;
  }
  function toggleEraser() {
    isEraser = !isEraser;
    ctx.strokeStyle = isEraser ? 'white' : currentColor;
  }
  function saveDrawingState() {
    drawingHistory.push(canvas.toDataURL());
  }
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
  function clearCanvas() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawingHistory = [];
  }
});
</script>
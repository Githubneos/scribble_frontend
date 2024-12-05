---
layout: post 
title: Scribbl With users
search_exclude: true
permalink: /home
menu: nav/scribbl_with_users.html
author: Zach
---

## Welcome to Scribbl With Users

This media page allows you to express yourself with hand drawn images, along with attempting to guess what a user draws along with helpful hints along the way. Click through the navigation menu above to get started!

### Drawing Pad

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Drawing Pad</title>
</head>
<body>
  <h1>Welcome to the Drawing Pad</h1>

  <script>
    const canvas = document.createElement('canvas');
    canvas.width = 500;
    canvas.height = 400;
    document.body.appendChild(canvas);

    const ctx = canvas.getContext('2d');

  
    const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'purple', 'pink', 'gray', 'brown', 'black'];
    let currentColor = 'black';  
    let isDrawing = false;
    let lastX = 0;
    let lastY = 0;
    let drawingHistory = [];

   
    ctx.lineWidth = 5;
    ctx.lineCap = 'round';
    ctx.strokeStyle = currentColor;

   
    colors.forEach(color => {
      const button = document.createElement('button');
      button.style.backgroundColor = color;
      button.addEventListener('click', () => changeColor(color));
      document.body.appendChild(button);
    });

   
    const undoButton = document.createElement('button');
    undoButton.textContent = 'Undo Last Action';
    undoButton.addEventListener('click', undo);
    document.body.appendChild(undoButton);

   
    const clearButton = document.createElement('button');
    clearButton.textContent = 'Clear All';
    clearButton.addEventListener('click', clearCanvas);
    document.body.appendChild(clearButton);

  
    const eraserButton = document.createElement('button');
    eraserButton.textContent = 'Toggle Eraser';
    eraserButton.addEventListener('click', toggleEraser);
    document.body.appendChild(eraserButton);

  
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
      currentColor = color;
      ctx.strokeStyle = color;
    }


    let isEraser = false;
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
      img.src = lastState;
      img.onload = () => ctx.clearRect(0, 0, canvas.width, canvas.height);
      img.onload = () => ctx.drawImage(img, 0, 0);
    }

    function clearCanvas() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawingHistory = [];
    }
  </script>
</body>
</html>

<<<<<<< HEAD
=======
# Run the application
if __name__ == "__main__":
    root = tk.Tk()
    app = DrawingPad(root)
    root.mainloop()

<!-- <style>
  canvas {
    border: 1px solid black;
    display: block;
    margin: 20px auto;
  }
</style>
<canvas id="drawingCanvas" width="800" height="600"></canvas>
<script>
  // Select the canvas and get its context
  const canvas = document.getElementById('drawingCanvas');
  const ctx = canvas.getContext('2d');

  let isDrawing = false;

  // Event listeners for mouse actions
  canvas.addEventListener('mousedown', (e) => {
    isDrawing = true;
    ctx.beginPath();
    ctx.moveTo(e.offsetX, e.offsetY);
  });

  canvas.addEventListener('mousemove', (e) => {
    if (isDrawing) {
      ctx.lineTo(e.offsetX, e.offsetY);
      ctx.stroke();
    }
  });

  canvas.addEventListener('mouseup', () => {
    isDrawing = false;
    ctx.closePath();
  });

  canvas.addEventListener('mouseleave', () => {
    isDrawing = false;
  });
</script>
>
>>>>>>> 7a1434da93ef99802ed161acf1fbf84603677a44

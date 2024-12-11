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
    `;

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

    function saveDrawing() {
        const link = document.createElement('a');
        link.download = 'drawing.png';
        link.href = canvas.toDataURL();
        link.click();
    }

    backgroundToggle.addEventListener('click', () => {
        canvas.style.background = canvas.style.background === 'black' ? 'white' : 'black';
    });

    app.appendChild(toolbar);
    app.appendChild(canvas);
});
</script>
<div id="app"></div>

---
layout: post
title: Flocker Art
search_exclude: true
description: Guess the Drawing
hide: true
---

## Welcome to Scribbl With Users

<table>
    <tr>
        <td><a href="{{site.baseurl}}/competition">Comepetitive</a></td>
        <td><a href="{{site.baseurl}}/guessing">Guess Game </a></td>
        <td><a href="{{site.baseurl}}/leaderboard">LeaderBoard</a></td>
        <td><a href="{{site.baseurl}}/stats">Statistics</a></td>
        <td><a href="{{site.baseurl}}/about">About Us</a></td>
    </tr>
</table>

<div id="app"></div>
<div id="chat-container" style="margin-top: 20px;">
    <div id="messages" style="height: 200px; overflow-y: auto; border: 1px solid #ccc; padding: 10px; margin-bottom: 10px;"></div>
    <input type="text" id="message-input" placeholder="Type your message..." style="width: 80%; padding: 5px;">
    <button id="send-button" style="padding: 5px 10px;">Send</button>
</div>
<script>
document.addEventListener('DOMContentLoaded', () => {
    const app = document.querySelector('#app');
    if (!app) {
        console.error('Error: #app container not found. Ensure the div with id "app" is in the HTML.');
        return;
    }
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
    backgroundToggle.addEventListener('click', () => {
        canvas.style.background = canvas.style.background === 'black' ? 'white' : 'black';
    });
    toolbar.appendChild(backgroundToggle);
    const saveButton = document.createElement('button');
    saveButton.textContent = 'Save';
    saveButton.style.cssText = `
        background: #28A745;
        color: white;
        border: none;
        padding: 10px;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
    `;
    saveButton.addEventListener('click', () => {
        const link = document.createElement('a');
        link.download = 'drawing.jpeg';
        link.href = canvas.toDataURL();
        link.click();
    });
    toolbar.appendChild(saveButton);
    const resetButton = document.createElement('button');
    resetButton.textContent = 'Reset';
    resetButton.style.cssText = `
        background: #DC3545;
        color: white;
        border: none;
        padding: 10px;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
    `;
    resetButton.addEventListener('click', () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    });
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
    ctx.fillStyle = 'white';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
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
        const newBackground = canvas.style.background === 'black' ? 'white' : 'black';
        canvas.style.background = newBackground;
        ctx.fillStyle = newBackground;
        ctx.fillRect(0, 0, canvas.width, canvas.height);
    });
    const messageInput = document.getElementById('message-input');
    const sendButton = document.getElementById('send-button');
    const messagesDiv = document.getElementById('messages');
    function sendMessage() {
        const message = messageInput.value.trim();
        if (message) {
            const messageElement = document.createElement('div');
            messageElement.textContent = `You: ${message}`;
            messagesDiv.appendChild(messageElement);
            messagesDiv.scrollTop = messagesDiv.scrollHeight;
            messageInput.value = '';
        }
    }
    sendButton.addEventListener('click', sendMessage);
    messageInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') {
            sendMessage();
        }
    });
    app.appendChild(toolbar);
    app.appendChild(canvas);
});
</script>
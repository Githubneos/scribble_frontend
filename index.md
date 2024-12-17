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
    </tr>
</table>

<<<<<<< HEAD

## Welcome to Scribbl With Users

=======
>>>>>>> 639f913 (commit)
<div id="app"></div>
<div id="chat-container" style="margin-top: 20px;">
    <div id="messages" style="height: 200px; overflow-y: auto; border: 1px solid #ccc; padding: 10px; margin-bottom: 10px;"></div>
    <input type="text" id="message-input" placeholder="Type your message..." style="width: 80%; padding: 5px;">
    <button id="send-button" style="padding: 5px 10px;">Send</button>
</div>
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
<script>
document.addEventListener('DOMContentLoaded', () => {
    const app = document.querySelector('#app');
    if (!app) {
        console.error('Error: #app container not found. Ensure the div with id "app" is in the HTML.');
        return;
    }
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
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
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
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
<<<<<<< HEAD
    let currentColor = colorPicker.value;
    let isEraser = false;
=======

    let currentColor = colorPicker.value;
    let isEraser = false;

>>>>>>> 639f913 (commit)
    colorPicker.addEventListener('input', () => {
        currentColor = colorPicker.value;
        isEraser = false;
    });
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
    const brushSize = document.createElement('input');
    brushSize.type = 'range';
    brushSize.min = '1';
    brushSize.max = '50';
    brushSize.value = '5';
    brushSize.style.cssText = 'margin: 0 10px;';
    toolbar.appendChild(brushSize);
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
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
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
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
<<<<<<< HEAD
    const saveButton = document.createElement('button');
    saveButton.textContent = 'Save';
    saveButton.style.cssText = `
        background: #28A745;
=======

    const saveButton = document.createElement('button');
    saveButton.textContent = 'Save';
    saveButton.style.cssText = `
        background: #28a745;
>>>>>>> 639f913 (commit)
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
<<<<<<< HEAD
    const resetButton = document.createElement('button');
    resetButton.textContent = 'Reset';
    resetButton.style.cssText = `
        background: #DC3545;
=======

    const resetButton = document.createElement('button');
    resetButton.textContent = 'Reset';
    resetButton.style.cssText = `
        background: #dc3545;
>>>>>>> 639f913 (commit)
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
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
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
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
    canvas.addEventListener('mousedown', (e) => {
        drawing = true;
        ctx.beginPath();
        ctx.moveTo(e.offsetX, e.offsetY);
    });
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
    canvas.addEventListener('mousemove', (e) => {
        if (drawing) {
            ctx.strokeStyle = isEraser ? 'white' : currentColor;
            ctx.lineWidth = brushSize.value;
            ctx.lineCap = 'round';
            ctx.lineTo(e.offsetX, e.offsetY);
            ctx.stroke();
        }
    });
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
    canvas.addEventListener('mouseup', () => {
        drawing = false;
        ctx.closePath();
    });
<<<<<<< HEAD
    canvas.addEventListener('mouseleave', () => {
        drawing = false;
    });
    function resetCanvas() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    }
=======

    canvas.addEventListener('mouseleave', () => {
        drawing = false;
    });

    function resetCanvas() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

>>>>>>> 639f913 (commit)
    function saveDrawing() {
        const link = document.createElement('a');
        link.download = 'drawing.png';
        link.href = canvas.toDataURL();
        link.click();
    }
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
    backgroundToggle.addEventListener('click', () => {
        const newBackground = canvas.style.background === 'black' ? 'white' : 'black';
        canvas.style.background = newBackground;
        ctx.fillStyle = newBackground;
        ctx.fillRect(0, 0, canvas.width, canvas.height);
    });
<<<<<<< HEAD
    const messageInput = document.getElementById('message-input');
    const sendButton = document.getElementById('send-button');
    const messagesDiv = document.getElementById('messages');
=======

    const messageInput = document.getElementById('message-input');
    const sendButton = document.getElementById('send-button');
    const messagesDiv = document.getElementById('messages');

>>>>>>> 639f913 (commit)
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
<<<<<<< HEAD
=======

>>>>>>> 639f913 (commit)
    sendButton.addEventListener('click', sendMessage);
    messageInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') {
            sendMessage();
        }
    });
<<<<<<< HEAD
    app.appendChild(toolbar);
    app.appendChild(canvas);
});
</script>
=======

    app.appendChild(toolbar);
    app.appendChild(canvas);
});
</script>
>>>>>>> 639f913 (commit)

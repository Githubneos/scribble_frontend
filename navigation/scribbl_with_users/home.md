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
        background: linear-gradient(135deg, #FF9A8B, #FF6A88, #FF99AC, #845EC2, #D65DB1);
        margin: 0;
    `;
    const toolbar = document.createElement('div');
    toolbar.style.cssText = `
        display: flex;
        justify-content: center;
        margin-bottom: 10px;
    `;
    const colors = ['black', 'red', 'blue', 'green', 'purple', 'orange'];
    let currentColor = 'black';
    colors.forEach(color => {
        const button = document.createElement('button');
        button.style.cssText = `
            background: ${color};
            border: none;
            width: 30px;
            height: 30px;
            margin: 0 5px;
            border-radius: 50%;
            cursor: pointer;
            outline: none;
        `;
        button.addEventListener('click', () => {
            currentColor = color;
        });
        toolbar.appendChild(button);
    });
    const resetButton = document.createElement('button');
    resetButton.textContent = 'Reset';
    resetButton.style.cssText = `
        background: #FF6A88;
        color: white;
        border: none;
        padding: 5px 15px;
        border-radius: 5px;
        cursor: pointer;
        margin-left: 10px;
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
            ctx.strokeStyle = currentColor;
            ctx.lineWidth = 2;
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
    app.appendChild(toolbar);
    app.appendChild(canvas);
});
</script>
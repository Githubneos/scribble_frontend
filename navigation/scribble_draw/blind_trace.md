---
layout: needsAuth
title: Blind trace
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
    body {
        background: linear-gradient(145deg, #F7CFD8, #F4F8D3, #A6F1E0, #73C7C7);
    }
    .game-container {
        max-width: 800px;
        margin: 0 auto;
        padding: 20px;
    }
    .hint-section {
        margin: 20px 0;
        padding: 15px;
        background: #34495E;
        border-radius: 8px;
    }
    .input-field {
        padding: 8px;
        border-radius: 4px;
        border: 1px solid #34495E;
        width: 200px;
    }
    .button {
        padding: 8px 20px;
        background: #3498DB;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        margin: 5px;
    }
    .button:hover {
        background: #2980B9;
    }
    .stats-container {
        margin-top: 20px;
        padding: 20px;
        background: #34495E;
        border-radius: 10px;
        color: white;
    }
    #guess-canvas {
        border: 2px solid #34495E;
        border-radius: 8px;
        margin-bottom: 20px;
    }
</style>

<h1>Blind Trace Challenge</h1>
<p>Memorize the image, then redraw it from memory!</p>

<div id="image-container">
    <img id="target-image" src="" alt="Reference Image">
</div>

<div id="canvas-container">
    <canvas id="drawing-canvas" width="300" height="200"></canvas>
</div>

<button id="hide-btn">Start Challenge (Hide Image)</button>
<button id="reshow-btn" disabled>Reshow Image (3 left)</button>
<button id="submit-btn" disabled>Submit Drawing</button>
<button id="clear-btn">Clear Canvas</button>

<script>
    const images = [
        "https://www.google.com/url?sa=i&url=https%3A%2F%2Fpngtree.com%2Fso%2Fcar-side&psig=AOvVaw3zT8m70iBzhLfzlJrsuIwY&ust=1740701859843000&source=images&cd=vfe&opi=89978449&ved=0CBYQjRxqFwoTCMjenfLJ4osDFQAAAAAdAAAAABAP",
        "https://www.google.com/url?sa=i&url=https%3A%2F%2Fstock.adobe.com%2Fsearch%3Fk%3Dmountain&psig=AOvVaw3wnw-i9cthiSmoRyyjVKMp&ust=1740701922045000&source=images&cd=vfe&opi=89978449&ved=0CBYQjRxqFwoTCNCr3pDK4osDFQAAAAAdAAAAABAE",
        "https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.pexels.com%2Fsearch%2Flandmark%2F&psig=AOvVaw3lpWwoPjdNxXEcAsf4HHTa&ust=1740701960507000&source=images&cd=vfe&opi=89978449&ved=0CBYQjRxqFwoTCIiTqafK4osDFQAAAAAdAAAAABAK",
        "https://www.google.com/url?sa=i&url=https%3A%2F%2Fpixabay.com%2Fimages%2Fsearch%2Ffamous%2520landmark%2F&psig=AOvVaw3lpWwoPjdNxXEcAsf4HHTa&ust=1740701960507000&source=images&cd=vfe&opi=89978449&ved=0CBYQjRxqFwoTCIiTqafK4osDFQAAAAAdAAAAABAP",
        "https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.istockphoto.com%2Fphotos%2Finternational-landmark&psig=AOvVaw3lpWwoPjdNxXEcAsf4HHTa&ust=1740701960507000&source=images&cd=vfe&opi=89978449&ved=0CBYQjRxqFwoTCIiTqafK4osDFQAAAAAdAAAAABAg"
    ];

    let currentImageIndex = 0;
    let reshowCount = 3;

    const image = document.getElementById("target-image");
    const hideBtn = document.getElementById("hide-btn");
    const reshowBtn = document.getElementById("reshow-btn");
    const submitBtn = document.getElementById("submit-btn");
    const clearBtn = document.getElementById("clear-btn");
    const canvas = document.getElementById("drawing-canvas");
    const ctx = canvas.getContext("2d");

    function loadNewImage() {
        image.src = images[currentImageIndex];
        image.style.display = "block";
        reshowCount = 3;
        reshowBtn.innerText = `Reshow Image (${reshowCount} left)`;
        reshowBtn.disabled = false;
        hideBtn.disabled = false;
        submitBtn.disabled = true;
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    hideBtn.addEventListener("click", () => {
        image.style.display = "none";
        hideBtn.disabled = true;
        reshowBtn.disabled = false;
        submitBtn.disabled = false;
    });

    reshowBtn.addEventListener("click", () => {
        if (reshowCount > 0) {
            image.style.display = "block";
            setTimeout(() => {
                image.style.display = "none";
            }, 3000);

            reshowCount--;
            reshowBtn.innerText = `Reshow Image (${reshowCount} left)`;

            if (reshowCount === 0) {
                reshowBtn.disabled = true;
            }
        }
    });

    let drawing = false;
    canvas.addEventListener("mousedown", () => drawing = true);
    canvas.addEventListener("mouseup", () => { drawing = false; ctx.beginPath(); });
    canvas.addEventListener("mousemove", draw);

    function draw(event) {
        if (!drawing) return;

        ctx.lineWidth = 2;
        ctx.lineCap = "round";
        ctx.strokeStyle = "black";

        const rect = canvas.getBoundingClientRect();
        const x = event.clientX - rect.left;
        const y = event.clientY - rect.top;

        ctx.lineTo(x, y);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(x, y);
    }

    clearBtn.addEventListener("click", () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    });

    submitBtn.addEventListener("click", () => {
        alert("Drawing submitted! Next round starts.");
        currentImageIndex = (currentImageIndex + 1) % images.length;
        loadNewImage();
    });

    loadNewImage();
</script>


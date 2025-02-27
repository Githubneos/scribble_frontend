---
layout: needsAuth
title: Blind Trace
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
    .container {
        max-width: 800px;
        margin: 0 auto;
        padding: 20px;
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
    canvas {
        border: 2px solid #34495E;
        border-radius: 8px;
        margin-bottom: 20px;
    }
</style>
<h1>Blind Trace Challenge</h1>
<p>Memorize the image, then redraw it from memory!</p>
    <div id="image-container">
        <img id="target-image" src="" alt="Reference Image" width="300">
    </div>
    <canvas id="drawing-canvas" width="300" height="200"></canvas>
    <br>
    <button id="hide-btn" class="button">Start Challenge (Hide Image)</button>
    <button id="reshow-btn" class="button" disabled>Reshow Image (3 left)</button>
    <button id="submit-btn" class="button" disabled>Submit Drawing</button>
    <button id="clear-btn" class="button">Clear Canvas</button>
    <h2>Previous Submissions</h2>
    <table>
        <thead>
            <tr>
                <th>Image</th>
                <th>Score</th>
                <th>Submission Time</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody id="submissions-body"></tbody>
    </table>
</div>

<script>
    const pythonURI = "https://scribble.stu.nighthawkcodingsociety.com/submission";
    const images = [
        "https://thumbs.dreamstime.com/b/tower-bridge-sunset-popular-landmark-london-uk-29462158.jpg",
        "https://t3.ftcdn.net/jpg/02/82/38/10/360_F_282381041_O7uUYn2MgQHcltBSnRnVf2daOZDR9nmO.jpg"
    ];

    let currentImageIndex = 0;
    let reshowCount = 3;
    let startTime = null;

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
        startTime = null;
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    hideBtn.addEventListener("click", () => {
        image.style.display = "none";
        hideBtn.disabled = true;
        reshowBtn.disabled = false;
        submitBtn.disabled = false;
        startTime = Date.now();
    });

    reshowBtn.addEventListener("click", () => {
        if (reshowCount > 0) {
            image.style.display = "block";
            setTimeout(() => { image.style.display = "none"; }, 3000);
            reshowCount--;
            reshowBtn.innerText = `Reshow Image (${reshowCount} left)`;
            if (reshowCount === 0) reshowBtn.disabled = true;
        }
    });

    let drawing = false;
    canvas.addEventListener("mousedown", () => {
        drawing = true;
        if (!startTime) startTime = Date.now();
    });
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

    submitBtn.addEventListener("click", async () => {
        if (!startTime) {
            alert("You need to start drawing first!");
            return;
        }

        const timeSpent = (Date.now() - startTime) / 1000;
        const drawingData = canvas.toDataURL("image/png");
        const score = Math.max(0, 100 - Math.floor(timeSpent * 2));

        const submissionData = {
            image_url: image.src,
            drawing: drawingData,
            score: score
        };

        try {
            const response = await fetch(pythonURI, {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    "Authorization": `Bearer ${localStorage.getItem("token")}`
                },
                body: JSON.stringify(submissionData)
            });

            if (!response.ok) throw new Error("Failed to submit drawing.");

            alert(`Drawing submitted! Score: ${score}`);
            loadSubmissions();
        } catch (error) {
            alert("Failed to submit drawing.");
            console.error(error);
        }

        currentImageIndex = (currentImageIndex + 1) % images.length;
        loadNewImage();
    });

    async function loadSubmissions() {
        try {
            const response = await fetch(pythonURI, {
                method: "GET",
                headers: { "Authorization": `Bearer ${localStorage.getItem("token")}` }
            });

            const data = await response.json();
            updateSubmissionsTable(data.submissions);
        } catch (error) {
            console.error("Error fetching submissions:", error);
        }
    }

    async function deleteSubmission(id) {
        try {
            const response = await fetch(`${pythonURI}/${id}`, {
                method: "DELETE",
                headers: { "Authorization": `Bearer ${localStorage.getItem("token")}` }
            });

            if (!response.ok) throw new Error("Failed to delete submission.");

            alert("Submission deleted!");
            loadSubmissions();
        } catch (error) {
            alert("Error deleting submission.");
            console.error(error);
        }
    }

    function updateSubmissionsTable(submissions) {
        const tableBody = document.getElementById("submissions-body");
        tableBody.innerHTML = "";

        submissions.forEach(submission => {
            const row = document.createElement("tr");
            row.innerHTML = `
                <td><img src="${submission.drawing}" width="100"></td>
                <td>${submission.score} points</td>
                <td>${new Date(submission.submission_time).toLocaleString()}</td>
                <td><button class="button" onclick="deleteSubmission('${submission.id}')">Delete</button></td>
            `;
            tableBody.appendChild(row);
        });
    }

    document.addEventListener("DOMContentLoaded", () => {
        loadNewImage();
        loadSubmissions();
    });
</script>

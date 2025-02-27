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
        "https://thumbs.dreamstime.com/b/tower-bridge-sunset-popular-landmark-london-uk-29462158.jpg",
        "https://t3.ftcdn.net/jpg/02/82/38/10/360_F_282381041_O7uUYn2MgQHcltBSnRnVf2daOZDR9nmO.jpg",
        "https://www.pexels.com/photo/landmark-1796686/",
        "https://www.pexels.com/photo/landmark-1796685/",
        "https://www.pexels.com/photo/landmark-1796687/"
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

<script>
    const pythonURI = "https://scribble.stu.nighthawkcodingsociety.com";

    async function fetchSubmissions() {
        try {
            const response = await fetch(`${pythonURI}/blind_trace/submissions`);
            const data = await response.json();

            if (response.ok) {
                updateSubmissionsTable(data);
            } else {
                console.error("Failed to fetch submissions:", data.message);
            }
        } catch (error) {
            console.error("Error fetching submissions:", error);
        }
    }

    async function submitDrawing() {
        const drawingData = canvas.toDataURL("image/png"); // Convert drawing to Base64

        const payload = {
            profile_name: "User123", // Replace with actual logged-in user
            image_url: image.src,
            drawing_url: drawingData
        };

        try {
            const response = await fetch(`${pythonURI}/blind_trace/submit`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(payload),
            });

            const data = await response.json();

            if (response.ok) {
                alert("Drawing submitted!");
                loadNewImage();
                fetchSubmissions();
            } else {
                console.error("Submission failed:", data.message);
            }
        } catch (error) {
            console.error("Error submitting drawing:", error);
        }
    }

    async function updateDrawing(submissionId) {
        const drawingData = canvas.toDataURL("image/png"); // Convert updated drawing

        const payload = { drawing_url: drawingData };

        try {
            const response = await fetch(`${pythonURI}/blind_trace/update/${submissionId}`, {
                method: "PUT",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(payload),
            });

            const data = await response.json();

            if (response.ok) {
                alert("Drawing updated!");
                fetchSubmissions();
            } else {
                console.error("Update failed:", data.message);
            }
        } catch (error) {
            console.error("Error updating drawing:", error);
        }
    }

    async function deleteSubmission(submissionId) {
        try {
            const response = await fetch(`${pythonURI}/blind_trace/delete/${submissionId}`, {
                method: "DELETE",
            });

            if (response.ok) {
                alert("Submission deleted!");
                fetchSubmissions();
            } else {
                console.error("Delete failed:", await response.json());
            }
        } catch (error) {
            console.error("Error deleting submission:", error);
        }
    }

    function updateSubmissionsTable(submissions) {
        const tableBody = document.getElementById("submissions-body");
        tableBody.innerHTML = "";

        submissions.forEach((submission) => {
            const row = document.createElement("tr");

            row.innerHTML = `
                <td>${submission.profile_name}</td>
                <td><img src="${submission.image_url}" width="100"></td>
                <td><img src="${submission.drawing_url}" width="100"></td>
                <td>
                    <button class="button" onclick="updateDrawing(${submission.id})">Update</button>
                    <button class="button" onclick="deleteSubmission(${submission.id})" style="background: red;">Delete</button>
                </td>
            `;

            tableBody.appendChild(row);
        });
    }

    submitBtn.addEventListener("click", submitDrawing);

    document.addEventListener("DOMContentLoaded", fetchSubmissions);
</script>

<h2>Previous Submissions</h2>
<table border="1">
    <thead>
        <tr>
            <th>User</th>
            <th>Original Image</th>
            <th>Your Drawing</th>
            <th>Actions</th>
        </tr>
    </thead>
    <tbody id="submissions-body"></tbody>
</table>

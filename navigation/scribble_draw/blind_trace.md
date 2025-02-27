---
layout: needsAuth
title: Blind Trace
permalink: /blind_trace
menu: nav/home.html
search_exclude: true
---

<style>
    body {
        background: linear-gradient(145deg, #F7CFD8, #F4F8D3, #A6F1E0, #73C7C7);
        font-family: Arial, sans-serif;
    }
    .container {
        max-width: 800px;
        margin: 0 auto;
        padding: 20px;
        text-align: center;
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
    #drawing-canvas {
        border: 2px solid #34495E;
        border-radius: 8px;
        margin: 20px 0;
        background: white;
    }
    .table-container {
        margin-top: 20px;
        text-align: left;
    }
    table {
        width: 100%;
        border-collapse: collapse;
    }
    th, td {
        border: 1px solid black;
        padding: 8px;
        text-align: center;
    }
    .delete-btn {
        background: red;
        color: white;
        padding: 4px 8px;
        border: none;
        cursor: pointer;
    }
</style>

<div class="container">
    <h1>Blind Trace Challenge</h1>
    <p>Memorize the image, then redraw it from memory!</p>

    <div id="image-container">
        <img id="target-image" src="" alt="Reference Image" width="300">
    </div>

    <canvas id="drawing-canvas" width="300" height="200"></canvas>
    <br>
    <button id="hide-btn" class="button">Start Challenge (Hide Image)</button>
    <button id="submit-btn" class="button" disabled>Submit Drawing</button>
    <button id="clear-btn" class="button">Clear Canvas</button>

    <div class="table-container">
        <h2>Previous Submissions</h2>
        <table>
            <thead>
                <tr>
                    <th>Image</th>
                    <th>Score</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody id="submissions-body"></tbody>
        </table>
    </div>
</div>

<script>
    const pythonURI = "https://scribble.stu.nighthawkcodingsociety.com/api/blind_trace/submission";
    const token = localStorage.getItem("token");  // Retrieve stored token

    const image = document.getElementById("target-image");
    const hideBtn = document.getElementById("hide-btn");
    const submitBtn = document.getElementById("submit-btn");
    const clearBtn = document.getElementById("clear-btn");
    const canvas = document.getElementById("drawing-canvas");
    const ctx = canvas.getContext("2d");

    let drawing = false;
    let startTime = null;
    let currentImageUrl = "";

    function loadNewImage() {
        fetch("https://api.unsplash.com/photos/random?query=sketch&client_id=YOUR_UNSPLASH_API_KEY")
            .then(response => response.json())
            .then(data => {
                currentImageUrl = data.urls.small;
                image.src = currentImageUrl;
                image.style.display = "block";
                hideBtn.disabled = false;
                submitBtn.disabled = true;
                ctx.clearRect(0, 0, canvas.width, canvas.height);
            });
    }

    hideBtn.addEventListener("click", () => {
        image.style.display = "none";
        hideBtn.disabled = true;
        submitBtn.disabled = false;
        startTime = Date.now();
    });

    canvas.addEventListener("mousedown", () => { drawing = true; });
    canvas.addEventListener("mouseup", () => { drawing = false; ctx.beginPath(); });
    canvas.addEventListener("mousemove", draw);

    function draw(event) {
        if (!drawing) return;
        ctx.lineWidth = 2;
        ctx.lineCap = "round";
        ctx.strokeStyle = "black";
        const rect = canvas.getBoundingClientRect();
        ctx.lineTo(event.clientX - rect.left, event.clientY - rect.top);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(event.clientX - rect.left, event.clientY - rect.top);
    }

    clearBtn.addEventListener("click", () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
    });

    function getCanvasImage() {
        return canvas.toDataURL("image/png");
    }

    submitBtn.addEventListener("click", async () => {
        const drawingData = getCanvasImage();

        try {
            const response = await fetch(, {
                method: "POST",
                headers: {
                    "Authorization": `Bearer ${token}`,
                    "Content-Type": "application/json"
                },
                body: JSON.stringify({
                    image_url: currentImageUrl,
                    drawing: drawingData
                })
            });

            const data = await response.json();
            if (response.ok) {
                alert(`Score: ${data.score}`);
                fetchSubmissions();
                loadNewImage();
            } else {
                console.error("Submission failed:", data.message);
            }
        } catch (error) {
            console.error("Error submitting drawing:", error);
        }
    });

    async function fetchSubmissions() {
        try {
            const response = await fetch(API_URL, {
                method: "GET",
                headers: { "Authorization": `Bearer ${token}` }
            });

            const data = await response.json();
            if (response.ok) {
                displaySubmissions(data.submissions);
            } else {
                console.error("Failed to fetch submissions:", data.message);
            }
        } catch (error) {
            console.error("Error fetching submissions:", error);
        }
    }

    function displaySubmissions(submissions) {
        const tableBody = document.getElementById("submissions-body");
        tableBody.innerHTML = "";

        submissions.forEach(submission => {
            const row = document.createElement("tr");
            row.innerHTML = `
                <td><img src="${submission.image_url}" width="100"></td>
                <td>${submission.score}</td>
                <td><button class="delete-btn" onclick="deleteSubmission(${submission.id})">Delete</button></td>
            `;
            tableBody.appendChild(row);
        });
    }

    async function deleteSubmission(submissionId) {
        try {
            const response = await fetch(API_URL, {
                method: "DELETE",
                headers: {
                    "Authorization": `Bearer ${token}`,
                    "Content-Type": "application/json"
                },
                body: JSON.stringify({ id: submissionId })
            });

            const data = await response.json();
            if (response.ok) {
                fetchSubmissions();
            } else {
                console.error("Failed to delete submission:", data.message);
            }
        } catch (error) {
            console.error("Error deleting submission:", error);
        }
    }

    document.addEventListener("DOMContentLoaded", () => {
        loadNewImage();
        fetchSubmissions();
    });
</script>

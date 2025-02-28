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
:root {
    --background: linear-gradient(145deg, #A6AEBF, #C5D3E8, #D0E8C5, #FFF8DE);
}

body {
    background: var(--background);
    min-height: 100vh;
    margin: 0;
    padding: 0;
}

.container {
    max-width: 1200px;
    margin: 2rem auto;
    padding: 1rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.canvas-container {
    text-align: center;
    margin-bottom: 2rem;
}

.canvas {
    border: 2px solid #ccc;
    background-color: #fafafa;
    cursor: crosshair;
}

.tool-panel {
    display: flex;
    justify-content: center;
    gap: 1rem;
    margin-bottom: 1rem;
}

.tool-btn {
    padding: 0.5rem 1rem;
    border: none;
    background: #2196F3;
    color: white;
    font-size: 1rem;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s;
}

.tool-btn:hover {
    background: #1976D2;
}

.color-picker {
    margin-top: 1rem;
}

.image-modal {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.8);
    z-index: 1000;
    justify-content: center;
    align-items: center;
}

.image-modal img {
    max-width: 80%;
    max-height: 80%;
    border: 5px solid white;
    border-radius: 8px;
}

</style>

<div class="container">
    <h2>Blind Trace Drawing Game</h2>
    <div id="image-display-container" class="canvas-container">
        <img id="reference-image" src="" alt="Reference Image" class="canvas">
        <button id="start-game-btn" class="tool-btn">Start Game</button>
    </div>
    <div id="canvas-section" class="canvas-container" style="display: none;">
        <canvas id="drawing-canvas" class="canvas"></canvas>
        <div class="tool-panel">
            <button id="clear-btn" class="tool-btn">Clear Canvas</button>
            <button id="reset-btn" class="tool-btn">Reset Drawing</button>
            <button id="view-btn" class="tool-btn">View Image</button>
        </div>
    </div>
    <div class="color-picker">
        <label>Select Color:</label>
        <input type="color" id="color-picker" value="#000000">
    </div>
    <div class="tool-panel">
        <button id="eraser-btn" class="tool-btn">Eraser</button>
        <button id="submit-btn" class="tool-btn">Submit Drawing</button>
    </div>
    <div id="score-container">
        <p id="score">Score: 0</p>
    </div>
    <div id="message" class="message"></div>
    <div id="submissions-container"></div>
</div>

<div class="container mt-4">
    <h2>Past Blind Trace Submissions</h2>
    <table id="blindTraceTable" class="table table-striped">
        <thead>
            <tr>
                <th>User</th>
                <th>Score</th>
                <th>Drawing</th>
                <th>Submission Time</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody></tbody> 
    </table>
</div>

<style>
:root {
    --background: linear-gradient(145deg, #A6AEBF, #C5D3E8, #D0E8C5, #FFF8DE);
}

body {
    background: var(--background);
    min-height: 100vh;
    margin: 0;
    padding: 0;
}

.container {
    max-width: 1200px;
    margin: 2rem auto;
    padding: 1rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.table {
    width: 100%;
    border-collapse: collapse;
}

.table th, .table td {
    padding: 10px;
    border: 1px solid #ddd;
    text-align: center;
}

.tool-panel {
    display: flex;
    justify-content: center;
    gap: 1rem;
}

.tool-btn {
    padding: 0.5rem 1rem;
    border: none;
    background: #2196F3;
    color: white;
    font-size: 1rem;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s;
}

.tool-btn:hover {
    background: #1976D2;
}

.color-picker {
    margin-top: 1rem;
}
</style>

<script>
document.addEventListener('DOMContentLoaded', () => {
    loadReferenceImage();
});

function loadReferenceImage() {
    const imageElement = document.getElementById("reference-image");
    const imageList = [
        "images/Bridge.jpg",
        "images/car.png",
        "images/colloseum.jpg",
        "images/french.jpg",
        "images/House.png",
        "images.school_logo.png",
        "images/stonehenge.jpg",
        "images/taj_mahal.jpg",
        "images.tower.jpg"
    ];
    const randomImage = imageList[Math.floor(Math.random() * imageList.length)];
    imageElement.src = randomImage;
}

document.getElementById("start-game-btn").addEventListener("click", function () {
    document.getElementById("image-display-container").style.display = "none";
    document.getElementById("canvas-section").style.display = "block";
});

document.getElementById("view-btn").addEventListener("click", function () {
    const img = document.getElementById("reference-image");
    img.classList.toggle("hidden");
});
</script>

<script>
const pythonURI = "https://scribble.stu.nighthawkcodingsociety.com"; 

document.addEventListener('DOMContentLoaded', () => {
    loadPastSubmissions();
    loadPreviousGuesses();
});

async function loadPastSubmissions() {
    try {
        const response = await fetch(`${pythonURI}/api/submission`, {
            method: 'GET',
            headers: {
                'Authorization': `Bearer ${localStorage.getItem('auth_token')}`,
            },
        });

        const result = await response.json();
        if (response.ok) {
            displayPastSubmissions(result.submissions);
        } else {
            alert(`Error: ${result.message}`);
        }
    } catch (error) {
        alert('Failed to load past submissions.');
    }
}

function displayPastSubmissions(submissions) {
    const tableBody = document.querySelector('#blindTraceTable tbody');
    tableBody.innerHTML = ''; 

    submissions.forEach(submission => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>${submission.username}</td>
            <td>${submission.score}</td>
            <td>
                <a href="${submission.drawing_url}" target="_blank">
                    <img src="${submission.drawing_url}" alt="Drawing" width="50">
                </a>
            </td>
            <td>${new Date(submission.submission_time).toLocaleString()}</td>
            <td>
                <button class="btn btn-danger delete-btn" data-id="${submission.id}">Delete</button>
            </td>
        `;
        tableBody.appendChild(row);
    });

    if ($.fn.DataTable.isDataTable("#blindTraceTable")) {
        $("#blindTraceTable").DataTable().destroy();
    }
    
    $("#blindTraceTable").DataTable({
        order: [[1, 'desc']],
        pageLength: 10
    });
}

async function loadPreviousGuesses() {
    try {
        const response = await fetch(`${pythonURI}/api/guess`, {
            method: 'GET',
            headers: {
                'Authorization': `Bearer ${localStorage.getItem('auth_token')}`,
            },
        });

        const result = await response.json();
        if (response.ok) {
            displayPreviousGuesses(result.guesses);
        } else {
            alert(`Error: ${result.message}`);
        }
    } catch (error) {
        alert('Failed to load previous guesses.');
    }
}

function displayPreviousGuesses(guesses) {
    const tableBody = document.querySelector('#previousGuessesTable tbody');
    tableBody.innerHTML = '';

    guesses.forEach(guess => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>${guess.guesser_name}</td>
            <td>${guess.guess}</td>
            <td>${guess.correct_answer}</td>
            <td>${guess.is_correct ? '✔️' : '❌'}</td>
            <td>${new Date(guess.date_created).toLocaleString()}</td>
        `;
        tableBody.appendChild(row);
    });

    if ($.fn.DataTable.isDataTable("#previousGuessesTable")) {
        $("#previousGuessesTable").DataTable().destroy();
    }
    
    $("#previousGuessesTable").DataTable({
        order: [[4, 'desc']],
        pageLength: 10
    });
}

$(document).on("click", ".delete-btn", function() {
    var id = $(this).data("id");

    if (!confirm("Are you sure you want to delete this submission?")) {
        return;
    }

    fetch(`${pythonURI}/api/submission`, {
        method: "DELETE",
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${localStorage.getItem('auth_token')}`,
        },
        body: JSON.stringify({ id: id })
    })
    .then(response => response.json())
    .then(data => {
        if (data.message) {
            alert(data.message);
            loadPastSubmissions();
        } else {
            alert(data.error || "Operation failed");
        }
    })
    .catch(error => {
        alert("Failed to process request");
    });
});
</script>

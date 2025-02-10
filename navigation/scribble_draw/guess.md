---
layout: post
title: Scribble Guess
search_exclude: true
description: Scribble Guess
permalink: /guess
author: Keerthan
---
## Welcome to Scribble Guess

<table>
    <tr>
        <td><a href="{{site.baseurl}}/index">Home</a></td>
        <td><a href="{{site.baseurl}}/competition">Competitive</a></td>
        <td><a href="{{site.baseurl}}/guess">Guess Game</a></td>
        <td><a href="{{site.baseurl}}/leaderboard">LeaderBoard</a></td>
        <td><a href="{{site.baseurl}}/stats">Statistics</a></td>
        <td><a href="{{site.baseurl}}/about">About Us</a></td>
        <td><a href="{{site.baseurl}}/deploy">Deploy Blog</a></td>
    </tr>
</table>

<canvas id="drawingCanvas" width="500" height="400" style="border: 1px solid black; background: white;"></canvas>

<div id="controls">
  <button id="hintButton">Get Hint</button>
  <button id="resetButton">Reset</button>
  <form id="guessForm" style="margin-top: 20px;">
    <input type="text" id="user" placeholder="Your Name" required />
    <input type="text" id="guess" placeholder="Your Guess" required />
    <button type="submit">Submit Guess</button>
  </form>
</div>

<div id="hintArea">Hint: ???</div>
<div id="messageArea"></div>

<table id="guessTable" border="1">
  <thead>
    <tr>
      <th>User</th>
      <th>Guess</th>
      <th>Result</th>
      <th>Actions</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

<style>
    body {
        background: linear-gradient(145deg, #FCC6FF, #FFE6C9, #FFC785, #FFA09B);
    }
</style>

<script>
  const canvas = document.getElementById('drawingCanvas');
  const ctx = canvas.getContext('2d');
  const hintArea = document.getElementById('hintArea');
  const messageArea = document.getElementById('messageArea');
  const guessTableBody = document.querySelector('#guessTable tbody');

  let currentDrawing = null;
  let hintIndex = 0;

  const drawings = [
    { label: "car", hints: ["It has four wheels.", "Used for transportation."], draw: () => {
      ctx.fillStyle = "#FFFFFF"; ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = "blue"; ctx.fillRect(150, 250, 200, 50);
      ctx.fillStyle = "gray"; ctx.fillRect(170, 200, 160, 50);
    }},
    { label: "sun", hints: ["It is bright.", "Seen in the sky."], draw: () => {
      ctx.fillStyle = "#FFFFFF"; ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = "yellow"; ctx.beginPath(); ctx.arc(250, 200, 50, 0, Math.PI * 2); ctx.fill();
    }},
    { label: "tree", hints: ["Has leaves.", "Grows tall."], draw: () => {
      ctx.fillStyle = "#FFFFFF"; ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = "brown"; ctx.fillRect(230, 250, 40, 100);
      ctx.fillStyle = "green"; ctx.beginPath(); ctx.arc(250, 200, 60, 0, Math.PI * 2); ctx.fill();
    }}
  ];

  function startGame() {
    currentDrawing = drawings[Math.floor(Math.random() * drawings.length)];
    hintIndex = 0;
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    currentDrawing.draw();
    hintArea.textContent = "Hint: ???";
    messageArea.textContent = "";
    fetchGuesses(); // Fetch guesses when the game starts
  }

  document.getElementById('hintButton').addEventListener('click', () => {
    if (hintIndex < currentDrawing.hints.length) {
      hintArea.textContent = `Hint: ${currentDrawing.hints[hintIndex]}`;
      hintIndex++;
    } else {
      hintArea.textContent = "No more hints!";
    }
  });

  document.getElementById('resetButton').addEventListener('click', startGame);

  document.getElementById('guessForm').addEventListener('submit', async (e) => {
  e.preventDefault();
  const user = document.getElementById('user').value.trim();
  const guess = document.getElementById('guess').value.trim();
  const isCorrect = guess.toLowerCase() === currentDrawing.label.toLowerCase();

  try {
    const response = await fetch('http://127.0.0.1:8203/api/submit_guess', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ user, guess, is_correct: isCorrect }),
    });

    if (response.ok) {
      const result = await response.json();
      messageArea.textContent = result.message;
      fetchGuesses();  // Refresh the guess table after submission
    } else {
      const errorText = await response.text();
      throw new Error(`Request failed: ${errorText}`);
    }
  } catch (error) {
    console.error('Error:', error);
    messageArea.textContent = `Error: ${error.message}`;
  }
});

async function fetchGuesses() {
  try {
    const response = await fetch('http://127.0.0.1:8203/api/guesses', { method: 'GET' });
    if (!response.ok) throw new Error('Failed to fetch guesses');

    const guesses = await response.json(); // Assuming the server responds with an array of guesses
    updateGuessTable(guesses);  // Update the table with the fetched guesses
  } catch (error) {
    console.error('Error fetching guesses:', error);
    messageArea.textContent = `Error fetching guesses: ${error.message}`;
  }
}

function updateGuessTable(guesses) {
  guessTableBody.innerHTML = '';  // Clear the table before adding new rows

  guesses.forEach((guess) => {
    const row = document.createElement('tr');
    row.innerHTML = `
      <td>${guess.user}</td>
      <td>${guess.guess}</td>
      <td class="result">${guess.is_correct ? 'Correct' : 'Incorrect'}</td>
      <td>
        <button onclick="editGuess(this)">Edit</button>
        <button onclick="deleteGuess(this)">Delete</button>
      </td>
    `;
    guessTableBody.appendChild(row);
  });
}

async function editGuess(button) {
    const row = button.parentElement.parentElement;
    const guessCell = row.cells[1];
    const resultCell = row.querySelector('.result');
    const newGuess = prompt("Edit your guess:", guessCell.textContent);

    if (!newGuess) return;  // Cancel if no new guess is provided

    const isCorrect = newGuess.toLowerCase() === currentDrawing.label.toLowerCase();

    const requestBody = {
        user: row.cells[0].textContent,  // User's name (from the row)
        guess: newGuess,                 // The updated guess
        is_correct: isCorrect            // Whether the guess is correct or not
    };

    try {
        console.log("Sending PUT request...");

        const response = await fetch('http://127.0.0.1:8203/api/guesses', {
            method: 'PUT',
            headers: { 
                'Content-Type': 'application/json' 
            },
            body: JSON.stringify(requestBody)
        });

        console.log('Response:', response);

        if (!response.ok) {
            const errorData = await response.json();
            console.error('Error response from backend:', errorData);
            throw new Error(`Error: ${errorData.error || 'Unknown error'}`);
        }

        const updatedGuess = await response.json();
        console.log('Guess updated:', updatedGuess);

        // Update the UI with the new guess and result
        guessCell.textContent = updatedGuess.guess;  // Update the guess in the table
        resultCell.textContent = updatedGuess.is_correct ? 'Correct' : 'Incorrect';  // Update the result in the table

    } catch (error) {
        console.error('Error updating guess:', error);
        alert(`Error updating guess: ${error.message}`);
    }
}

async function deleteGuess(button) {
  const row = button.parentElement.parentElement;
  const user = row.cells[0].textContent;
  const guess = row.cells[1].textContent;

  try {
    const response = await fetch('http://127.0.0.1:8203/api/guesses', {
      method: 'DELETE',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ user, guess }),
    });

    if (!response.ok) throw new Error('Delete failed');
    row.remove();
  } catch (error) {
    messageArea.textContent = `Error deleting: ${error.message}`;
  }
}



  startGame();
</script>

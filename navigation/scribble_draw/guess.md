---
layout: post
title: Scribble Guess
search_exclude: true
description: Scribble Guess
permalink: /guess
author: Keerthan
---

<canvas id="drawingCanvas" width="500" height="400" style="border: 1px solid black;"></canvas>

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
<ul id="guessList"></ul>

<script>
  const canvas = document.getElementById('drawingCanvas');
  const ctx = canvas.getContext('2d');
  const hintArea = document.getElementById('hintArea');
  const messageArea = document.getElementById('messageArea');
  const guessList = document.getElementById('guessList');

  const drawings = [
    {
      label: "cat",
      hintSteps: [
        "It is a common pet.",
        "It says 'meow'.",
        "It has whiskers and pointy ears.",
        "It likes to chase mice.",
        "It can have different fur colors."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.beginPath();
        ctx.arc(250, 200, 50, 0, Math.PI * 2); // Head
        ctx.moveTo(220, 180);
        ctx.lineTo(240, 140); // Left ear
        ctx.lineTo(260, 180);
        ctx.moveTo(280, 180);
        ctx.lineTo(300, 140); // Right ear
        ctx.lineTo(320, 180);
        ctx.stroke();
        ctx.beginPath();
        ctx.arc(240, 210, 5, 0, Math.PI * 2); // Left eye
        ctx.arc(260, 210, 5, 0, Math.PI * 2); // Right eye
        ctx.moveTo(250, 220);
        ctx.lineTo(250, 230); // Nose to mouth
        ctx.moveTo(250, 230);
        ctx.lineTo(240, 240); // Left mouth
        ctx.moveTo(250, 230);
        ctx.lineTo(260, 240); // Right mouth
        ctx.stroke();
      }
    },
    {
      label: "dog",
      hintSteps: [
        "It is a loyal companion.",
        "It barks and wags its tail.",
        "It is often called 'man's best friend'.",
        "It comes in many breeds.",
        "It can be trained to do tricks."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.beginPath();
        ctx.arc(250, 200, 50, 0, Math.PI * 2); // Head
        ctx.moveTo(220, 180);
        ctx.lineTo(240, 140); // Left ear
        ctx.lineTo(260, 180);
        ctx.moveTo(280, 180);
        ctx.lineTo(300, 140); // Right ear
        ctx.lineTo(320, 180);
        ctx.stroke();
        ctx.beginPath();
        ctx.arc(240, 210, 5, 0, Math.PI * 2); // Left eye
        ctx.arc(260, 210, 5, 0, Math.PI * 2); // Right eye
        ctx.moveTo(250, 220);
        ctx.lineTo(250, 230); // Nose to mouth
        ctx.moveTo(250, 230);
        ctx.lineTo(240, 240); // Left mouth
        ctx.moveTo(250, 230);
        ctx.lineTo(260, 240); // Right mouth
        ctx.stroke();
      }
    },
    {
      label: "sun",
      hintSteps: [
        "It provides light and warmth.",
        "It is the center of our solar system.",
        "It is a star.",
        "It rises in the east and sets in the west.",
        "It shines brightly during the day."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.beginPath();
        ctx.arc(250, 200, 60, 0, Math.PI * 2); // Sun
        ctx.fillStyle = "yellow";
        ctx.fill();
        for (let i = 0; i < 12; i++) {
          const angle = (i * Math.PI) / 6;
          const x1 = 250 + Math.cos(angle) * 60;
          const y1 = 200 + Math.sin(angle) * 60;
          const x2 = 250 + Math.cos(angle) * 80;
          const y2 = 200 + Math.sin(angle) * 80;
          ctx.moveTo(x1, y1);
          ctx.lineTo(x2, y2);
        }
        ctx.stroke();
      }
    },
    {
      label: "house",
      hintSteps: [
        "It has a roof and walls.",
        "People live in it.",
        "It is a place for shelter.",
        "It has windows and doors.",
        "Some houses have gardens."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.fillStyle = '#8B4513';
        ctx.fillRect(180, 230, 140, 120); // Main body
        ctx.beginPath();
        ctx.moveTo(150, 230);
        ctx.lineTo(250, 150);
        ctx.lineTo(350, 230); // Roof
        ctx.closePath();
        ctx.fillStyle = '#D2691E';
        ctx.fill();
        ctx.fillStyle = '#FFF';
        ctx.fillRect(220, 270, 30, 50); // Door
      }
    },
    {
      label: "tree",
      hintSteps: [
        "It has a trunk and leaves.",
        "It is found in forests and parks.",
        "It can produce fruit or shade.",
        "It is green in spring and summer.",
        "Some trees lose their leaves in autumn."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.fillStyle = '#8B4513';
        ctx.fillRect(230, 270, 40, 80); // Trunk
        ctx.beginPath();
        ctx.arc(250, 200, 60, 0, Math.PI * 2); // Foliage
        ctx.fillStyle = "green";
        ctx.fill();
      }
    },
    {
      label: "cactus",
      hintSteps: [
        "It grows in dry, desert environments.",
        "It has spines instead of leaves.",
        "It stores water in its stem.",
        "It can be found in arid climates.",
        "It often has a thick, tall trunk."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.fillStyle = '#228B22'; // Cactus green
        ctx.fillRect(240, 200, 20, 100); // Main trunk
        ctx.fillRect(220, 150, 20, 50); // Left arm
        ctx.fillRect(260, 150, 20, 50); // Right arm
        ctx.fillRect(220, 240, 20, 50); // Left bottom arm
        ctx.fillRect(260, 240, 20, 50); // Right bottom arm
      }
    },
    {
      label: "mountain",
      hintSteps: [
        "It is a large landform that rises prominently above its surroundings.",
        "It is made of rock and earth.",
        "Mountains can have snow at the top.",
        "They are often found in ranges.",
        "They can be climbed or hiked."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.fillStyle = '#A9A9A9'; // Mountain gray
        ctx.beginPath();
        ctx.moveTo(100, 300);
        ctx.lineTo(250, 100); // Peak
        ctx.lineTo(400, 300);
        ctx.closePath();
        ctx.fill();
      }
    },
    {
      label: "ocean",
      hintSteps: [
        "It covers most of the Earth's surface.",
        "It is salty water.",
        "It has waves.",
        "Marine life thrives in it.",
        "You can swim and surf in it."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.fillStyle = '#1E90FF'; // Ocean blue
        ctx.fillRect(0, 250, canvas.width, 150); // Water
        for (let i = 0; i < 6; i++) {
          ctx.beginPath();
          ctx.arc(100 + i * 70, 280 + Math.sin(i) * 15, 20, 0, Math.PI * 2); // Waves
          ctx.fill();
        }
      }
    },
    {
      label: "desert",
      hintSteps: [
        "It is a dry, barren area.",
        "It receives very little rain.",
        "It has sand dunes.",
        "It is often very hot.",
        "Cacti and sparse vegetation are common."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.fillStyle = '#F4A300'; // Desert sand
        ctx.fillRect(0, 250, canvas.width, 150); // Sand dunes
        ctx.beginPath();
        ctx.moveTo(50, 250);
        ctx.lineTo(100, 220); // Dunes
        ctx.lineTo(150, 250);
        ctx.closePath();
        ctx.fill();
      }
    },
    {
      label: "city",
      hintSteps: [
        "It is a large human settlement.",
        "It has buildings, streets, and infrastructure.",
        "People live and work in it.",
        "It often has a lot of traffic.",
        "Skyscrapers are common in cities."
      ],
      draw: () => {
        ctx.fillStyle = "#FFFFFF";
        ctx.fillRect(0, 0, canvas.width, canvas.height); // Set canvas background to white
        ctx.fillStyle = '#808080'; // Building gray
        ctx.fillRect(100, 200, 50, 100); // Building 1
        ctx.fillRect(200, 150, 70, 150); // Building 2
        ctx.fillRect(300, 180, 60, 120); // Building 3
        ctx.fillStyle = '#000000'; // Windows
        ctx.fillRect(110, 220, 20, 20); // Window 1
        ctx.fillRect(230, 170, 20, 20); // Window 2
        ctx.fillRect(310, 200, 20, 20); // Window 3
      }
    }
  ];

  let currentDrawing = null;
  let hintIndex = 0;

  function startGame() {
    currentDrawing = drawings[Math.floor(Math.random() * drawings.length)];
    hintIndex = 0;

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    currentDrawing.draw();
    hintArea.textContent = "Hint: ???";
    messageArea.textContent = "";
  }

  document.getElementById('hintButton').addEventListener('click', () => {
    if (hintIndex < currentDrawing.hintSteps.length) {
      hintArea.textContent = `Hint: ${currentDrawing.hintSteps[hintIndex]}`;
      hintIndex++;
    } else {
      hintArea.textContent = "No more hints!";
    }
  });

  document.getElementById('resetButton').addEventListener('click', () => {
    startGame();
  });

  document.getElementById('guessForm').addEventListener('submit', async (e) => {
    e.preventDefault();

    const user = document.getElementById('user').value.trim();
    const guess = document.getElementById('guess').value.trim();
    const is_correct = guess.toLowerCase() === currentDrawing.label.toLowerCase();

    const guessData = { user, guess, is_correct };

    try {
      console.log('Sending guess data:', guessData); // Log the guess data being sent

      const response = await fetch('http://127.0.0.1:8887/api/submit_guess', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(guessData),
      });

      if (!response.ok) {
        throw new Error('Network response was not ok');
      }

      const result = await response.json();
      console.log('Response from server:', result); // Log the server response

      messageArea.textContent = result.message;

      if (is_correct) {
        guessList.innerHTML += `<li><strong>${user}</strong> guessed correctly: ${guess}</li>`;
      } else {
        guessList.innerHTML += `<li><strong>${user}</strong> guessed incorrectly: ${guess}</li>`;
      }
    } catch (error) {
      console.error('Error submitting guess:', error);
      messageArea.textContent = `There was an error submitting your guess. Please try again. Error: ${error.message}`;
    }
  });

  // Initialize the game on load
  startGame();
</script>

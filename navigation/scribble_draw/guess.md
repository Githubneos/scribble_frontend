---
layout: post
title: Scribble Guess
search_exclude: true
description: Chatroom
permalink: /guessing
Author: Keerthan
---
<div>
    <style>
        canvas {
            border: 2px solid #000;
            display: block;
            margin: 20px auto;
            background-color: #f4f4f9;
        }

        .controls {
            text-align: center;
            margin: 10px;
        }

        .gallery {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 10px;
            margin-top: 20px;
        }

        .gallery img {
            width: 100px;
            height: 100px;
            object-fit: cover;
            border: 2px solid #000;
            cursor: pointer;
        }

        .hint {
            font-size: 1.2em;
            color: #333;
            text-align: center;
            margin-top: 10px;
        }
    </style>

    <canvas id="drawingCanvas" width="500" height="500"></canvas>

    <div class="controls">
        <button id="saveDrawing">Save Drawing</button>
        <button id="clearCanvas">Clear Canvas</button>
    </div>

    <div class="gallery" id="drawingGallery"></div>

    <div class="controls">
        <input type="text" id="guessInput" placeholder="Guess what it is">
        <button id="submitGuess">Submit Guess</button>
    </div>

    <div class="hint" id="hintArea"></div>
</div>

<script>
    const canvas = document.getElementById('drawingCanvas');
    const ctx = canvas.getContext('2d');
    const saveButton = document.getElementById('saveDrawing');
    const clearButton = document.getElementById('clearCanvas');
    const gallery = document.getElementById('drawingGallery');
    const guessInput = document.getElementById('guessInput');
    const submitGuess = document.getElementById('submitGuess');
    const hintArea = document.getElementById('hintArea');

    let isDrawing = false;
    let drawings = [];
    let currentDrawing = null;

    const hints = {
        "cat": "It's a popular pet with whiskers.",
        "dog": "Man's best friend.",
        "house": "It's where people live.",
        "car": "A vehicle with four wheels.",
        "tree": "It has leaves and grows in forests."
    };

    canvas.addEventListener('mousedown', () => isDrawing = true);
    canvas.addEventListener('mouseup', () => isDrawing = false);
    canvas.addEventListener('mousemove', draw);

    function draw(event) {
        if (!isDrawing) return;

        ctx.lineWidth = 2;
        ctx.lineCap = 'round';
        ctx.strokeStyle = '#000';

        ctx.lineTo(event.offsetX, event.offsetY);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(event.offsetX, event.offsetY);
    }

    clearButton.addEventListener('click', () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.beginPath();
    });

    saveButton.addEventListener('click', () => {
        const drawing = canvas.toDataURL();
        const label = prompt("What is this drawing?").toLowerCase();
        if (!label) return;

        drawings.push({ label, drawing });
        displayGallery();
        clearButton.click();
    });

    function displayGallery() {
        gallery.innerHTML = '';
        drawings.forEach((entry, index) => {
            const img = document.createElement('img');
            img.src = entry.drawing;
            img.dataset.label = entry.label;
            img.dataset.index = index;

            img.addEventListener('click', () => {
                currentDrawing = entry;
                hintArea.textContent = "";
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                const image = new Image();
                image.src = entry.drawing;
                image.onload = () => ctx.drawImage(image, 0, 0);
            });

            gallery.appendChild(img);
        });
    }

    submitGuess.addEventListener('click', () => {
        if (!currentDrawing) {
            alert("Please select a drawing first.");
            return;
        }

        const guess = guessInput.value.trim().toLowerCase();
        if (guess === currentDrawing.label) {
            alert("Correct! You guessed it.");
            hintArea.textContent = "";
        } else {
            alert("Try again!");
        }

        guessInput.value = '';
    });

    // Hint functionality
    function giveHint(label) {
        if (hints[label]) {
            hintArea.textContent = `Hint: ${hints[label]}`;
        } else {
            hintArea.textContent = "No hints available for this drawing.";
        }
    }

    gallery.addEventListener('click', (event) => {
        if (event.target.tagName === 'IMG') {
            const label = event.target.dataset.label;
            giveHint(label);
        }
    });
</script>

<script>
const setupDiv = document.createElement("div");
const secretWordLabel = document.createElement("label");
secretWordLabel.textContent = "Enter the secret word:";
const secretWordInput = document.createElement("input");
secretWordInput.type = "password";
const startButton = document.createElement("button");
startButton.textContent = "Start Game";
setupDiv.appendChild(secretWordLabel);
setupDiv.appendChild(secretWordInput);
setupDiv.appendChild(startButton);
document.body.appendChild(setupDiv);

const gameDiv = document.createElement("div");
gameDiv.style.display = "none";
const hiddenWordElement = document.createElement("p");
hiddenWordElement.className = "hidden-word";
const hintElement = document.createElement("p");
hintElement.className = "hint";
const messageElement = document.createElement("p");
messageElement.className = "message";
const guessInput = document.createElement("input");
const submitGuessButton = document.createElement("button");
submitGuessButton.textContent = "Submit Guess";
const restartGameButton = document.createElement("button");
restartGameButton.textContent = "Restart Game";
restartGameButton.style.display = "none";

gameDiv.appendChild(hiddenWordElement);
gameDiv.appendChild(guessInput);
gameDiv.appendChild(submitGuessButton);
gameDiv.appendChild(hintElement);
gameDiv.appendChild(messageElement);
gameDiv.appendChild(restartGameButton);
document.body.appendChild(gameDiv);

let secretWord = "";
let hiddenWord = "";
let maxAttempts = 5;
let attemptsLeft = maxAttempts;
let hintRevealed = 0;

function hideWord(word) {
    return '*'.repeat(word.length);
}

function giveHint(word, revealedCount) {
    const hintArray = Array.from(hiddenWord);
    const revealIndices = new Set();
    while (revealIndices.size < revealedCount) {
        revealIndices.add(Math.floor(Math.random() * word.length));
    }
    revealIndices.forEach(index => {
        hintArray[index] = word[index];
    });
    return hintArray.join('');
}

function resetGame() {
    secretWord = "";
    hiddenWord = "";
    attemptsLeft = maxAttempts;
    hintRevealed = 0;
    hiddenWordElement.textContent = "";
    hintElement.textContent = "";
    messageElement.textContent = "";
    guessInput.value = "";
    setupDiv.style.display = "block";
    gameDiv.style.display = "none";
    restartGameButton.style.display = "none";
}

function handleGuess() {
    const guess = guessInput.value.trim().toLowerCase();
    guessInput.value = "";

    if (!guess) {
        messageElement.textContent = "Please enter a guess.";
        return;
    }

    if (guess === secretWord) {
        messageElement.style.color = "green";
        messageElement.textContent = "Congratulations! You've guessed the word!";
        restartGameButton.style.display = "inline-block";
        return;
    }

    attemptsLeft--;
    messageElement.style.color = "red";
    messageElement.textContent = `Wrong guess. Attempts left: ${attemptsLeft}.`;

    if (attemptsLeft <= 0) {
        messageElement.textContent = `Game Over! The correct word was: "${secretWord}"`;
        restartGameButton.style.display = "inline-block";
    } else if (attemptsLeft <= maxAttempts - Math.ceil(secretWord.length * 0.25)) {
        hintRevealed = Math.ceil(secretWord.length * 0.25);
        hiddenWord = giveHint(secretWord, hintRevealed);
        hiddenWordElement.textContent = hiddenWord;
        hintElement.textContent = `Hint: ${hiddenWord}`;
    }
}

startButton.addEventListener("click", () => {
    secretWord = secretWordInput.value.trim().toLowerCase();
    if (secretWord) {
        hiddenWord = hideWord(secretWord);
        hiddenWordElement.textContent = hiddenWord;
        attemptsLeft = maxAttempts;
        hintRevealed = 0;
        hintElement.textContent = "";
        messageElement.textContent = "";
        setupDiv.style.display = "none";
        gameDiv.style.display = "block";
    } else {
        alert("Please enter a valid word!");
    }
});

submitGuessButton.addEventListener("click", handleGuess);
guessInput.addEventListener("keypress", (event) => {
    if (event.key === "Enter") {
        event.preventDefault(); 
        handleGuess();
    }
});

restartGameButton.addEventListener("click", resetGame);
</script>



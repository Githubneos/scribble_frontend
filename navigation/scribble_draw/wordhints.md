---
layout: post
title: Word Hints
search_exclude: true
description: Word Hints
permalink: /wordhints
menu: /nav/scribble_draw
author: Ian
---


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



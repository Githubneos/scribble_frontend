---
layout: post
title: Word Hints
menu: nav/scribbl_with_users.html
permalink: /scribbl_with_users/word_hints/
author: Ian
---
<div id="setup">
    <label for="secret-word">Enter the secret word:</label>
    <input type="password" id="secret-word">
    <button id="start-game">Start Game</button>
</div>

<div id="game" style="display: none;">
    <p class="hidden-word" id="hidden-word"></p>
    <label for="guess-input">Your guess:</label>
    <input type="text" id="guess-input">
    <button id="submit-guess">Submit Guess</button>
    <p class="hint" id="hint"></p>
    <p class="message" id="message"></p>
    <button id="restart-game" style="display: none;">Restart Game</button>
</div>

<style>
    body {
        font-family: Arial, sans-serif;
        margin: 20px;
        text-align: center;
    }
    .hidden-word {
        font-size: 24px;
        margin: 20px 0;
    }
    .hint {
        color: blue;
        margin-top: 10px;
    }
    .message {
        color: red;
        margin-top: 10px;
    }
    #restart-game {
        margin-top: 20px;
        font-size: 16px;
        padding: 10px 20px;
    }
</style>

<script>
    const setupDiv = document.getElementById("setup");
    const gameDiv = document.getElementById("game");
    const hiddenWordElement = document.getElementById("hidden-word");
    const hintElement = document.getElementById("hint");
    const messageElement = document.getElementById("message");
    const guessInput = document.getElementById("guess-input");
    const submitGuessButton = document.getElementById("submit-guess");
    const restartGameButton = document.getElementById("restart-game");

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

    document.getElementById("start-game").addEventListener("click", () => {
        secretWord = document.getElementById("secret-word").value.trim().toLowerCase();
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

    // Allow pressing Enter to submit the guess
    guessInput.addEventListener("keypress", (event) => {
        if (event.key === "Enter") {
            event.preventDefault(); // Prevent form submission or default behavior
            handleGuess();
        }
    });

    restartGameButton.addEventListener("click", resetGame);
</script>

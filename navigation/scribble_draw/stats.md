---
layout: post
title: Scribble Stats
search_exclude: true
description: Stats page
permalink: /stats
Author: Max
---

<div>
    <style>
        /* General Body Styling */
        body {
            font-family: 'Poppins', sans-serif;
            margin: 0;
            padding: 0;
            background: #121212;
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        /* Statistics Container */
        .statistics-container {
            width: 90%;
            min-width: 1200px;
            max-width: 1200px;
            background: linear-gradient(145deg, #1e1e1e, #2a2a2a);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.6);
            text-align: center;
        }
        /* Header */
        .statistics-header {
            margin-bottom: 40px;
        }
        .statistics-header h1 {
            font-size: 2.8rem;
            font-weight: bold;
            color: #ffcc00;
        }
        /* Statistics Grid */
        .statistics-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
        }
        /* Stat Card */
        .stat-card {
            background: linear-gradient(145deg, #2d2d2d, #252525);
            border-radius: 15px;
            padding: 30px;
            text-align: center;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.5);
            transition: transform 0.3s, box-shadow 0.3s;
            cursor: pointer;
            position: relative;
        }
        .stat-card:hover {
            transform: scale(1.05);
            box-shadow: 0 12px 30px rgba(0, 0, 0, 0.7);
        }
        .stat-emoji {
            font-size: 3rem;
            margin-bottom: 10px;
        }
        .stat-value {
            font-size: 3rem;
            font-weight: bold;
            color: #00ffcc;
        }
        .stat-label {
            font-size: 1.4rem;
            color: #ffcc00;
        }
        /* Hint Section */
        .hint {
            margin-top: 40px;
            font-size: 1.3rem;
            color: #ffcc00;
            font-style: italic;
        }
        /* Modal Styling */
        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            z-index: 999;
            display: none;
        }
        .modal {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 90%;
            max-width: 400px;
            background: linear-gradient(145deg, #1e1e1e, #2a2a2a);
            border-radius: 15px;
            padding: 20px;
            text-align: center;
            box-shadow: 0 12px 35px rgba(0, 0, 0, 0.6);
            z-index: 1000;
            display: none;
        }
        .modal h2 {
            font-size: 1.8rem;
            margin-bottom: 15px;
            color: #00ffcc;
        }
        .modal p {
            font-size: 1.2rem;
            color: #fff;
        }
        .modal .close-btn {
            background: #ff5555;
            color: #fff;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 20px;
            transition: background 0.3s;
        }
        .modal .close-btn:hover {
            background: #ff3333;
        }
    </style>
    <!-- Statistics Container -->
    <div class="statistics-container">
        <div class="statistics-header">
            <h1>🎮 Game Statistics 🎯</h1>
        </div>
        <div class="statistics-grid">
            <div class="stat-card" onclick="showDetail('totalRounds')">
                <div class="stat-emoji">🏆</div>
                <div class="stat-value" id="total-rounds">0</div>
                <div class="stat-label">Total Rounds</div>
            </div>
            <div class="stat-card" onclick="showDetail('correctGuesses')">
                <div class="stat-emoji">✅</div>
                <div class="stat-value" id="correct-guesses">0</div>
                <div class="stat-label">Correct Guesses</div>
            </div>
            <div class="stat-card" onclick="showDetail('wrongGuesses')">
                <div class="stat-emoji">❌</div>
                <div class="stat-value" id="wrong-guesses">0</div>
                <div class="stat-label">Wrong Guesses</div>
            </div>
            <div class="stat-card" onclick="showDetail('hintUses')">
                <div class="stat-emoji">💡</div>
                <div class="stat-value" id="hint-uses">0</div>
                <div class="stat-label">Hints Used</div>
            </div>
            <div class="stat-card" onclick="showDetail('streaks')">
                <div class="stat-emoji">🔥</div>
                <div class="stat-value" id="streaks">0</div>
                <div class="stat-label">Current Streak</div>
            </div>
        </div>
        <div class="hint" id="hint">🌟 Use hints wisely to boost your score! 🌟</div>
    </div>
    <!-- Modal and Overlay -->
    <div class="overlay" id="overlay" onclick="closeModal()"></div>
    <div class="modal" id="modal">
        <h2 id="modal-title">Detail</h2>
        <p id="modal-detail">Detailed information will appear here.</p>
        <button class="close-btn" onclick="closeModal()">Close</button>
    </div>
    <script>
        const statistics = {
            totalRounds: 10,
            correctGuesses: 7,
            wrongGuesses: 3,
            hintUses: 2,
            streaks: 5
        };
        const details = {
            totalRounds: "The total number of rounds you've played in the game so far.",
            correctGuesses: "The number of times you guessed correctly during the game.",
            wrongGuesses: "The number of incorrect guesses made during gameplay.",
            hintUses: "The total number of hints you used to help with your guesses.",
            streaks: "The highest number of consecutive correct guesses in the game."
        };
        const hints = [
            "🌟 Use hints wisely to boost your score! 🌟",
            "🎯 Every correct guess increases your streak! Keep it up! 🔥",
            "🔄 Remember: each round is a chance to improve! 🏆",
            "💡 Hints help, but too many can cost you points! ⚖️",
            "🏅 A good strategy is key to success! Plan your guesses! 🧠",
            "🎉 Don't forget to celebrate your correct guesses! 🎉",
            "⏳ Time is precious! Use your hints strategically! ⏱️"
        ];
        function updateStatistics() {
            document.getElementById("total-rounds").textContent = statistics.totalRounds;
            document.getElementById("correct-guesses").textContent = statistics.correctGuesses;
            document.getElementById("wrong-guesses").textContent = statistics.wrongGuesses;
            document.getElementById("hint-uses").textContent = statistics.hintUses;
            document.getElementById("streaks").textContent = statistics.streaks;
        }
        function setRandomHint() {
            const randomIndex = Math.floor(Math.random() * hints.length);
            document.getElementById("hint").textContent = hints[randomIndex];
        }
        function showDetail(stat) {
            const modal = document.getElementById("modal");
            const overlay = document.getElementById("overlay");
            document.getElementById("modal-title").textContent = stat
                .replace(/([A-Z])/g, " $1")
                .replace(/^./, (str) => str.toUpperCase());
            document.getElementById("modal-detail").textContent = details[stat];
            modal.style.display = "block";
            overlay.style.display = "block";
        }
        function closeModal() {
            document.getElementById("modal").style.display = "none";
            document.getElementById("overlay").style.display = "none";
        }
        setRandomHint();
        updateStatistics();
    </script>
</div>

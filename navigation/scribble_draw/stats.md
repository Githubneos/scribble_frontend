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
        .statistics-container {
            width: 90%;
            max-width: 1200px;
            background: linear-gradient(145deg, #1e1e1e, #2a2a2a);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 0 50px rgba(0,0,0,0.7);
            text-align: center;
        }
        .statistics-header {
            text-align: center;
            margin-bottom: 40px;
        }
        .statistics-header h1 {
            font-size: 3rem;
            font-weight: bold;
        }
        .statistics-grid {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;
            gap: 30px;
        }
        .stat-card {
            background: linear-gradient(145deg, #2d2d2d, #252525);
            border-radius: 15px;
            padding: 40px;
            text-align: center;
            box-shadow: 0 8px 25px rgba(0,0,0,0.5);
            transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
            width: 250px;
            cursor: pointer;
            position: relative;
        }
        .stat-card:hover {
            transform: scale(1.1);
            box-shadow: 0 12px 35px rgba(0,0,0,0.7);
        }
        .stat-value {
            font-size: 4rem;
            font-weight: bold;
            color: #00ffcc;
        }
        .stat-label {
            margin-top: 10px;
            font-size: 1.4rem;
            color: #ffcc00;
        }
        .stat-emoji {
            font-size: 3rem;
            position: absolute;
            top: -30px;
            right: -30px;
        }
        .hint {
            margin-top: 40px;
            text-align: center;
            font-size: 1.5rem;
            color: #ffcc00;
            font-style: italic;
        }
        @media (max-width: 768px) {
            .statistics-container {
                padding: 20px;
            }
            .stat-card {
                padding: 20px;
                width: 200px;
            }
            .statistics-header h1 {
                font-size: 2rem;
            }
        }
    </style>
    <div class="statistics-container">
        <div class="statistics-header">
            <h1>🎮 Game Statistics 🎯</h1>
        </div>
        <div class="statistics-grid">
            <div class="stat-card">
                <div class="stat-emoji">🏆</div>
                <div class="stat-value" id="total-rounds">0</div>
                <div class="stat-label">Total Rounds</div>
            </div>
            <div class="stat-card">
                <div class="stat-emoji">✅</div>
                <div class="stat-value" id="correct-guesses">0</div>
                <div class="stat-label">Correct Guesses</div>
            </div>
            <div class="stat-card">
                <div class="stat-emoji">❌</div>
                <div class="stat-value" id="wrong-guesses">0</div>
                <div class="stat-label">Wrong Guesses</div>
            </div>
            <div class="stat-card">
                <div class="stat-emoji">💡</div>
                <div class="stat-value" id="hint-uses">0</div>
                <div class="stat-label">Hints Used</div>
            </div>
            <div class="stat-card">
                <div class="stat-emoji">🔥</div>
                <div class="stat-value" id="streaks">0</div>
                <div class="stat-label">Current Streak</div>
            </div>
        </div>
        <div class="hint" id="hint">🌟 Use hints wisely to boost your score! 🌟</div>
    </div>
    <script>
        const statistics = {
            totalRounds: 10,
            correctGuesses: 7,
            wrongGuesses: 3,
            hintUses: 2,
            streaks: 5
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
            document.getElementById('total-rounds').textContent = statistics.totalRounds;
            document.getElementById('correct-guesses').textContent = statistics.correctGuesses;
            document.getElementById('wrong-guesses').textContent = statistics.wrongGuesses;
            document.getElementById('hint-uses').textContent = statistics.hintUses;
            document.getElementById('streaks').textContent = statistics.streaks;
        }
        function setRandomHint() {
            const randomIndex = Math.floor(Math.random() * hints.length);
            document.getElementById('hint').textContent = hints[randomIndex];
        }
        setRandomHint();  // Set a random hint each time the page is refreshed
        updateStatistics();
    </script>
</div>

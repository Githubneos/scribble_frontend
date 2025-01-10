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
    </style>
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
    <script>
        async function fetchStatistics() {
            try {
                const response = await fetch('http://localhost:8887/api/statistics');
                const data = await response.json();
                document.getElementById("total-rounds").textContent = data.total_rounds;
                document.getElementById("correct-guesses").textContent = data.correct_guesses;
                document.getElementById("wrong-guesses").textContent = data.wrong_guesses;
                document.getElementById("hint-uses").textContent = data.hints_used;
                document.getElementById("streaks").textContent = data.current_streak;
            } catch (error) {
                console.error('Error fetching statistics:', error);
            }
        }
        fetchStatistics();
    </script>
</div>

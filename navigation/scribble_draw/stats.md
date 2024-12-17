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
        /* General styles */
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background: linear-gradient(135deg, #1E3C72, #2A5298);
            color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .statistics-container {
            width: 90%;
            max-width: 800px;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
        }
        .statistics-header {
            text-align: center;
            margin-bottom: 20px;
        }
        .statistics-header h1 {
            font-size: 2.5rem;
            margin: 0;
        }
        .statistics-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
        }
        .stat-card {
            background: rgba(255, 255, 255, 0.2);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 10px;
            padding: 20px;
            text-align: center;
            transition: transform 0.3s, box-shadow 0.3s;
        }
        .stat-card:hover {
            transform: translateY(-10px);
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
        }
        .stat-value {
            font-size: 2rem;
            font-weight: bold;
        }
        .stat-label {
            margin-top: 10px;
            font-size: 1rem;
            color: #CFD8DC;
        }
        .hint {
            margin-top: 15px;
            text-align: center;
            font-size: 0.9rem;
            color: #FFAB00;
        }
        @media (max-width: 600px) {
            .statistics-header h1 {
                font-size: 1.8rem;
            }
            .stat-card {
                padding: 15px;
            }
            .stat-value {
                font-size: 1.5rem;
            }
            .stat-label {
                font-size: 0.8rem;
            }
        }
    </style>
    <div class="statistics-container">
        <div class="statistics-header">
            <h1>Game Statistics</h1>
        </div>
        <div class="statistics-grid">
            <div class="stat-card">
                <div class="stat-value" id="total-rounds">0</div>
                <div class="stat-label">Total Rounds</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="correct-guesses">0</div>
                <div class="stat-label">Correct Guesses</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="wrong-guesses">0</div>
                <div class="stat-label">Wrong Guesses</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="hint-uses">0</div>
                <div class="stat-label">Hints Used</div>
            </div>
        </div>
        <div class="hint" id="hint">Use hints wisely to improve your score!</div>
    </div>
    <script>
        // Example statistics data
        const statistics = {
            totalRounds: 10,
            correctGuesses: 7,
            wrongGuesses: 3,
            hintUses: 2,
        };
        // Function to update statistics dynamically
        function updateStatistics() {
            document.getElementById('total-rounds').textContent = statistics.totalRounds;
            document.getElementById('correct-guesses').textContent = statistics.correctGuesses;
            document.getElementById('wrong-guesses').textContent = statistics.wrongGuesses;
            document.getElementById('hint-uses').textContent = statistics.hintUses;
        }
        // Call update function on page load
        updateStatistics();
    </script>
</div>

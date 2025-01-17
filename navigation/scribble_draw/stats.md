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
            min-width: 1200px;
            max-width: 1200px;
            background: linear-gradient(145deg, #1e1e1e, #2a2a2a);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.6);
            text-align: center;
        }
        .statistics-header h1 {
            font-size: 2.8rem;
            font-weight: bold;
            color: #ffcc00;
        }
        .statistics-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
        }
        .stat-card {
            background: linear-gradient(145deg, #2d2d2d, #252525);
            border-radius: 15px;
            padding: 30px;
            text-align: center;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.5);
        }
        .input-section {
            margin-top: 40px;
        }
        .input-form label {
            display: block;
            margin: 10px 0 5px;
        }
        .input-form input {
            padding: 10px;
            width: 100%;
            border-radius: 8px;
        }
        .submit-button {
            margin-top: 20px;
            padding: 10px 20px;
            background: #00ffcc;
            border: none;
            border-radius: 8px;
            cursor: pointer;
        }
        .notification {
            margin-top: 20px;
            font-size: 1rem;
            color: #00ffcc;
            font-weight: bold;
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
        </div>
        <!-- Manual Input Section -->
        <div class="input-section">
            <h2>Update Statistics</h2>
            <form class="input-form" onsubmit="return updateStatistics(event)">
                <label for="correct-guesses-input">Correct Guesses</label>
                <input type="number" id="correct-guesses-input" min="0">
                <label for="wrong-guesses-input">Wrong Guesses</label>
                <input type="number" id="wrong-guesses-input" min="0">
                <button class="submit-button" type="submit">Update Stats</button>
            </form>
            <div class="notification" id="notification" style="display: none;"></div>
        </div>
    </div>
    <script type="module">
        import { pythonURI, fetchOptions } from '{{ site.baseurl }}/assets/js/api/config.js';

        async function fetchStatistics() {
            try {
                const response = await fetch(`${pythonURI}/api/statistics`, {
                    ...fetchOptions
                });
                const data = await response.json();
                document.getElementById('total-rounds').textContent = data.total_rounds;
                document.getElementById('correct-guesses').textContent = data.correct_guesses;
                document.getElementById('wrong-guesses').textContent = data.wrong_guesses;
            } catch (error) {
                console.error('Error fetching statistics:', error);
            }
        }

        async function updateStatistics(event) {
            event.preventDefault();
            const correctGuesses = document.getElementById('correct-guesses-input').value;
            const wrongGuesses = document.getElementById('wrong-guesses-input').value;
            if (!correctGuesses && !wrongGuesses) {
                showNotification('Please enter values to update.', 'error');
                return;
            }
            const payload = {
                correct: parseInt(correctGuesses) || 0,
                wrong: parseInt(wrongGuesses) || 0
            };
            try {
                const response = await fetch(`${pythonURI}/api/statistics`, {
                    ...fetchOptions,
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(payload)
                });
                const result = await response.json();
                if (result.status === 'success') {
                    showNotification('Statistics updated successfully!', 'success');
                    fetchStatistics();
                } else {
                    showNotification('Failed to update statistics.', 'error');
                }
            } catch (error) {
                console.error('Error updating statistics:', error);
                showNotification('An error occurred. Please try again.', 'error');
            }
        }

        window.updateStatistics = updateStatistics;
        window.onload = fetchStatistics;
    </script>
</div>

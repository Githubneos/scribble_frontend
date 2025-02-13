---
layout: needsAuth
title: Leaderboard
permalink: /leaderboard
search_exclude: true
menu: nav/home.html 
---
<table>
    <tr>
        <td><a href="{{site.baseurl}}/index">Home</a></td>
        <td><a href="{{site.baseurl}}/competition">Competitive</a></td>
        <td><a href="{{site.baseurl}}/guess">Guess Game</a></td>
        <td><a href="{{site.baseurl}}/leaderboard">LeaderBoard</a></td>
        <td><a href="{{site.baseurl}}/stats">Statistics</a></td>
        <td><a href="{{site.baseurl}}/about">About Us</a></td>
        <td><a href="{{site.baseurl}}/deploy">Deploy Blog</a></td>
        <td><a href="{{site.baseurl}}/posts">Posts</a></td>
    </tr>
</table>

<style>
  body {
    background: linear-gradient(145deg, #727D73, #AAB99A, #D0DDD0, #F0F0D7);
  }
  .container {
    padding: 20px;
    max-width: 800px;
    margin: 0 auto;
  }
  
  .title {
    text-align: center;
    margin-bottom: 30px;
  }
  
  .form-group {
    margin-bottom: 15px;
  }
  
  .form-group label {
    display: block;
    margin-bottom: 5px;
  }
  
  .form-group input {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
  }
  
  .submit-button {
    width: 100%;
    padding: 10px;
    background-color: #dc2626;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .submit-button:hover {
    background-color: #b91c1c;
  }
  
  .leaderboard-table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  
  .leaderboard-table th,
  .leaderboard-table td {
    border: 1px solid #ddd;
    padding: 12px;
    text-align: center;
  }
  
  .leaderboard-table th {
    background-color: #f3f4f6;
  }
  
  .delete-btn {
    color: #dc2626;
    text-decoration: underline;
    cursor: pointer;
    background: none;
    border: none;
  }
  
  .message {
    text-align: center;
    margin-top: 15px;
  }
  
  .success {
    color: #059669;
  }
  
  .error {
    color: #dc2626;
  }
</style>

<div class="container">
  <h2 class="title">Drawing Leaderboard</h2>

  <form id="score-form">
    <div class="form-group">
      <label for="drawingName">Drawing Name</label>
      <input type="text" id="drawingName" required>
    </div>
    <div class="form-group">
      <label for="score">Score (0-100)</label>
      <input type="number" id="score" min="0" max="100" required>
    </div>
    <button type="submit" class="submit-button">Submit Score</button>
  </form>

  <table class="leaderboard-table">
    <thead>
      <tr>
        <th>Rank</th>
        <th>Player</th>
        <th>Drawing</th>
        <th>Score</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody id="leaderboard-entries">
    </tbody>
  </table>
  
  <div id="message" class="message"></div>
</div>

<script type="module">
  import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

  const fetchConfig = {
    credentials: 'include',
    headers: {
      'Content-Type': 'application/json'
    }
  };

  async function fetchLeaderboard() {
    const messageElement = document.getElementById('message');
    const leaderboardBody = document.getElementById('leaderboard-entries');

    leaderboardBody.innerHTML = '';

    try {
      const response = await fetch(`${pythonURI}/api/leaderboard`, {
        method: "GET"  // GET doesn't need authentication
      });

      if (!response.ok) throw new Error('Failed to load leaderboard');
      const data = await response.json();

      data
        .sort((a, b) => b.score - a.score)
        .forEach((entry, index) => {
          const row = document.createElement('tr');
          row.innerHTML = `
            <td><span style="color: ${index < 3 ? '#dc2626' : '#666'}">#${index + 1}</span></td>
            <td>${entry.profile_name}</td>
            <td>${entry.drawing_name}</td>
            <td>${entry.score}</td>
            <td>${entry.created_by ? 
              `<button class="delete-btn" onclick="deleteEntry(${entry.id})">Delete</button>` 
              : ''}</td>
          `;
          leaderboardBody.appendChild(row);
        });
      messageElement.textContent = '';
    } catch (error) {
      console.error('Error:', error);
      messageElement.textContent = 'Error loading leaderboard';
      messageElement.className = 'message error';
    }
  }

  document.getElementById('score-form').addEventListener('submit', async function(event) {
    event.preventDefault();
    const messageElement = document.getElementById('message');
    
    const drawingName = document.getElementById('drawingName').value.trim();
    const score = parseInt(document.getElementById('score').value);

    if (score < 0 || score > 100) {
      messageElement.textContent = 'Score must be between 0 and 100';
      messageElement.className = 'message error';
      return;
    }

    try {
      const response = await fetch(`${pythonURI}/api/leaderboard`, {
        ...fetchConfig,
        method: "POST",
        body: JSON.stringify({
          drawing_name: drawingName,
          score: score
        })
      });

      const data = await response.json();
      
      if (!response.ok) {
        throw new Error(data.message || 'Failed to submit score');
      }

      messageElement.textContent = 'Score submitted successfully!';
      messageElement.className = 'message success';
      this.reset();
      
      await fetchLeaderboard();
    } catch (error) {
      console.error('Error:', error);
      messageElement.textContent = error.message;
      messageElement.className = 'message error';
    }
  });

  window.deleteEntry = async function(entryId) {
    if (!confirm('Are you sure you want to delete this entry?')) return;
    const messageElement = document.getElementById('message');

    try {
      const response = await fetch(`${pythonURI}/api/leaderboard`, {
        ...fetchConfig,
        method: "DELETE",
        body: JSON.stringify({ id: entryId })
      });

      const data = await response.json();

      if (!response.ok) {
        throw new Error(data.message || 'Failed to delete entry');
      }

      messageElement.textContent = data.message || 'Entry deleted successfully';
      messageElement.className = 'message success';
      
      await fetchLeaderboard();
    } catch (error) {
      console.error('Error:', error);
      messageElement.textContent = error.message;
      messageElement.className = 'message error';
    }
  };

  function showMessage(text, type) {
    const messageElement = document.getElementById('message');
    messageElement.textContent = text;
    messageElement.className = `message ${type}`;
    messageElement.style.display = 'block';
    messageEl.style.backgroundColor = isError ? '#727D73' : '#AAB99A';
    messageEl.style.color = isError ? '#D0DDD0' : '#F0F0D7';
    setTimeout(() => messageElement.style.display = 'none', 3000);
  }

  document.addEventListener('DOMContentLoaded', fetchLeaderboard);
</script>
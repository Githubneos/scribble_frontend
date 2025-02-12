---
layout: post
title: Picture Gallery
search_exclude: true
description: Dark themed picture gallery
Author: Daksha
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
    </tr>
</table>

<style>
    :root {
        --primary-color: #1a237e;
        --secondary-color: #283593;
        --background: linear-gradient(135deg, #1a1a2e, #16213e);
        --text-color: #e1e1e1;
        --card-bg: rgba(30, 41, 59, 0.8);
        --error: #e74c3c;
        --success: #2ecc71;
    }

    body {
        background: var(--background);
        color: var(--text-color);
        min-height: 100vh;
    }

    table {
        width: 100%;
        margin-bottom: 2rem;
        border-collapse: collapse;
        background: rgba(30, 41, 59, 0.5);
        border-radius: 8px;
    }

    table td {
        padding: 0.5rem;
        text-align: center;
    }

    table a {
        color: #93c5fd;
        text-decoration: none;
        transition: color 0.3s ease;
    }

    table a:hover {
        color: #60a5fa;
    }

    .picture-gallery {
        max-width: 1200px;
        margin: 2rem auto;
        padding: 1rem;
    }

    .upload-form {
        background: var(--card-bg);
        padding: 2rem;
        border-radius: 8px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        margin-bottom: 2rem;
        backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.1);
    }

    .form-group {
        margin-bottom: 1.5rem;
    }

    .form-label {
        display: block;
        margin-bottom: 0.5rem;
        color: var(--text-color);
        font-weight: 500;
    }

    .form-input {
        width: 100%;
        padding: 0.75rem;
        border: 1px solid rgba(255, 255, 255, 0.1);
        border-radius: 4px;
        background: rgba(15, 23, 42, 0.7);
        color: var(--text-color);
        transition: border-color 0.3s ease;
    }

    .form-input:focus {
        outline: none;
        border-color: #3b82f6;
    }

    .submit-btn {
        background: #3b82f6;
        color: white;
        border: none;
        padding: 0.75rem 1.5rem;
        border-radius: 4px;
        cursor: pointer;
        transition: all 0.3s ease;
    }

    .submit-btn:hover {
        background: #2563eb;
        transform: translateY(-2px);
    }

    .gallery-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 2rem;
    }

    .picture-card {
        background: var(--card-bg);
        border-radius: 8px;
        overflow: hidden;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        transition: transform 0.3s ease;
        backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.1);
    }

    .picture-card:hover {
        transform: translateY(-5px);
    }

    .picture-img {
        width: 100%;
        height: 200px;
        object-fit: contain;
        background: transparent;
        padding: 1rem;
    }

    .picture-info {
        padding: 1rem;
    }

    .picture-info h3 {
        color: #93c5fd;
        margin: 0 0 0.5rem 0;
    }

    .picture-info p {
        color: var(--text-color);
        margin: 0 0 1rem 0;
        opacity: 0.8;
    }

    .picture-info small {
        color: var(--text-color);
        opacity: 0.6;
    }

    #message {
        padding: 1rem;
        margin: 1rem 0;
        border-radius: 4px;
        display: none;
    }
</style>

<div class="picture-gallery">
    <h1>Picture Gallery</h1>
    <div class="upload-form">
        <h2>Upload New Picture</h2>
        <div class="form-group">
            <label class="form-label">User Name</label>
            <input type="text" id="userName" class="form-input" required>
        </div>
        <div class="form-group">
            <label class="form-label">Drawing Name</label>
            <input type="text" id="drawingName" class="form-input" required>
        </div>
        <div class="form-group">
            <label class="form-label">Description</label>
            <textarea id="description" class="form-input" rows="3"></textarea>
        </div>
        <div class="form-group">
            <label class="form-label">Picture (PNG only)</label>
            <input type="file" id="pictureFile" accept=".png" class="form-input" required>
        </div>
        <button onclick="uploadPicture()" class="submit-btn">Upload Picture</button>
    </div>
    <div id="message"></div>
    <div id="galleryGrid" class="gallery-grid"></div>
</div>

<script>
    const API_URL = 'https://scribble.stu.nighthawkcodingsociety.com/api/picture';

    function showMessage(message, isError = false) {
        const messageEl = document.getElementById('message');
        messageEl.textContent = message;
        messageEl.style.display = 'block';
        messageEl.style.backgroundColor = isError ? 'rgba(231, 76, 60, 0.2)' : 'rgba(46, 204, 113, 0.2)';
        messageEl.style.color = isError ? '#e74c3c' : '#2ecc71';
        messageEl.style.border = `1px solid ${isError ? '#e74c3c' : '#2ecc71'}`;
        messageEl.style.padding = '1rem';
        messageEl.style.borderRadius = '4px';
        setTimeout(() => messageEl.style.display = 'none', 3000);
    }

    async function uploadPicture() {
        const userName = document.getElementById('userName').value;
        const drawingName = document.getElementById('drawingName').value;
        const description = document.getElementById('description').value;
        const pictureFile = document.getElementById('pictureFile').files[0];

        if (!userName || !drawingName || !pictureFile) {
            showMessage('Please fill in all required fields', true);
            return;
        }

        const formData = new FormData();
        formData.append('user_name', userName);
        formData.append('drawing_name', drawingName);
        formData.append('description', description);
        formData.append('image', pictureFile);

        try {
            const response = await fetch(API_URL, {
                method: 'POST',
                body: formData
            });

            const data = await response.json();
            console.log('Upload response:', data);

            if (response.ok) {
                showMessage('Picture uploaded successfully');
                clearForm();
                fetchPictures();
            } else {
                throw new Error(data.error || 'Failed to upload picture');
            }
        } catch (error) {
            console.error('Upload error:', error);
            showMessage(error.message, true);
        }
    }

    function clearForm() {
        document.getElementById('userName').value = '';
        document.getElementById('drawingName').value = '';
        document.getElementById('description').value = '';
        document.getElementById('pictureFile').value = '';
    }

    async function fetchPictures() {
        try {
            const response = await fetch(API_URL);
            if (!response.ok) throw new Error('Failed to fetch pictures');
            const data = await response.json();
            console.log('Fetched pictures:', data);
            displayPictures(data.pictures || []);
        } catch (error) {
            console.error('Fetch error:', error);
            showMessage(error.message, true);
        }
    }

    function displayPictures(pictures) {
        const grid = document.getElementById('galleryGrid');
        grid.innerHTML = '';

        pictures.forEach(picture => {
            const card = document.createElement('div');
            card.className = 'picture-card';
            card.innerHTML = `
                <img src="${picture.image_url}" 
                     alt="${picture.drawing_name}" 
                     class="picture-img"
                     onerror="this.onerror=null; this.src='data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVR42mNkYAAAAAYAAjCB0C8AAAAASUVORK5CYII='">
                <div class="picture-info">
                    <h3>${picture.drawing_name}</h3>
                    <p>${picture.description || 'No description provided'}</p>
                    <small>By: ${picture.user_name}</small>
                    <small class="date">${new Date(picture.created_at).toLocaleDateString()}</small>
                </div>
            `;
            grid.appendChild(card);
        });
    }

    // Initialize gallery
    fetchPictures();

    // Refresh every 30 seconds
    setInterval(fetchPictures, 30000);
</script>
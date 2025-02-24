---
layout: needsAuth
title: Posts
permalink: /posts
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
    :root {
        --primary-color: #1a237e;
        --secondary-color: #283593;
        --background: linear-gradient(135deg, #1a1a2e, #16213e);
        --text-color: #e1e1e1;
        --card-bg: rgba(30, 41, 59, 0.8);
        --error: #e74c3c;
        --success: #2ecc71;
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
    }

    .picture-img {
        width: 100%;
        height: 200px;
        object-fit: contain;
        background: #fff;
        padding: 1rem;
    }

    .picture-info {
        padding: 1rem;
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

    .message {
        padding: 1rem;
        margin: 1rem 0;
        border-radius: 4px;
        text-align: center;
    }

    .error {
        background: rgba(231, 76, 60, 0.2);
        color: #e74c3c;
        border: 1px solid #e74c3c;
    }

    .success {
        background: rgba(46, 204, 113, 0.2);
        color: #2ecc71;
        border: 1px solid #2ecc71;
    }
</style>

<div class="picture-gallery">
    <div class="upload-form">
        <h2>Upload Drawing</h2>
        <form id="picture-form">
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
                <input type="file" id="image" accept=".png" class="form-input" required>
            </div>
            <button type="submit" class="submit-btn">Upload Drawing</button>
        </form>
    </div>
    <div id="message" class="message" style="display: none;"></div>
    <div id="gallery" class="gallery-grid"></div>
</div>

<script type="module">
    import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

    const fetchConfig = {
        credentials: "include",
        headers: {
            'X-Origin': 'client'
        }
    };

    async function fetchPictures() {
        try {
            const response = await fetch(`${pythonURI}/api/picture`, {
                method: "GET"
            });

            if (!response.ok) throw new Error('Failed to load pictures');
            const pictures = await response.json();

            const gallery = document.getElementById('gallery');
            gallery.innerHTML = '';

            pictures.forEach(picture => {
                const card = document.createElement('div');
                card.className = 'picture-card';
                card.innerHTML = `
                    <img src="data:image/png;base64,${picture.image_data}" 
                         alt="${picture.drawing_name}" 
                         class="picture-img">
                    <div class="picture-info">
                        <h3>${picture.drawing_name}</h3>
                        <p>${picture.description || 'No description'}</p>
                        <small>By: ${picture.user_name}</small>
                        <br>
                        ${picture.can_delete ? 
                            `<button onclick="deletePicture(${picture.id})" class="delete-btn">Delete</button>` 
                            : ''}
                    </div>
                `;
                gallery.appendChild(card);
            });
        } catch (error) {
            console.error('Error:', error);
            showMessage('Failed to load pictures: ' + error.message, true);
        }
    }

    document.getElementById('picture-form').addEventListener('submit', async function(event) {
        event.preventDefault();
        
        const formData = new FormData();
        formData.append('drawing_name', document.getElementById('drawingName').value);
        formData.append('description', document.getElementById('description').value);
        formData.append('image', document.getElementById('image').files[0]);

        try {
            const response = await fetch(`${pythonURI}/api/picture`, {
                method: "POST",
                credentials: "include",
                headers: {
                    'X-Origin': 'client'
                },
                body: formData
            });

            const data = await response.json();

            if (!response.ok) {
                throw new Error(data.message || 'Failed to upload picture');
            }

            showMessage('Picture uploaded successfully!');
            this.reset();
            await fetchPictures();
        } catch (error) {
            console.error('Error:', error);
            showMessage('Upload failed: ' + error.message, true);
        }
    });

    window.deletePicture = async function(pictureId) {
        if (!confirm('Are you sure you want to delete this picture?')) return;

        try {
            const response = await fetch(`${pythonURI}/api/picture`, {
                method: "DELETE",
                credentials: "include",
                headers: {
                    'Content-Type': 'application/json',
                    'X-Origin': 'client'
                },
                body: JSON.stringify({ id: pictureId })
            });

            const data = await response.json();

            if (!response.ok) {
                throw new Error(data.message || 'Failed to delete picture');
            }

            showMessage('Picture deleted successfully');
            await fetchPictures();
        } catch (error) {
            console.error('Error:', error);
            showMessage('Delete failed: ' + error.message, true);
        }
    };

    function showMessage(message, isError = false) {
        const messageEl = document.getElementById('message');
        messageEl.textContent = message;
        messageEl.style.display = 'block';
        messageEl.style.backgroundColor = isError ? '#C6E7FF' : '#D4F6FF';
        messageEl.style.color = isError ? '#FBFBFB' : '#FFDDAE';
        setTimeout(() => messageEl.style.display = 'none', 3000);
    }

    document.addEventListener('DOMContentLoaded', fetchPictures);
</script>
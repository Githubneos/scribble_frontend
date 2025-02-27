---
layout: needsAuth
title: Blind Trace Submissions
permalink: /blind-trace
search_exclude: true
---

<style>
:root {
    --background: linear-gradient(145deg, #A6AEBF, #C5D3E8, #D0E8C5, #FFF8DE);
}

body {
    background: var(--background);
    min-height: 100vh;
    margin: 0;
    padding: 0;
}

.container {
    max-width: 1200px;
    margin: 2rem auto;
    padding: 1rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.submission-form {
    background: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    margin-bottom: 2rem;
}

.form-group {
    margin-bottom: 1.5rem;
}

.form-group label {
    display: block;
    margin-bottom: 0.5rem;
    color: #333;
    font-weight: bold;
}

.form-group input, .form-group textarea {
    width: 100%;
    padding: 0.75rem;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 1rem;
}

.submit-btn {
    background: #2196F3;
    color: white;
    border: none;
    padding: 1rem 2rem;
    border-radius: 4px;
    cursor: pointer;
    font-weight: bold;
    width: 100%;
    font-size: 1rem;
    transition: background-color 0.3s;
}

.submit-btn:hover {
    background: #1976D2;
}

.submission-entry {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    margin-bottom: 2rem;
    padding: 1rem;
    text-align: center;
}

.submission-title {
    background: #f8f9fa;
    padding: 1rem;
    margin: 0;
    font-size: 1.25rem;
    color: #333;
}

.submission-info {
    padding: 1rem;
}

.submission-info img {
    max-width: 300px;
    border-radius: 4px;
    margin: 1rem 0;
}

.edit-btn, .delete-btn {
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
    transition: background-color 0.3s;
    margin: 5px;
}

.edit-btn { background: #ffc107; }
.edit-btn:hover { background: #e0a800; }

.delete-btn { background: #dc3545; }
.delete-btn:hover { background: #c82333; }

.message {
    padding: 1rem;
    border-radius: 4px;
    margin: 1rem 0;
    display: none;
    transition: opacity 0.3s;
}

.success {
    background: #d4edda;
    color: #155724;
}

.error {
    background: #f8d7da;
    color: #721c24;
}
</style>

<div class="container">
    <form class="submission-form" id="submission-form">
        <h2>Submit Blind Trace</h2>
        <div class="form-group">
            <label for="imageUrl">Reference Image URL:</label>
            <input type="text" id="imageUrl" required>
        </div>
        <div class="form-group">
            <label for="drawingData">Base64 Drawing Data:</label>
            <textarea id="drawingData" rows="4" required></textarea>
        </div>
        <button type="submit" class="submit-btn">Submit Drawing</button>
        <div id="message" class="message"></div>
    </form>

    <div id="submissions-container"></div>
</div>

<script type="module">
import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

document.addEventListener('DOMContentLoaded', async () => {
    fetchSubmissions();
});

async function fetchSubmissions() {
    try {
        const response = await fetch(`${pythonURI}/api/submission`, { credentials: 'include' });

        if (!response.ok) throw new Error('Failed to fetch submissions');

        const data = await response.json();
        const container = document.getElementById('submissions-container');
        container.innerHTML = '';

        data.submissions.forEach(submission => {
            container.appendChild(createSubmissionEntry(submission));
        });
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
}

function createSubmissionEntry(submission) {
    const entry = document.createElement('div');
    entry.className = 'submission-entry';

    entry.innerHTML = `
        <h3 class="submission-title">Submission - ${new Date(submission.submission_time).toLocaleString()}</h3>
        <div class="submission-info">
            <p><strong>Reference Image:</strong></p>
            <img src="${submission.image_url}" alt="Reference Image">
            <p><strong>Submitted Drawing:</strong></p>
            <img src="${submission.drawing}" alt="User Drawing">
            <p><strong>Score:</strong> ${submission.score}</p>
            <button class="edit-btn" onclick="editSubmission(${submission.id}, '${submission.image_url}', '${submission.drawing}')">Edit</button>
            <button class="delete-btn" onclick="deleteSubmission(${submission.id})">Delete</button>
        </div>
    `;

    return entry;
}

document.getElementById('submission-form').addEventListener('submit', async function(event) {
    event.preventDefault();
    const imageUrl = document.getElementById('imageUrl').value.trim();
    const drawing = document.getElementById('drawingData').value.trim();

    if (!imageUrl || !drawing) {
        showMessage('Please provide both an image URL and a drawing.', 'error');
        return;
    }

    try {
        const response = await fetch(`${pythonURI}/api/submission`, {
            method: 'POST',
            credentials: 'include',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ image_url: imageUrl, drawing })
        });

        if (!response.ok) throw new Error('Submission failed');

        showMessage('Submission successful!', 'success');
        this.reset();
        fetchSubmissions();
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
});

window.deleteSubmission = async function(id) {
    if (!confirm('Are you sure you want to delete this submission?')) return;

    try {
        await fetch(`${pythonURI}/api/submission/${id}`, { method: 'DELETE', credentials: 'include' });
        showMessage('Submission deleted successfully', 'success');
        fetchSubmissions();
    } catch (error) {
        console.error('Error:', error);
        showMessage(error.message, 'error');
    }
};

window.editSubmission = function(id, imageUrl, drawing) {
    const newImageUrl = prompt('Enter new image URL:', imageUrl);
    const newDrawing = prompt('Enter new base64 drawing data:', drawing);

    if (newImageUrl && newDrawing) {
        updateSubmission(id, newImageUrl, newDrawing);
    }
};
</script>

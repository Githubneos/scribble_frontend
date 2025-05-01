---
layout: needsAuth
title: Posts
description: Post your images here!
permalink: /posts
search_exclude: true
menu: nav/home.html 
---

<table>
    <!-- Navigation menu for the website -->
    <!-- Provides links to different sections of the site -->
    <tr>
        <td><a href="{{site.baseurl}}/index">Home</a></td>
        <td><a href="{{site.baseurl}}/competition">Competitive</a></td>
        <td><a href="{{site.baseurl}}/guess">Guess Game</a></td>
        <td><a href="{{site.baseurl}}/blind_trace">Blind Trace</a></td>
        <td><a href="{{site.baseurl}}/leaderboard">LeaderBoard</a></td>
        <td><a href="{{site.baseurl}}/stats">Statistics</a></td>
        <td><a href="{{site.baseurl}}/about">About Us</a></td>
        <td><a href="{{site.baseurl}}/deploy">Deploy Blog</a></td>
        <td><a href="{{site.baseurl}}/posts">Posts</a></td>
    </tr>
</table>

<style>
    :root {
        /* Define CSS variables for consistent styling across the page */
        --primary-color: #1a237e; /* Primary color for buttons and highlights */
        --secondary-color: #283593; /* Secondary color for hover effects */
        --background: linear-gradient(145deg, #789DBC, #FFE3E3, #FEF9F2, #C9E9D2, rgb(120, 157, 188), rgb(255, 227, 227), rgb(254, 249, 242), rgb(201, 233, 210));
        --text-color: #e1e1e1; /* Text color for labels and descriptions */
        --card-bg: rgba(30, 41, 59, 0.8); /* Background color for cards */
        --error: #e74c3c; /* Error color for delete buttons */
        --success: #2ecc71; /* Success color for messages */
    }

    body {
        /* Set the background and ensure the page takes up the full height */
        background: var(--background);
        min-height: 100vh;
        margin: 0;
        padding: 0;
    }

    .picture-gallery {
        /* Center the gallery and set a maximum width */
        max-width: 1200px;
        margin: 2rem auto;
        padding: 0 1rem;
        background: white;
    }

    .upload-form {
        /* Style the upload form with a card-like appearance */
        background-color: var(--card-bg);
        padding: 2rem;
        border-radius: 8px;
        margin-bottom: 2rem;
    }

    .form-group {
        /* Add spacing between form elements */
        margin-bottom: 1rem;
    }

    .form-label {
        /* Style the labels for form inputs */
        display: block;
        margin-bottom: 0.5rem;
        color: var(--text-color);
    }

    .form-input {
        /* Style the input fields */
        width: 100%;
        padding: 0.5rem;
        border: 1px solid rgba(255, 255, 255, 0.1);
        border-radius: 4px;
        background-color: rgba(255, 255, 255, 0.05);
        color: var(--text-color);
    }

    .submit-btn {
        /* Style the submit button */
        background-color: var(--primary-color);
        color: white;
        padding: 0.75rem 1.5rem;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    .submit-btn:hover {
        /* Change the background color on hover */
        background-color: var(--secondary-color);
    }

    .gallery-grid {
        /* Create a responsive grid layout for the gallery */
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 1.5rem;
        padding: 1rem 0;
    }

    .picture-card {
        /* Style individual picture cards */
        background-color: var(--card-bg);
        border-radius: 8px;
        overflow: hidden;
        transition: transform 0.3s;
        max-width: 300px;
        margin: 0 auto;
    }

    .picture-img {
        /* Style the images inside the cards */
        width: 100%;
        height: 160px;
        object-fit: contain;
        background-color: rgba(0, 0, 0, 0.1);
        padding: 0.5rem;
    }

    .picture-info {
        /* Style the text information inside the cards */
        padding: 1rem;
        color: var(--text-color);
    }

    .picture-info h3 {
        /* Style the title of the drawing */
        margin: 0 0 0.5rem 0;
        color: var(--text-color);
    }

    .picture-info p {
        /* Style the description text */
        margin: 0 0 1rem 0;
        font-size: 0.9rem;
        color: rgba(225, 225, 225, 0.8);
    }

    .delete-btn {
        /* Style the delete button */
        background-color: var(--error);
        color: white;
        border: none;
        padding: 0.5rem 1rem;
        border-radius: 4px;
        cursor: pointer;
        transition: opacity 0.3s;
    }

    .delete-btn:hover {
        /* Slightly reduce opacity on hover */
        opacity: 0.9;
    }

    .message {
        /* Style the message box for notifications */
        position: fixed;
        top: 20px;
        right: 20px;
        padding: 1rem 2rem;
        border-radius: 4px;
        animation: fadeIn 0.3s ease-in;
        z-index: 1000;
    }

    @keyframes fadeIn {
        /* Animation for the message box */
        from { opacity: 0; transform: translateY(-10px); }
        to { opacity: 1; transform: translateY(0); }
    }
</style>

<div class="picture-gallery">
    <!-- Container for the drawing board and gallery -->
    <div id="app"></div>

    <!-- Upload Drawing Section -->
    <div class="upload-form">
        <h2>Upload Drawing</h2>
        <form id="picture-form" enctype="multipart/form-data">
            <!-- Input for drawing name -->
            <div class="form-group">
                <label class="form-label">Drawing Name</label>
                <input type="text" id="drawingName" class="form-input" required>
            </div>
            <!-- Input for description -->
            <div class="form-group">
                <label class="form-label">Description</label>
                <textarea id="description" class="form-input" rows="3"></textarea>
            </div>
            <!-- Input for image file -->
            <div class="form-group">    
                <label class="form-label">Picture (PNG only)</label>
                <input type="file" id="image" accept="image/png" class="form-input" required>
            </div>
            <!-- Submit button -->
            <button type="submit" class="submit-btn">Upload Drawing</button>
        </form>
    </div>
    <div id="message" class="message" style="display: none;"></div>
    <div id="gallery" class="gallery-grid"></div>
</div>

<script type="module">
    import { pythonURI } from '{{site.baseurl}}/assets/js/api/config.js';

    // Configuration for fetch requests
    const fetchConfig = {
        credentials: "include", // Include credentials for authentication
        headers: {
            'X-Origin': 'client' // Custom header to identify the client
        }
    };

    document.addEventListener('DOMContentLoaded', () => {
        const app = document.querySelector('#app');
        if (!app) {
            console.error('Error: #app container not found. Ensure the div with id "app" is in the HTML.');
            return;
        }

        const toolbar = document.createElement('div');
        toolbar.style.cssText = `
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 10px;
            background: rgba(255, 255, 255, 0.3);
            padding: 10px;
            border-radius: 10px;
            gap: 10px;
            flex-wrap: wrap;
        `;

        const colorPicker = document.createElement('input');
        colorPicker.type = 'color';
        colorPicker.value = '#000000';
        colorPicker.style.cssText = `
            width: 40px;
            height: 40px;
            border: none;
            cursor: pointer;
        `;
        toolbar.appendChild(colorPicker);

        let currentColor = colorPicker.value;
        let isEraser = false;

        colorPicker.addEventListener('input', () => {
            currentColor = colorPicker.value;
            isEraser = false;
        });

        const brushSize = document.createElement('input');
        brushSize.type = 'range';
        brushSize.min = '1';
        brushSize.max = '50';
        brushSize.value = '5';
        brushSize.style.cssText = 'margin: 0 10px;';
        toolbar.appendChild(brushSize);

        const markerButton = document.createElement('button');
        markerButton.textContent = 'Marker';
        markerButton.style.cssText = `
            background: #FF5733;
            color: white;
            border: 2px solid #000;
            padding: 10px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        `;
        markerButton.addEventListener('click', () => {
            isEraser = false;
            currentColor = '#000000';
            colorPicker.value = currentColor;
        });
        toolbar.appendChild(markerButton);

        // Eraser button
        const eraserButton = document.createElement('button');
        eraserButton.textContent = 'Eraser';
        eraserButton.style.cssText = `
            background: white;
            color: black;
            border: 2px solid #000;
            padding: 10px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        `;
        eraserButton.addEventListener('click', () => {
            isEraser = true;
        });
        toolbar.appendChild(eraserButton);

        const undoButton = document.createElement('button');
        undoButton.textContent = 'Undo';
        undoButton.style.cssText = `
            background: #FFC107;
            color: white;
            border: none;
            padding: 10px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        `;
        undoButton.addEventListener('click', () => {
            if (undoStack.length > 0) {
                undoStack.pop();
                const lastImage = undoStack.length > 0 ? undoStack[undoStack.length - 1] : null;
                const img = new Image();
                img.src = lastImage || '';
                img.onload = () => {
                    ctx.clearRect(0, 0, canvas.width, canvas.height);
                    ctx.drawImage(img, 0, 0);
                };
            }
        });
        toolbar.appendChild(undoButton);

        const resetButton = document.createElement('button');
        resetButton.textContent = 'Reset';
        resetButton.style.cssText = `
            background: #DC3545;
            color: white;
            border: none;
            padding: 10px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        `;
        resetButton.addEventListener('click', () => {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            undoStack = [];
        });
        toolbar.appendChild(resetButton);

        const saveButton = document.createElement('button');
        saveButton.textContent = 'Save Drawing';
        saveButton.style.cssText = `
            background: #28A745;
            color: white;
            border: none;
            padding: 10px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        `;
        saveButton.addEventListener('click', () => {
            const drawingData = canvas.toDataURL("image/png");
            const link = document.createElement('a');
            link.download = `drawing.png`;
            link.href = drawingData;
            link.click();
        });
        toolbar.appendChild(saveButton);

        const canvas = document.createElement('canvas');
        canvas.width = 800;
        canvas.height = 600;
        canvas.style.cssText = `
            border: 2px solid black;
            background: white;
            cursor: crosshair;
        `;
        const ctx = canvas.getContext('2d');
        ctx.fillStyle = 'white';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        let drawing = false;
        let undoStack = [];

        canvas.addEventListener('mousedown', (e) => {
            drawing = true;
            ctx.beginPath();
            ctx.moveTo(e.offsetX, e.offsetY);
        });

        canvas.addEventListener('mousemove', (e) => {
            if (drawing) {
                ctx.strokeStyle = isEraser ? 'white' : currentColor;
                ctx.lineWidth = brushSize.value;
                ctx.lineCap = 'round';
                ctx.lineTo(e.offsetX, e.offsetY);
                ctx.stroke();
            }
        });

        canvas.addEventListener('mouseup', () => {
            drawing = false;
            ctx.closePath();
            undoStack.push(canvas.toDataURL());
        });

        canvas.addEventListener('mouseleave', () => {
            drawing = false;
        });

        app.appendChild(toolbar);
        app.appendChild(canvas);
    });

    // Function to fetch and display pictures
    async function fetchPictures() {
        try {
            const response = await fetch(`${pythonURI}/api/pictures`, {
                method: "GET", // HTTP GET request to fetch pictures
                ...fetchConfig
            });

            if (!response.ok) throw new Error('Failed to load pictures');
            const pictures = await response.json();

            // Use a list to store picture data
            const gallery = document.getElementById('gallery');
            gallery.innerHTML = ''; // Clear the gallery before adding new pictures

            pictures.forEach(picture => {
                const card = document.createElement('div');
                card.className = 'picture-card'; // Add a class for styling
                card.innerHTML = `
                    <img src="${picture.image_data}" 
                         alt="${picture.drawing_name}" 
                         class="picture-img">
                    <div class="picture-info">
                        <h3>${picture.drawing_name}</h3>
                        <p>${picture.description || 'No description'}</p>
                        <small>By: ${picture.user_name}</small>
                        <br>
                        <div class="button-group">
                            ${picture.can_delete ? 
                                `<button onclick="deletePicture(${picture.id})" class="delete-btn">Delete Drawing</button>` 
                                : ''}
                        </div>
                    </div>
                `;
                gallery.appendChild(card); // Add the card to the gallery
            });
        } catch (error) {
            console.error('Error:', error);
            showMessage('Failed to load pictures: ' + error.message, true);
        }
    }

    // Procedure to delete a picture
    window.deletePicture = async function(pictureId) {
        if (!confirm('Are you sure you want to delete this picture?')) return;

        try {
            const response = await fetch(`${pythonURI}/api/pictures/delete/${pictureId}`, {
                method: "DELETE", // HTTP DELETE request to remove a picture
                ...fetchConfig
            });

            const data = await response.json();

            if (!response.ok) {
                throw new Error(data.message || 'Failed to delete picture');
            }

            showMessage('Picture deleted successfully');
            await fetchPictures(); // Refresh the gallery after deletion
        } catch (error) {
            console.error('Error:', error);
            showMessage('Delete failed: ' + error.message, true);
        }
    };

    // Procedure to show messages to the user
    function showMessage(message, isError = false) {
        const messageEl = document.getElementById('message');
        messageEl.textContent = message; // Set the message text
        messageEl.style.display = 'block'; // Make the message visible
        messageEl.style.backgroundColor = isError ? '#C6E7FF' : '#D4F6FF'; // Set background color based on error state
        messageEl.style.color = isError ? '#FBFBFB' : '#FFDDAE'; // Set text color
        setTimeout(() => messageEl.style.display = 'none', 3000); // Hide the message after 3 seconds
    }

    // Event listener for form submission
    document.getElementById('picture-form').addEventListener('submit', async function(event) {
        event.preventDefault(); // Prevent the default form submission behavior
        
        const formData = new FormData(); // Create a FormData object to hold the form data
        formData.append('drawing_name', document.getElementById('drawingName').value); // Add the drawing name
        formData.append('description', document.getElementById('description').value); // Add the description
        formData.append('image', document.getElementById('image').files[0]); // Add the image file

        try {
            const response = await fetch(`${pythonURI}/api/pictures`, {
                method: "POST", // HTTP POST request to upload the picture
                ...fetchConfig,
                body: formData
            });

            const data = await response.json();

            if (!response.ok) {
                throw new Error(data.message || 'Failed to upload picture');
            }

            showMessage('Picture uploaded successfully!');
            this.reset(); // Reset the form after successful upload
            await fetchPictures(); // Refresh the gallery
        } catch (error) {
            console.error('Error:', error);
            showMessage('Upload failed: ' + error.message, true);
        }
    });

    // Fetch pictures on page load
    document.addEventListener('DOMContentLoaded', fetchPictures);
</script>
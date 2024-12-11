---
layout: post
title: Scribble Chat
search_exclude: true
description: Chatroom
permalink: scribbl_with_users/chatroom
Author: Keerthan Karumudi
hide: true
---

<div id="app"></div>

<script>
    document.addEventListener('DOMContentLoaded', () => {
        const app = document.getElementById('app');

        app.style.cssText = `
            background: linear-gradient(135deg, #2c3e50, #3498db);
            background-size: 200% 200%;
            animation: subtleBG 15s ease infinite;
            font-family: Arial, sans-serif;
            color: white;
            margin: 0;
            padding: 0;
            height: 100vh;
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: flex-start;
        `;

        const style = document.createElement('style');
        style.textContent = `
            @keyframes subtleBG {
                0% { background-position: 0% 50%; }
                50% { background-position: 100% 50%; }
                100% { background-position: 0% 50%; }
            }

            #content {
                padding: 20px;
                text-align: center;
                width: 100%;
                flex: 1;
                display: flex;
                justify-content: center;
                align-items: center;
                flex-direction: column;
            }

            #chatroom-content {
                width: 80%;
                background: rgba(0, 0, 0, 0.6);
                padding: 20px;
                border-radius: 10px;
            }

            #chatbox {
                width: 100%;
                height: 300px;
                overflow-y: auto;
                border: 1px solid #ccc;
                padding: 10px;
                background-color: black;
                color: white;
                border-radius: 5px;
                font-family: monospace;
            }

            .chat-message {
                margin-bottom: 10px;
                color: white;
            }

            input, button {
                margin-top: 10px;
                padding: 10px;
                border: none;
                border-radius: 5px;
            }

            input {
                width: 80%;
            }

            button {
                background-color: #ff6a00;
                color: white;
                cursor: pointer;
            }

            button:hover {
                background-color: #e65a00;
            }
        `;
        document.head.appendChild(style);

        const content = document.createElement('div');
        content.id = 'content';

        const chatroomContent = document.createElement('div');
        chatroomContent.id = 'chatroom-content';
        chatroomContent.innerHTML = `
            <h1>Chatroom</h1>
            <div id="chatbox"></div>
            <div id="controls">
                <input type="text" id="message" placeholder="Enter your message" />
                <button id="sendButton">Send</button>
            </div>
        `;

        content.appendChild(chatroomContent);
        app.appendChild(content);

        const chatbox = document.getElementById('chatbox');

        const displayMessage = (msg) => {
            const div = document.createElement('div');
            div.className = 'chat-message';
            div.textContent = `${msg.timestamp || new Date().toLocaleTimeString()}: ${msg.message}`;
            chatbox.appendChild(div);
            chatbox.scrollTop = chatbox.scrollHeight;
        };

        async function fetchMessages() {
            const messages = [
                { message: "Welcome to the chatroom!", timestamp: "10:00 AM" },
                { message: "Feel free to chat here.", timestamp: "10:01 AM" }
            ];
            chatbox.innerHTML = '';
            messages.forEach(displayMessage);
        }

        async function sendMessage() {
            const message = document.getElementById('message').value;
            if (!message) return;

            const newMessage = {
                message,
                timestamp: new Date().toLocaleTimeString()
            };
            displayMessage(newMessage); 
            document.getElementById('message').value = ''; 
        }

        document.getElementById('sendButton').addEventListener('click', sendMessage);

        // Load initial chat messages
        fetchMessages();
    });
</script>
   

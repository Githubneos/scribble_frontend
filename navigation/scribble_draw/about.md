---
layout: post
title: Scribble stats
search_exclude: true
description: statistics page
permalink: /about
Author: Keerthan
hide: true
---

## Welcome to Scribble Aboutpage

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
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        font-family: Arial, sans-serif;
    }

    body {
        background: linear-gradient(145deg, #727D73, #AAB99A, #D0DDD0, #F0F0D7);
        color: #333;
        line-height: 1.6;
        text-align: center;
    }

    .container {
        max-width: 1100px;
        margin: 0 auto;
        padding: 20px;
    }

    header {
        background: #4CAF50;
        color: #fff;
        padding: 10px 0;
    }

    header h1 {
        font-size: 2.5em;
    }

    .about-section {
        margin: 30px 0;
        text-align: justify;
    }

    .about-section h2 {
        font-size: 2em;
        margin-bottom: 10px;
        color: #4CAF50;
        text-align: center;
    }

    .devs {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 20px;
        margin-top: 20px;
    }

    .dev-card {
        background: #fff;
        border-radius: 8px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        padding: 20px;
        text-align: center;
        width: 180px;
        transition: transform 0.3s ease;
    }

    .dev-card:hover {
        transform: scale(1.05);
    }

    .dev-card img {
        width: 80px;
        height: 80px;
        border-radius: 50%;
        object-fit: cover;
        margin-bottom: 10px;
    }

    .dev-card h3 {
        font-size: 1.2em;
        color: #333;
    }

    .footer-text {
        margin-top: 30px;
        background: #333;
        color: #fff;
        padding: 10px 0;
        text-align: center;
    }

    .footer-text a {
        color: #4CAF50;
        text-decoration: none;
    }
</style>

<div class="container">
    <h2>Meet the Developers</h2>
    <div class="devs">
        <div class="dev-card">
            <h3>Keerthan</h3>
            <p>Scrum Master</p>
        </div>
        <div class="dev-card">
            <h3>Zach</h3>
            <p>Backend Developer</p>
        </div>
        <div class="dev-card">
            <h3>Ian</h3>
            <p>Frontend Developer</p>
        </div>
        <div class="dev-card">
            <h3>Max</h3>
            <p>Backend Developer</p>
        </div>
        <div class="dev-card">
            <h3>Daksha</h3>
            <p>Assistant Scrum Master</p>
        </div>
    </div>
</div>

<div class="footer-text">
    <p>&copy; 2024 Scribble Art. Designed and developed by <a href="#">Keerthan, Zach, Ian, Max, Daksha</a>.</p>
</div>


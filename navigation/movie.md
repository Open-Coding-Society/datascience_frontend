---
layout: post
title: AI Movie Recommender
search_exclude: true
permalink: /movie/
---

<html>
<head>
  <title>Movie Recommender 🎬</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: rgb(119, 158, 240);
      color:rgb(142, 197, 234);
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 40px;
    }
    .container {
      background: rgb(119, 158, 240);
      padding: 30px 40px;
      border-radius: 15px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      max-width: 400px;
      width: 100%;
    }
    h1 {
      color: rgb(0, 0, 0);
      font-size: 2em;
      margin-bottom: 20px;
    }
    h2 {
      color: rgb(10, 37, 89);
      font-size: 2em;
      margin-bottom: 20px;
    }
    form {
      background: rgb(113, 158, 231);
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
      width: 100%;
      max-width: 400px;
    }
    label {
      display: block;
      margin-top: 15px;
      font-weight: bold;
      color: #ddd;
    }
    select, input {
      width: 100%;
      padding: 8px;
      margin-top: 5px;
      border: 1px solid #444;
      border-radius: 6px;
      background: #2a2a2a;
      color: white;
    }
    button {
      margin-top: 20px;
      width: 100%;
      padding: 10px;
      background: #ff3d00;
      border: none;
      color: white;
      font-size: 16px;
      border-radius: 8px;
      cursor: pointer;
      transition: background 0.3s;
    }
    button:hover {
      background: #ff6d00;
    }
    #result {
      margin-top: 30px;
      font-size: 1.2em;
      color: #00e676;
      text-align: center;
    }
    ul {
      list-style: none;
      padding: 0;
    }
    li {
      background: #2a2a2a;
      margin: 8px 0;
      padding: 10px;
      border-radius: 6px;
      color: #fff;
    }
  </style>
</head>
<body>
  <h2>🎥 Get Movie Recommendations!</h2>
  <form id="movie-form">
    <label>Favorite Genre:</label>
    <select name="Genre">
      <option>Action</option>
      <option>Comedy</option>
      <option>Horror</option>
      <option>Drama</option>
      <option>Sci-Fi</option>
    </select>
    <label>Favorite Actor:</label>
    <input type="text" name="Actor" required>
    <label>Preferred Duration (minutes):</label>
    <input type="number" name="Duration" required>
    <button type="submit">Recommend</button>
  </form>

  <div id="result"></div>
<script>
import { pythonURI, fetchOptions } from "/assets/js/api/config.js"; 
  document.addEventListener("DOMContentLoaded", function () {
    const form = document.getElementById('movie-form');
    const resultDiv = document.getElementById('result');
    form.addEventListener('submit', async (e) => {
      e.preventDefault();
      const data = {
        Genre: form.Genre.value,
        Actor: form.Actor.value,
        Duration: parseInt(form.Duration.value)
      };
      try {
        const res = await fetch(pythonURI + '/api/movie/recommend', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(data)
        });
        if (!res.ok) {
          throw new Error(`HTTP error! Status: ${res.status}`);
        }
        const result = await res.json();
        if (result.movies && result.movies.length > 0) {
          resultDiv.innerHTML = `<h3>🍿 Recommended Movies:</h3><ul>` +
            result.movies.map(movie => `<li>${movie}</li>`).join('') +
            `</ul>`;
        } else {
          resultDiv.textContent = "😕 No recommendations found.";
        }
      } catch (error) {
        resultDiv.textContent = "🚨 Error fetching recommendations.";
        console.error("Fetch error:", error);
      }
    });
    document.addEventListener("DOMContentLoaded", function () {
  const loginElem = document.getElementById("some-login-element");
  if (loginElem) {
    const attr = loginElem.getAttribute("x");
    // do something
  }
});
  });
</script>

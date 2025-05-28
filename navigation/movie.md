---
layout: post
title: AI Movie Predictor
search_exclude: true
permalink: /movie/
---

<html>
<head>
  <title>Movie Box Office Predictor 🎬</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background:rgb(119, 158, 240);
      color: #f5f5f5;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 40px;
    }
    h2 {
      color:rgb(10, 37, 89);
      font-size: 2em;
      margin-bottom: 20px;
    }
    form {
      background:rgb(113, 158, 231);
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
      font-size: 1.3em;
      color: #00e676;
      text-align: center;
    }
  </style>
</head>
<body>
  <h2>🎥 Will Your Movie Be a Hit?</h2>
  <form id="movie-form">
    <label>Genre:</label>
    <select name="Genre">
      <option>Action</option>
      <option>Comedy</option>
      <option>Horror</option>
      <option>Drama</option>
      <option>Sci-Fi</option>
    </select>
    <label>Budget (in millions):</label>
    <input type="number" name="Budget" required>
    <label>Lead Actor Fame (1–5):</label>
    <input type="number" name="Fame" min="1" max="5" required>
    <label>Meme Potential (0–100):</label>
    <input type="number" name="MemePotential" min="0" max="100" required>
    <button type="submit">Predict</button>
  </form>

  <h3 id="result"></h3>

  <script>
    document.getElementById('movie-form').addEventListener('submit', async (e) => {
      e.preventDefault();
      const form = e.target;
      const data = {
        Genre: form.Genre.value,
        Budget: parseInt(form.Budget.value),
        Fame: parseInt(form.Fame.value),
        MemePotential: parseInt(form.MemePotential.value)
      };
      const res = await fetch('http://127.0.0.1:8887/api/movie/guess', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
      });

      const result = await res.json();
      document.getElementById('result').textContent = `🎬 Your movie will be a: ${result.result}`;
    });
  </script>
</body>
</html>

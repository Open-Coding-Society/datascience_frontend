---
layout: post
title: AI Movie Predictor
search_exclude: true
permalink: /movie/
---

<html>
<head>
  <title>Movie Box Office Predictor 🎬</title>
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
    </select><br><br>
    <label>Budget (in millions):</label>
    <input type="number" name="Budget" required><br><br>
    <label>Lead Actor Fame (1–5):</label>
    <input type="number" name="Fame" min="1" max="5" required><br><br>
    <label>Meme Potential (0–100):</label>
    <input type="number" name="MemePotential" min="0" max="100" required><br><br>
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

      const res = await fetch('/predict', {
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

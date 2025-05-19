---
layout: post
title: AI Pet Model
search_exclude: true
permalink: /train/
---

  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>ML Pet Trainer</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 2rem;
    }
    label, input {
      display: block;
      margin: 0.5rem 0;
    }
    button {
      margin-top: 1rem;
    }
    #result, #importance {
      margin-top: 2rem;
    }
  </style>


  <h1>ML Pet Trainer 🐶</h1>

  <form id="petForm">
    <label for="food">Food (1–5):</label>
    <input type="number" id="food" name="food" min="1" max="5" required>
    <label for="play">Play (1–5):</label>
    <input type="number" id="play" name="play" min="1" max="5" required>
    <label for="sleep">Sleep (1–5):</label>
    <input type="number" id="sleep" name="sleep" min="1" max="5" required>
    <button type="submit">Predict Happiness</button>
  </form>

  <div id="result"></div>
  <div id="importance"></div>

  <script>
    const form = document.getElementById('petForm');
    const resultDiv = document.getElementById('result');
    const importanceDiv = document.getElementById('importance');

    // Fetch feature importance on load
    fetch('http://localhost:8887/api/pet/features')
      .then(res => res.json())
      .then(data => {
        let html = '<h3>Feature Importance</h3><ul>';
        for (const [feature, value] of Object.entries(data)) {
          html += `<li><strong>${feature}</strong>: ${value}</li>`;
        }
        html += '</ul>';
        importanceDiv.innerHTML = html;
      });

    form.addEventListener('submit', async (e) => {
      e.preventDefault();

      const food = parseInt(document.getElementById('food').value);
      const play = parseInt(document.getElementById('play').value);
      const sleep = parseInt(document.getElementById('sleep').value);

      const response = await fetch('http://localhost:8887/api/pet/predict', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ food, play, sleep })
      });

      const data = await response.json();
      resultDiv.innerHTML = `<h3>Predicted Happiness: ${data.predicted_happiness}</h3>`;
    });
  </script>


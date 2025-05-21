---
layout: post
title: Datascience Chart Game 
search_exclude: true
permalink: /chart/
---

<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Guess the Chart</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background-color: #f0f9ff;
      text-align: center;
      padding: 2rem;
      color: #0c4a6e;
    }
    .chart-box {
      margin: 1rem auto;
      background: white;
      border-radius: 1rem;
      padding: 1rem;
      width: 320px;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
    }
    canvas {
      margin-top: 1rem;
    }
    input, button {
      padding: 0.5rem;
      margin: 0.5rem;
      border-radius: 0.5rem;
      border: 1px solid #93c5fd;
    }
    button {
      background-color: #38bdf8;
      color: white;
      cursor: pointer;
    }
    #descriptions {
      margin-top: 1rem;
      padding: 1rem;
      background-color: #e0f2fe;
      border-radius: 0.5rem;
      display: none;
    }
  </style>
</head>
<body>
  <h1>🔍 Guess the Chart</h1>
  <div class="chart-box">
    <canvas id="chartCanvas" width="300" height="200"></canvas>
    <input type="text" id="guessInput" placeholder="What's this chart about?" />
    <button onclick="submitGuess()">Submit Guess</button>
    <p id="scoreDisplay">Score: 0</p>
    <p id="gameOverMsg" style="display:none;"></p>
  </div>

  <div id="descriptions"></div>

  <script>
    const charts = [
      {
        data: [100, 50, 75],
        labels: ["Apples", "Bananas", "Cherries"],
        answer: "Fruit Sales"
      },
      {
        data: [20, 80, 60],
        labels: ["Instagram", "TikTok", "Twitter"],
        answer: "Social Media Usage"
      },
      {
        data: [300, 100, 150],
        labels: ["Dogs", "Cats", "Birds"],
        answer: "Pet Adoption"
      },
      {
        data: [5, 2, 10],
        labels: ["Monday", "Wednesday", "Friday"],
        answer: "Coffee Consumption"
      }
    ];

    let index = 0;
    let score = 0;
    const ctx = document.getElementById('chartCanvas').getContext('2d');

    function drawChart(chart) {
      ctx.clearRect(0, 0, 300, 200);
      const max = Math.max(...chart.data);
      const width = 50;
      const gap = 30;

      chart.data.forEach((value, i) => {
        const height = (value / max) * 150;
        ctx.fillStyle = '#38bdf8';
        ctx.fillRect(i * (width + gap) + 20, 180 - height, width, height);
        ctx.fillStyle = '#0c4a6e';
        ctx.fillText(chart.labels[i], i * (width + gap) + 25, 195);
      });
    }

    function submitGuess() {
      const guess = document.getElementById('guessInput').value.trim().toLowerCase();
      const correct = charts[index].answer.toLowerCase();
      if (guess === correct) score++;
      index++;

      if (index < charts.length) {
        drawChart(charts[index]);
        document.getElementById('guessInput').value = '';
        document.getElementById('scoreDisplay').innerText = `Score: ${score}`;
      } else {
        document.getElementById('scoreDisplay').innerText = `Final Score: ${score}`;
        document.getElementById('gameOverMsg').style.display = 'block';
        document.getElementById('gameOverMsg').innerText = '🎉 Game Over! Score submitted.';
        submitScore(score);
        showDescriptions();
      }
    }

    function submitScore(score) {
      fetch('http://localhost:8887/submit-score', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ score })
      });
    }

    function showDescriptions() {
      fetch('http://localhost:8887/chart-definitions')
        .then(res => res.json())
        .then(definitions => {
          const descBox = document.getElementById('descriptions');
          descBox.innerHTML = "<h3>📊 Chart Descriptions:</h3>";
          definitions.forEach((item, i) => {
            descBox.innerHTML += `<p><strong>${i + 1}. ${item.answer}:</strong> ${item.description}</p>`;
          });
          descBox.style.display = "block";
        });
    }

    drawChart(charts[index]);
  </script>
</body>

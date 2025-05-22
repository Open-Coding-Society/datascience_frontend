---
layout: post
title: Score Predictor
permalink: /score/
---

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<div id="scoreForm" style="background-color:#FFC0CB;padding:2rem;border-radius:12px;max-width:700px;margin:auto; font-size:1.5rem;">
  <label for="mcq" style="font-size:1.75rem;">Multiple Choice:</label>
  <div style="display: flex; align-items: center; gap: 1rem; margin-bottom: 1.5rem;">
    <input type="range" id="mcq" min="0" max="25" value="15" oninput="syncInput('mcq')" style="flex:1;">
    <div style="display:flex; align-items: center; gap: 0.5rem;">
      <input type="number" id="mcqInput" min="0" max="25" value="15" oninput="syncSlider('mcq')" style="width: 80px; font-size:1.5rem;">
      <span>/25</span>
    </div>
  </div>

  <label for="frq" style="font-size:1.75rem;">Free Response:</label>
  <div style="display: flex; align-items: center; gap: 1rem; margin-bottom: 2rem;">
    <input type="range" id="frq" min="0" max="5" value="3" oninput="syncInput('frq')" style="flex:1;">
    <div style="display:flex; align-items: center; gap: 0.5rem;">
      <input type="number" id="frqInput" min="0" max="5" value="3" oninput="syncSlider('frq')" style="width: 80px; font-size:1.5rem;">
      <span>/5</span>
    </div>
  </div>

  <div id="summary" style="margin-top:2rem;">
    <h3 style="font-size:2rem;">Score Summary</h3>
    <p><strong>MCQ Score:</strong> <span id="mcqScore">50%</span></p>
    <p><strong>FRQ Score:</strong> <span id="frqScore">50%</span></p>
    <p><strong>Total Score:</strong> <span id="totalScore">50%</span></p>
  </div>
  <canvas id="percentileChart" width="600" height="300" style="margin-top: 3rem;"></canvas>
</div>

<script>
function syncInput(type) {
  const val = parseInt(document.getElementById(type).value);
  document.getElementById(`${type}Input`).value = val;
  updateScores();
}

function syncSlider(type) {
  const val = parseInt(document.getElementById(`${type}Input`).value);
  const max = parseInt(document.getElementById(type).max);
  const boundedVal = Math.min(Math.max(0, val), max);
  document.getElementById(type).value = boundedVal;
  updateScores();
}

function updateScores() {
  const mcq = parseInt(document.getElementById('mcq').value);
  const frq = parseInt(document.getElementById('frq').value);

  const mcqPercent = Math.round((mcq / 25) * 100);
  const frqPercent = Math.round((frq / 5) * 100);
  const totalRaw = (mcq / 25 * 0.5) + (frq / 5 * 0.5);
  const totalPercent = Math.round(totalRaw * 100);

  let predictedScore = 1;
  if (totalPercent >= 90) predictedScore = 5;
  else if (totalPercent >= 75) predictedScore = 4;
  else if (totalPercent >= 60) predictedScore = 3;
  else if (totalPercent >= 45) predictedScore = 2;

  document.getElementById('mcqScore').textContent = `${mcqPercent}%`;
  document.getElementById('frqScore').textContent = `${frqPercent}%`;
  document.getElementById('totalScore').textContent = `${totalPercent}%`;

  // Call backend for percentiles
  fetch('/api/percentile', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ mcq: mcq, frq: frq })
  })
  .then(response => response.json())
  .then(data => {
    // Update chart with actual percentiles
    percentileChart.data.datasets[0].data = [
      data.mcq_percentile,
      data.frq_percentile,
      totalPercent,
      100 - data.mcq_percentile,
      100 - data.frq_percentile
    ];
    percentileChart.update();
  })
  .catch(error => {
    console.error('Error fetching percentile:', error);
  });
}

// Initialize
updateScores();

const ctx = document.getElementById('percentileChart').getContext('2d');
const percentileChart = new Chart(ctx, {
  type: 'bar',
  data: {
    labels: ['MCQ %ile', 'FRQ %ile', 'Total %', 'MCQ Gap', 'FRQ Gap'],
    datasets: [{
      label: 'Live Percentile Data',
      data: [50, 50, 50, 50, 50],
      backgroundColor: '#FF69B4',
      borderRadius: 10
    }]
  },
  options: {
    responsive: true,
    scales: {
      y: {
        beginAtZero: true,
        max: 100,
        title: {
          display: true,
          text: 'Percentile / Score %'
        }
      },
      x: {
        title: {
          display: true,
          text: 'Score Components'
        }
      }
    },
    plugins: {
      legend: {
        display: false
      }
    }
  }
});
</script>

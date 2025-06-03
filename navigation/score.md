---
layout: post
title: Score Predictor
permalink: /score/
---

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<div id="scoreForm" style="background-color:#FFC0CB;padding:2rem;border-radius:12px;max-width:700px;margin:auto; font-size:1.5rem;">
<!-- Help Button in top-right of pink box -->
<div style="position: relative;">
  <div style="position: absolute; top: 0.5rem; right: 0.5rem;">
    <div style="position: relative;">
      <button id="helpBtn" style="background-color: white; border: 2px solid #333; border-radius: 50%; width: 35px; height: 35px; font-weight: bold; font-size: 1.2rem; cursor: help;">?</button>
      <div id="tooltip" style="display: none; position: absolute; top: 40px; right: 0; background: rgb(65, 83, 201); color: white; border: 1px solid #ccc; border-radius: 12px; padding: 1rem; width: 400px; font-size: 1rem; box-shadow: 0px 4px 10px rgba(0,0,0,0.1); z-index: 10; white-space: normal; word-wrap: break-word;">
        <strong>How it Works:</strong>
        <p> The backend uses scikit-learn’s QuantileTransformer to convert raw MCQ and FRQ scores into percentile values by learning from a dataset of past scores. When users input their scores on the frontend, the data is sent to a Flask API, where the backend applies the transformer to map the scores to a uniform distribution. This effectively shows each user's percentile ranking compared to the data, which is then returned and displayed on the frontend.
        <a href="../scoreblog/" target="_blank" rel="noopener noreferrer">
        View the how the score predictor work, step-by-step!
        </a>
        </p>
      </div>
    </div>
  </div>
</div>
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

<div id="percentileInfo" style="margin-top: 1.5rem; font-size: 1.5rem;"></div>

<script>
// Hover effect for the "?" tooltip
const helpButton = document.querySelector('button');
const tooltip = helpButton.nextElementSibling;

helpButton.addEventListener('mouseenter', () => {
  tooltip.style.display = 'block';
});

helpButton.addEventListener('mouseleave', () => {
  tooltip.style.display = 'none';
});

tooltip.addEventListener('mouseenter', () => {
  tooltip.style.display = 'block';
});
tooltip.addEventListener('mouseleave', () => {
  tooltip.style.display = 'none';
});

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

async function updateScores() {
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
  try {
    const response = await fetch('http://localhost:8887/api/score/percentile', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ mcq: mcq, frq: frq })
    });

    if (!response.ok) throw new Error('Network response was not ok');

    const data = await response.json();

    // Update chart with actual percentiles
    percentileChart.data.datasets[0].data = [
      100 - data.mcq_percentile,  // Score 1
      100 - (data.mcq_percentile * 0.8),  // Score 2
      100 - (data.frq_percentile * 0.5),  // Score 3
      data.mcq_percentile,  // Score 4
      data.frq_percentile   // Score 5
    ];
    percentileChart.update();
  } catch (error) {
    console.error('Error fetching percentile:', error);
  }
}

// Initialize

const ctx = document.getElementById('percentileChart').getContext('2d');
const percentileChart = new Chart(ctx, {
  type: 'bar',
  data: {
    labels: ['MCQ %tile', 'FRQ %tile', 'Total %', 'MCQ Gap', 'FRQ Gap'],
    datasets: [{
      label: 'Live Percentile Data',
      data: [50, 50, 50, 50, 50],
      backgroundColor: '#1E90FF',
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
          text: 'Score Range'
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
updateScores();

</script>

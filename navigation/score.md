---
layout: post
title: Score Predictor
permalink: /score/
---

<div id="scoreForm" style="background-color:#FFC0CB;padding:1rem;border-radius:12px;max-width:700px;margin:auto;">
  <label for="mcq">Multiple Choice:</label>
  <div style="display: flex; align-items: center; gap: 1rem;">
    <input type="range" id="mcq" min="0" max="25" value="15" oninput="syncInput('mcq')">
    <div style="display:flex; align-items: center; gap: 0.25rem;">
      <input type="number" id="mcqInput" min="0" max="25" value="15" oninput="syncSlider('mcq')">
      <span>/25</span>
    </div>
  </div>

  <label for="frq" style="margin-top:1rem;">Free Response:</label>
  <div style="display: flex; align-items: center; gap: 1rem;">
    <input type="range" id="frq" min="0" max="5" value="3" oninput="syncInput('frq')">
    <div style="display:flex; align-items: center; gap: 0.25rem;">
      <input type="number" id="frqInput" min="0" max="5" value="3" oninput="syncSlider('frq')">
      <span>/5</span>
    </div>
  </div>

  <div id="summary" style="margin-top:2rem;">
    <h3>Score Summary</h3>
    <p><strong>MCQ Score:</strong> <span id="mcqScore">50%</span></p>
    <p><strong>FRQ Score:</strong> <span id="frqScore">50%</span></p>
    <p><strong>Total Score:</strong> <span id="totalScore">50%</span></p>
  </div>
</div>

  <div id="summary" style="margin-top:2rem;">
    <h3>Score Summary</h3>
    <p><strong>MCQ Score:</strong> <span id="mcqScore">50%</span></p>
    <p><strong>FRQ Score:</strong> <span id="frqScore">50%</span></p>
    <p><strong>Total Score:</strong> <span id="totalScore">50%</span></p>
  </div>
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

  const mcqPercent = Math.round((mcq / 60) * 100);
  const frqPercent = Math.round((frq / 40) * 100);
  const totalRaw = (mcq / 60 * 0.5) + (frq / 40 * 0.5);
  const totalPercent = Math.round(totalRaw * 100);

  let predictedScore = 1;
  if (totalPercent >= 90) predictedScore = 5;
  else if (totalPercent >= 75) predictedScore = 4;
  else if (totalPercent >= 60) predictedScore = 3;
  else if (totalPercent >= 45) predictedScore = 2;

  document.getElementById('mcqScore').textContent = `${mcqPercent}%`;
  document.getElementById('frqScore').textContent = `${frqPercent}%`;
  document.getElementById('totalScore').textContent = `${totalPercent}%`;
}

// Initialize
updateScores();
</script>

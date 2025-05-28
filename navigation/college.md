---
layout: post
title: College Acceptance Model
search_exclude: true
permalink: /college/
---

<h1>UC College Acceptance Predictor</h1>

<div>
  <label>GPA: <input type="number" step="0.01" id="gpa" /></label><br />
  <label>SAT: <input type="number" id="sat" /></label><br />
  <label>ACT: <input type="number" id="act" /></label><br />
  <label>AP Classes Taken: <input type="number" id="apCount" /></label><br />
  <label>Extracurricular Score (1-10): <input type="number" id="extracurriculars" /></label><br />
  <button onclick="predict()">Predict</button>
</div>

<div id="results" style="margin-top: 20px;"></div>

<script>
  async function predict() {
    const data = {
      gpa: parseFloat(document.getElementById('gpa').value),
      sat: parseInt(document.getElementById('sat').value),
      act: parseInt(document.getElementById('act').value),
      apCount: parseInt(document.getElementById('apCount').value),
      extracurriculars: parseInt(document.getElementById('extracurriculars').value),
    };

    const response = await fetch('/api/college/chance', {  // updated endpoint
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data),
    });

    const result = await response.json();

    // Since your current backend returns one prediction:
    const chance = result.predicted_chance;
    let html = `<h2>Predicted Acceptance Chance</h2><p>${chance}%</p>`;

    document.getElementById('results').innerHTML = html;
  }
</script>

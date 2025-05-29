---
layout: post
title: College Acceptance Model
search_exclude: true
permalink: /college/
---



<h1 class="text-4xl font-bold mb-6 text-center text-blue-700">🎓 UC College Acceptance Predictor</h1>

<div class="max-w-lg mx-auto p-6 bg-silver rounded-2xl shadow-lg space-y-4">
  <div>
    <label class="block font-semibold mb-1">📚 GPA</label>
    <input type="number" step="0.01" id="gpa" class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400" />
  </div>
  <div>
    <label class="block font-semibold mb-1">📝 SAT Score</label>
    <input type="number" id="sat" class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400" />
  </div>
  <div>
    <label class="block font-semibold mb-1">🧠 ACT Score</label>
    <input type="number" id="act" class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400" />
  </div>
  <div>
    <label class="block font-semibold mb-1">📘 AP Classes Taken</label>
    <input type="number" id="apCount" class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400" />
  </div>
  <div>
    <label class="block font-semibold mb-1">🎭 Extracurricular Score (1-10)</label>
    <input type="number" id="extracurriculars" class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400" />
  </div>
  <div class="text-center">
    <button onclick="predict()" class="mt-4 px-6 py-2 bg-blue-600 text-white font-semibold rounded-xl hover:bg-blue-700 transition">🔮 Predict</button>
  </div>
</div>

<div id="results" class="max-w-lg mx-auto mt-6 text-center text-lg font-medium"></div>

<script>
  async function predict() {
    const data = {
      gpa: parseFloat(document.getElementById('gpa').value),
      sat: parseInt(document.getElementById('sat').value),
      act: parseInt(document.getElementById('act').value),
      apCount: parseInt(document.getElementById('apCount').value),
      extracurriculars: parseInt(document.getElementById('extracurriculars').value),
    };

    const response = await fetch('http://localhost:8887/api/college/chance', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data),
    });

    const result = await response.json();
    const chance = result.predicted_chance;
    
    document.getElementById('results').innerHTML = `
      <div class="p-4 mt-4 bg-green-100 text-green-800 rounded-xl shadow">
        <h2 class="text-2xl font-bold mb-2">🌟 Predicted Acceptance Chance</h2>
        <p class="text-3xl">${chance}%</p>
      </div>`;
  }
</script>

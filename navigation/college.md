---
layout: post
title: College Acceptance Model
search_exclude: true
permalink: /college/
---


<h1 class="text-4xl font-bold mb-6 text-center text-blue-700">🎓 UC College Acceptance Predictor</h1>


<div class="max-w-lg mx-auto p-6 bg-silver rounded-2xl shadow-lg space-y-4">
<div style="position: relative;">
  <div style="position: absolute; top: 0.5rem; right: 0.5rem;">
    <div style="position: relative;">
      <button id="helpBtn" style="background-color: white; border: 2px solid #333; border-radius: 50%; width: 35px; height: 35px; font-weight: bold; font-size: 1.2rem; cursor: help;">?</button>
        <div id="tooltip" style="display: none; position: absolute; top: 40px; right: 0; background: rgb(65, 83, 201); color: white; border: 1px solid #ccc; border-radius: 12px; padding: 1rem; width: 400px; font-size: 1rem; box-shadow: 0px 4px 10px rgba(0,0,0,0.1); z-index: 10; white-space: normal; word-wrap: break-word;">
        <strong>How it Works:</strong>
        <p> This college predictor model is a practical tool for future students to estimate their admission chances using real-world data, showcasing how data science transforms complex datasets into meaningful, personalized insights for informed decision-making.
        </p>
      </div>
    </div>
  </div>
</div>
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
    let chance = result.predicted_chance;
chance = Math.max(0, Math.min(100, chance)); // clamp between 0 and 100
function getLikelyUC(chance) {
  if (chance >= 90) return "UC Berkeley or UCLA 🎉 (Top Tier)";
  if (chance >= 75) return "UC San Diego or UC Irvine 🌟 (Highly Competitive)";
  if (chance >= 60) return "UC Davis or UC Santa Barbara ✅ (Solid Match)";
  if (chance >= 45) return "UC Riverside or UC Santa Cruz 💪 (Likely)";
  if (chance >= 30) return "UC Merced 🌱 (Safe Option)";
  return "A UC might be tough with current stats – but never say never! 💫";
}

    
    const likelyUC = getLikelyUC(chance);

document.getElementById('results').innerHTML = `
  <div class="p-4 mt-4 bg-green-100 text-green-800 rounded-xl shadow">
    <h2 class="text-2xl font-bold mb-2">🌟 Predicted Acceptance Chance</h2>
    <p class="text-3xl mb-2">${chance}%</p>
    <p class="text-lg">🎯 Best UC match: <strong>${likelyUC}</strong></p>
  </div>`;

  }
</script>

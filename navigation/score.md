---
layout: post
title: Score Predictor
search_exclude: true
permalink: /score/
---
<h1>Score Predictor 🎯</h1>
<p>Enter data to predict your score!</p>

<form id="studentForm">
  <label for="study">Study Hours (0–10):</label>
  <input type="number" id="study" min="0" max="10" required>
  
  <label for="sleep">Sleep Hours (0–10):</label>
  <input type="number" id="sleep" min="0" max="10" required>
  
  <label for="participation">Participation (0–5):</label>
  <input type="number" id="participation" min="0" max="5" required>
  
  <button type="submit">Predict Success</button>
</form>

<div id="predictionResult"></div>
<progress id="successBar" value="0" max="100"></progress>
<img id="avatar" src="neutral.png" alt="Student Avatar" />
<p id="studentMessage"></p>

<script>
  document.getElementById('studentForm').addEventListener('submit', async (e) => {
    e.preventDefault();

    const study = parseInt(document.getElementById('study').value);
    const sleep = parseInt(document.getElementById('sleep').value);
    const participation = parseInt(document.getElementById('participation').value);

    const res = await fetch('http://localhost:8887/api/student/predict', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ study, sleep, participation })
    });

    const data = await res.json();
    const probability = data.success_probability;

    document.getElementById('predictionResult').innerText = `Success Probability: ${probability}%`;
    document.getElementById('successBar').value = probability;

    const avatar = document.getElementById('avatar');
    const msg = document.getElementById('studentMessage');
    if (probability > 75) {
      avatar.src = 'happy_student.png';
      msg.textContent = "You're ready to ace it! 💯";
    } else if (probability < 40) {
      avatar.src = 'tired_student.png';
      msg.textContent = "You might need a break... 😴";
    } else {
      avatar.src = 'neutral_student.png';
      msg.textContent = "Keep going! 📘";
    }
  });
</script>

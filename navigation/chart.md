---
layout: post
title: Surviving Sickness 
search_exclude: true
permalink: /chart/
---

<style>
  body {
    font-family: sans-serif;
    background-color: #1b2e1b; /* dark green */
    padding: 0; /* Remove extra padding */
    display: flex;
    flex-direction: column; /* Stack elements vertically */
    align-items: center; /* Center horizontally */
    height: 100vh; /* Full viewport height */
    margin: 0; /* Remove default margin */
  }
  h1 {
    display: none; /* Hide the title */
  }
  h2.module-title {
    margin-top: 20px; /* Add spacing at the top */
    color: #ffffff; /* White text for contrast */
    font-size: 2.5rem; /* Adjust font size */
    text-align: center;
    font-weight: bold; /* Make the title more prominent */
    text-shadow: 1px 1px 3px #006400; /* Add shadow for depth */
    background: linear-gradient(90deg, #2ecc71, #27ae60); /* Gradient background */
    padding: 10px; /* Add padding around the text */
    border-radius: 8px; /* Rounded corners */
  }
  form {
    background: #2f4f2f; /* forest green */
    padding: 30px; /* Reduce padding */
    border-radius: 16px;
    box-shadow: 0 0 20px #006400; /* dark green glow */
    width: 500px; /* Increase width */
    color: #e0ffe0; /* light green text */
    margin-top: 20px; /* Add spacing below the title */
  }
  label, select, input {
    display: block;
    margin-top: 15px; /* Reduce spacing */
    width: 100%;
    font-size: 16px;
  }
  input, select {
    padding: 10px;
    border-radius: 6px;
    border: 1px solid #88cc88;
    background-color: #eaffea;
  }
  button {
    margin-top: 20px; /* Adjust spacing */
    background: #228b22; /* forest green */
    color: white;
    padding: 12px;
    border: none;
    border-radius: 8px;
    font-size: 16px;
    cursor: pointer;
  }
  button:hover {
    background-color: #2ecc71; /* emerald green */
  }
  #result {
    margin-top: 20px; /* Adjust spacing */
    text-align: center;
    font-weight: bold;
    color: #b0ffb0;
    font-size: 1.2rem;
  }
  h2 {
    text-align: center;
    color: #ccffcc;
    font-size: 1.8rem;
  }
</style>

<h2 class="module-title">Survival Sickness</h2>
<form id="form">
  <h2>🧬 Predict Survival </h2>
  <label>Age: <input type="number" id="age" required /></label>
  <label>Sex:
    <select id="sex">
      <option value="male">Male</option>
      <option value="female">Female</option>
    </select>
  </label>
  <label>Fever: <select id="fever"><option value="1">Yes</option><option value="0">No</option></select></label>
  <label>Cough: <select id="cough"><option value="1">Yes</option><option value="0">No</option></select></label>
  <label>Shortness of Breath: <select id="shortness_of_breath"><option value="1">Yes</option><option value="0">No</option></select></label>
  <label>Chest Pain: <select id="chest_pain"><option value="1">Yes</option><option value="0">No</option></select></label>
  <label>Comorbidities: <select id="comorbidities"><option value="1">Yes</option><option value="0">No</option></select></label>
  <button type="submit">Predict</button>
  <div id="result"></div>
</form>

<script>
  document.getElementById('form').addEventListener('submit', async e => {
    e.preventDefault();
    const payload = {
      age: parseInt(document.getElementById('age').value),
      sex: document.getElementById('sex').value,
      fever: parseInt(document.getElementById('fever').value),
      cough: parseInt(document.getElementById('cough').value),
      shortness_of_breath: parseInt(document.getElementById('shortness_of_breath').value),
      chest_pain: parseInt(document.getElementById('chest_pain').value),
      comorbidities: parseInt(document.getElementById('comorbidities').value)
    };
    const res = await fetch('http://127.0.0.1:8887/api/influenza/survive-or-thrive', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    });
    const data = await res.json();
    document.getElementById('result').innerHTML = `
      🟢 Survival Chance: ${data.survived * 100}%<br>
      🔴 Death Risk: ${data.died * 100}%`;
  });

  const helpBtn = document.getElementById('helpBtn');
  const tooltip = document.getElementById('tooltip');
  helpBtn.addEventListener('mouseenter', () => {
    tooltip.style.display = 'block';
  });
  helpBtn.addEventListener('mouseleave', () => {
    tooltip.style.display = 'none';
  });
</script>

<div style="position: relative;">
  <div style="position: absolute; top: 1rem; right: 1rem;">
    <div style="position: relative;">
      <button id="helpBtn" style="background-color: white; border: 2px solid #333; border-radius: 50%; width: 35px; height: 35px; font-weight: bold; font-size: 1.2rem; cursor: help;">?</button>
      <div id="tooltip" style="display: none; position: absolute; top: 40px; right: 0; background: rgb(65, 83, 201); color: white; border: 1px solid #ccc; border-radius: 12px; padding: 1rem; width: 400px; font-size: 1rem; box-shadow: 0px 4px 10px rgba(0,0,0,0.1); z-index: 10;">
        <strong>How it Works:</strong>
        <p>
          This feature uses a logistic regression model trained on an influenza dataset similar to those from the UCI Machine Learning Repository.
          The model analyzes symptoms like fever, cough, chest pain, and other factors (including age, sex, and comorbidities) to estimate
          the probability of survival. The inputs are processed in Python via a Flask backend, which returns a prediction to this page.
        </p>
      </div>
    </div>
  </div>
</div>

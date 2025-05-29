---
layout: post
title: Surviving Sickness 
search_exclude: true
permalink: /chart/
---
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Survival Predictor</title>
  <style>
    body {
      font-family: sans-serif;
      background-color: #ffe4e1;
      padding: 20px;
      display: flex;
      justify-content: center;
    }
    form {
      background: #fff0f5;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 0 10px #ffc0cb;
      width: 300px;
    }
    label, select, input {
      display: block;
      margin-top: 15px;
      width: 100%;
    }
    button {
      margin-top: 20px;
      background: #ff69b4;
      color: white;
      padding: 10px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }
    #result {
      margin-top: 20px;
      text-align: center;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <form id="form">
    <h2>Predict Survival</h2>
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

      const res = await fetch('http://127.0.0.1:8887/api/influenza/predict', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });
      const data = await res.json();
      document.getElementById('result').innerHTML = `
        Survival: ${data.survived * 100}%<br>
        Death: ${data.died * 100}%`;
    });
  </script>
</body>
</html>


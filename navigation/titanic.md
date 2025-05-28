---
layout: post
title: titanic ml
search_exclude: true
permalink: /titanic/
---


<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Titanic Survival Prediction</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #121212;
      padding: 20px;
      color: white;
    }
    .container {
      max-width: 500px;
      margin: auto;
      background: #000;
      padding: 30px;
      border-radius: 15px;
      box-shadow: 0 4px 8px rgba(255, 255, 255, 0.1);
    }
    h2 {
      text-align: center;
      color: white;
    }
    label {
      display: block;
      margin-top: 15px;
      color: white;
    }
    input, select {
      width: 100%;
      padding: 8px;
      margin-top: 5px;
      border-radius: 5px;
      border: 1px solid #666;
      background-color: #1e1e1e;
      color: white;
    }
    button {
      width: 100%;
      padding: 12px;
      margin-top: 20px;
      background-color: #007BFF;
      color: white;
      border: none;
      border-radius: 5px;
      font-size: 16px;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
    .result {
      margin-top: 20px;
      text-align: center;
      font-size: 18px;
      padding: 10px;
      background-color: #1e1e1e;
      border-radius: 10px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>Titanic Survival Predictor</h2>
    <form id="titanic-form">
      <label for="name">Name:</label>
      <input type="text" id="name" required />

      <label for="pclass">Passenger Class:</label>
      <select id="pclass" required>
        <option value="1">1st</option>
        <option value="2">2nd</option>
        <option value="3">3rd</option>
      </select>

      <label for="sex">Sex:</label>
      <select id="sex" required>
        <option value="male">Male</option>
        <option value="female">Female</option>
      </select>

      <label for="age">Age:</label>
      <input type="number" id="age" min="0" step="any" required />

      <label for="sibsp">Siblings/Spouses Aboard:</label>
      <input type="number" id="sibsp" min="0" required />

      <label for="parch">Parents/Children Aboard:</label>
      <input type="number" id="parch" min="0" required />

      <label for="fare">Fare Paid:</label>
      <input type="number" id="fare" step="any" min="0" required />

      <label for="embarked">Embarked Port:</label>
      <select id="embarked" required>
        <option value="C">Cherbourg</option>
        <option value="Q">Queenstown</option>
        <option value="S">Southampton</option>
      </select>

      <div class="checkbox-group">
        <input type="checkbox" id="alone" />
        <label for="alone">Traveling Alone</label>
      </div>

      <button type="submit">Predict Survival</button>
    </form>

    <div class="result" id="result"></div>
  </div>

  <script>
    document.getElementById("titanic-form").addEventListener("submit", async function (e) {
      e.preventDefault();

      const data = {
        name: [document.getElementById("name").value],
        pclass: [parseInt(document.getElementById("pclass").value)],
        sex: [document.getElementById("sex").value],
        age: [parseFloat(document.getElementById("age").value)],
        sibsp: [parseInt(document.getElementById("sibsp").value)],
        parch: [parseInt(document.getElementById("parch").value)],
        fare: [parseFloat(document.getElementById("fare").value)],
        embarked: [document.getElementById("embarked").value],
        alone: [document.getElementById("alone").checked]
      };

      const responseBox = document.getElementById("result");
      responseBox.innerHTML = "Predicting...";

      try {
        const res = await fetch("http://127.0.0.1:8887/api/titanic/predict", {          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(data)
        });

        if (!res.ok) throw new Error("Prediction failed");

        const result = await res.json();
        const die = (result.die * 100).toFixed(2);
        const survive = (result.survive * 100).toFixed(2);

        responseBox.innerHTML = `
          <strong>Prediction:</strong><br/>
          <span style="color: red;">Death Probability: ${die}%</span><br/>
          <span style="color: lime;">Survival Probability: ${survive}%</span>
        `;
      } catch (err) {
        responseBox.innerHTML = "Error: Could not get prediction.";
        console.error(err);
      }
    });
  </script>
</body>

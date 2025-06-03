---
layout: post
title: Titanic ML
search_exclude: true
permalink: /titanic/
---



<h2>Titanic Survival Prediction</h2>

<div style="position: relative;">
  <div style="position: absolute; top: 0.5rem; right: 0.5rem;">
    <div style="position: relative;">
      <button id="helpBtn" style="background-color: white; border: 2px solid #333; border-radius: 50%; width: 35px; height: 35px; font-weight: bold; font-size: 1.2rem; cursor: help;">?</button>
      <div id="tooltip" style="display: none; position: absolute; top: 40px; right: 0; background: rgb(65, 83, 201); color: white; border: 1px solid #ccc; border-radius: 12px; padding: 1rem; width: 400px; font-size: 1rem; box-shadow: 0px 4px 10px rgba(0,0,0,0.1); z-index: 10; white-space: normal; word-wrap: break-word;">
        <strong>How it Works:</strong>
        <p> The model loads and cleans Titanic dataset, converting categorical values and encoding ports of embarkation.It trains a logistic regression model to predict survival, and a decision tree to evaluate feature importance. A singleton pattern ensures the model is trained once and reused for predictions.Predictions are made by transforming user input and outputting survival and death probabilities.
        <a href="../scoreblog/" target="_blank" rel="noopener noreferrer">
        View the how the score predictor work, step-by-step!
        </a>
        </p>
      </div>
    </div>
  </div>
</div>

<form id="titanic-form">
  <label for="name">Your Name:</label>
  <input type="text" id="name" required />

  <label for="pclass">Passenger Class (1 = 1st, 2 = 2nd, 3 = 3rd):</label>
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
  <input type="number" id="age" min="0" step="0.1" required />

  <label for="sibsp">Number of Siblings/Spouses Aboard:</label>
  <input type="number" id="sibsp" min="0" required />

  <label for="parch">Number of Parents/Children Aboard:</label>
  <input type="number" id="parch" min="0" required />

  <label for="fare">Fare Paid:</label>
  <input type="number" id="fare" min="0" step="0.01" required />

  <label for="embarked">Port of Embarkation:</label>
  <select id="embarked" required>
    <option value="C">Cherbourg</option>
    <option value="Q">Queenstown</option>
    <option value="S">Southampton</option>
  </select>

  <label for="alone">Traveling Alone:</label>
  <select id="alone" required>
    <option value="true">Yes</option>
    <option value="false">No</option>
  </select>

  <div class="checkbox-group">
    <span class="checkbox-label">Share My Prediction Publicly</span>
    <label class="switch">
      <input type="checkbox" id="share" />
      <span class="slider"></span>
    </label>
  </div>

  <button type="submit">Predict Survival</button>
</form>

<div class="result" id="result"></div>

<canvas id="predictionChart" width="400" height="400" style="margin-top: 20px;"></canvas>

<div class="result" id="shared-results">
  <h3>Public Predictions</h3>
  <ul id="shared-list"></ul>
</div>

<!-- Chart.js CDN -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
  h2, h3 {
    text-align: center;
    color: #ffffff;
  }

  label {
    display: block;
    margin: 10px 0 5px;
    font-weight: bold;
  }

  input, select {
    width: 100%;
    padding: 8px;
    border-radius: 5px;
    border: 1px solid #333;
    background-color: #1e1e1e;
    color: white;
  }

  button {
    margin-top: 15px;
    padding: 10px;
    width: 100%;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 16px;
    cursor: pointer;
  }

  button:hover {
    background-color: #0056b3;
  }

  .result {
    margin-top: 20px;
    padding: 15px;
    background-color: #000;
    border-radius: 8px;
    box-shadow: 0 0 5px rgba(255,255,255,0.1);
  }

  ul {
    list-style-type: none;
    padding-left: 0;
  }

  li {
    margin: 8px 0;
    padding: 6px 10px;
    background: #1f1f1f;
    border-radius: 6px;
    color: #fff;
  }

  .switch {
    position: relative;
    display: inline-block;
    width: 50px;
    height: 24px;
  }

  .switch input {
    opacity: 0;
    width: 0;
    height: 0;
  }

  .slider {
    position: absolute;
    cursor: pointer;
    top: 0; left: 0;
    right: 0; bottom: 0;
    background-color: #ccc;
    transition: 0.4s;
    border-radius: 24px;
  }

  .slider:before {
    position: absolute;
    content: "";
    height: 18px;
    width: 18px;
    left: 3px;
    bottom: 3px;
    background-color: white;
    transition: 0.4s;
    border-radius: 50%;
  }

  input:checked + .slider {
    background-color: #2196F3;
  }

  input:checked + .slider:before {
    transform: translateX(26px);
  }

  .checkbox-group {
    margin-top: 15px;
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .checkbox-label {
    flex-grow: 1;
  }

  canvas {
    display: block;
    margin: 0 auto;
    background-color: #1e1e1e;
    border-radius: 8px;
  }
</style>

<script>
  function updateSharedResults() {
    const shared = JSON.parse(localStorage.getItem("sharedPredictions") || "[]");
    const list = document.getElementById("shared-list");
    list.innerHTML = "";
    shared.forEach(entry => {
      const li = document.createElement("li");
      li.innerHTML = `<strong>${entry.name}</strong>: ${entry.survive}% survive, ${entry.die}% die`;
      list.appendChild(li);
    });
  }

  document.getElementById("titanic-form").addEventListener("submit", async function (e) {
    e.preventDefault();

    const name = document.getElementById("name").value;
    const data = {
      name: [name],
      pclass: [parseInt(document.getElementById("pclass").value)],
      sex: [document.getElementById("sex").value],
      age: [parseFloat(document.getElementById("age").value)],
      sibsp: [parseInt(document.getElementById("sibsp").value)],
      parch: [parseInt(document.getElementById("parch").value)],
      fare: [parseFloat(document.getElementById("fare").value)],
      embarked: [document.getElementById("embarked").value],
      alone: [document.getElementById("alone").value === "true"]
    };

    const share = document.getElementById("share").checked;
    const responseBox = document.getElementById("result");
    responseBox.innerHTML = "Predicting...";

    try {
      const res = await fetch("http://127.0.0.1:8887/api/titanic/predict", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        credentials: "include",
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

      const ctx = document.getElementById("predictionChart").getContext("2d");
      window.predictionChart = new Chart(ctx, {
        type: "pie",
        data: {
          labels: ["Survive", "Die"],
          datasets: [{
            data: [survive, die],
            backgroundColor: ["#00ff00", "#ff0000"]
          }]
        },
        options: {
          responsive: true,
          plugins: {
            legend: {
              position: "bottom",
              labels: { color: "white" }
            },
            title: {
              display: true,
              text: "Survival vs Death Probability",
              color: "white",
              font: { size: 18 }
            }
          }
        }
      });

      if (share) {
        const shared = JSON.parse(localStorage.getItem("sharedPredictions") || "[]");
        shared.push({ name, survive, die });
        localStorage.setItem("sharedPredictions", JSON.stringify(shared));
        updateSharedResults();
      }
    } catch (err) {
      responseBox.innerHTML = "Error: Could not get prediction.";
      console.error(err);
    }
  });

  window.addEventListener("DOMContentLoaded", updateSharedResults);
</script>
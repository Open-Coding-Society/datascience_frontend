---
layout: post
title: AI Travel Recommender
search_exclude: true
permalink: /travel/
---


<!-- Help Button in top-right of pink box -->
<div style="position: relative;">
  <div style="position: absolute; top: 0.5rem; right: 1.5rem;">
    <div style="position: relative;">
      <button id="helpBtn" style="background-color: white; border: 2px solid #333; border-radius: 50%; width: 35px; height: 35px; font-weight: bold; font-size: 1.2rem; cursor: help;">?</button>
      <div id="tooltip" style="display: none; position: absolute; top: 40px; right: 0; background: rgb(65, 83, 201); color: white; border: 1px solid #ccc; border-radius: 12px; padding: 1rem; width: 400px; font-size: 1rem; box-shadow: 0px 4px 10px rgba(0,0,0,0.1); z-index: 10; white-space: normal; word-wrap: break-word;">
        <strong>How it Works:</strong>
        <p> The backend of this feature uses a decision tree model trained on a real-world travel preferences dataset to recommend destinations. When users select their preferred season, activity type, budget, and continent on the frontend, the data is sent to a Flask API. The backend processes these inputs and predicts the most suitable travel destination along with a suggested activity. The recommendation is then returned and dynamically displayed on the frontend, helping users discover ideal locations tailored to their interests.
        <a href="http://127.0.0.1:4887/datascience_frontend/TravelTeach/" target="_blank" rel="noopener noreferrer">
        View the how the travel recommender works, step-by-step!
        </a>
        </p>
      </div>
    </div>
  </div>
</div>

  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Travel Recommendation</title>
<style>
    body {
      font-family: Arial, sans-serif;
      padding: 40px;
      color: #333;
      background: none;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    .container {
      background:rgba(124, 166, 227, 0.91) ;
      padding: 30px 40px;
      border-radius: 15px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      max-width: 400px;
      width: 100%;
    }
    h1 {
      text-align: center;
      margin-bottom: 25px;
      color:rgb(1, 16, 32);
    }
    label {
      display: block;
      margin-top: 15px;
      font-weight: bold;
      color: #222;
    }
    select, button {
      width: 100%;
      padding: 10px;
      margin-top: 5px;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 1rem;
      color: #333;
      box-sizing: border-box;
      transition: border-color 0.3s ease;
    }
    select:focus, button:focus {
      outline: none;
      border-color: #007BFF;
      box-shadow: 0 0 5px #007BFF;
    }
    button {
      margin-top: 25px;
      background-color: #007BFF;
      color: white;
      font-weight: bold;
      cursor: pointer;
      border: none;
      transition: background-color 0.3s ease;
    }
    button:hover {
      background-color: #0056b3;
    }
    #result {
      margin-top: 30px;
      font-size: 1.2rem;
      font-weight: 600;
      text-align: center;
      color: #006400;
      min-height: 1.4em;
    }
</style>

  <div class="container">
    <h1>Find Your Perfect Travel Destination 🌍</h1>
    <form id="travelForm">
      <label for="season">Preferred Season:</label>
      <select id="season" name="season" required>
        <option value="">-- Choose a season --</option>
        <option value="Spring">Spring</option>
        <option value="Summer">Summer</option>
        <option value="Autumn">Autumn</option>
        <option value="Winter">Winter</option>
      </select>
      <label for="activity">Activity Type:</label>
      <select id="activity" name="activity" required>
        <option value="">-- Choose an activity --</option>
        <option value="Adventure">Adventure</option>
        <option value="Relaxation">Relaxation</option>
        <option value="Sightseeing">Sightseeing</option>
        <option value="Cultural">Cultural</option>
        <option value="Beach">Beach</option>
        <option value="Nature">Nature</option>
      </select>
      <label for="budget">Budget:</label>
      <select id="budget" name="budget" required>
        <option value="">-- Choose a budget --</option>
        <option value="Low">Low</option>
        <option value="Medium">Medium</option>
        <option value="High">High</option>
      </select>
      <label for="continent">Preferred Continent:</label>
      <select id="continent" name="continent" required>
        <option value="">-- Choose a continent --</option>
        <option value="Europe">Europe</option>
        <option value="Asia">Asia</option>
        <option value="Africa">Africa</option>
        <option value="North America">North America</option>
        <option value="South America">South America</option>
        <option value="Oceania">Oceania</option>
      </select>
      <button type="submit">Get Recommendation</button>
    </form>
    <div id="result"></div>
          <div style="text-align: center; margin-top: 20px;">
      <a href="http://127.0.0.1:4887/datascience_frontend/TravelTeach/" target="_blank">
        <button type="button" style="background-color:rgba(70, 94, 152, 0.8);">Learn how this feature works</button>
      </a>
    </div>
  </div>

  <script>
  import { pythonURI, fetchOptions } from "/assets/js/api/config.js";  
      // Tooltip toggle
  const helpBtn = document.getElementById('helpBtn');
  const tooltip = document.getElementById('tooltip');

  helpBtn.addEventListener('mouseenter', () => {
    tooltip.style.display = 'block';
  });

  helpBtn.addEventListener('mouseleave', () => {
    tooltip.style.display = 'none';
  });

  tooltip.addEventListener('mouseenter', () => {
    tooltip.style.display = 'block';
  });

  tooltip.addEventListener('mouseleave', () => {
    tooltip.style.display = 'none';
  });

    document.getElementById('travelForm').addEventListener('submit', async function(e) {
      e.preventDefault();

      const season = document.getElementById('season').value;
      const activity = document.getElementById('activity').value;
      const budget = document.getElementById('budget').value;
      const continent = document.getElementById('continent').value;

      const resultDiv = document.getElementById('result');
      resultDiv.textContent = "Loading recommendation...";

      try {
        const response = await fetch(pythonURI + '/api/destination/recommend', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
              season: season.toLowerCase(),
              activity: activity.toLowerCase(),
              budget: budget.toLowerCase(),
              continent: continent.toLowerCase()
           })
        });

        if (!response.ok) throw new Error("Request failed");

        const result = await response.json();

        if (result.destination && result.activitySuggestion) {
        resultDiv.innerHTML = `
          <h3>🌍 Recommended Destination:</h3>
          <p>${result.destination}</p>
          <h3>🎯 Suggested Activity:</h3>
          <p>${result.activitySuggestion}</p>
        `;
      } else {
        resultDiv.textContent = `❌ No recommendation found.`;
      }
    } catch (err) {
      resultDiv.textContent = "❌ Error connecting to the server.";
      console.error(err);
    }
  });
</script>



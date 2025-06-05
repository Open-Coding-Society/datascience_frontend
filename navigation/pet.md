---
layout: post
title: Pet Training
search_exclude: true
permalink: /train/
---

  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>ML Pet Trainer</title>
<style>
    body {
      background-color: #fff8f0;
      color: #333;
      margin: 2rem;
    }
    label, input {
      display: block;
      margin: 0.5rem 0;
    }
    button {
      margin-top: 1rem;
      padding: 0.5rem 1rem;
      background-color: red;
      border: 5px;
      border-radius: 8px;
      cursor: pointer;
      font-size: 1rem;
    }
    button:hover {
      background-color: #ffb347;
    }
    #result, #importance, #petSpeech {
      margin-top: 2rem;
    }
    #petImage {
      margin-top: 2rem;
      text-align: center;
      position: relative;
      width: 400px;
      margin-left: auto;
      margin-right: auto;
    }
    #petPic {
      width: 400px;
      display: block;
      margin: 0 auto;
      height: 250px;
      transition: transform 0.3s ease;
    }
    @keyframes bounce {
      0%, 100% { transform: translateY(0); }
      50% { transform: translateY(-10px); }
    }
    @keyframes sparkle {
        0%, 100% { opacity: 0; transform: scale(1) rotate(0deg); }
         50% { opacity: 1; transform: scale(1.2) rotate(20deg); }
    }
    .sparkle {
        position: absolute;
        width: 20px;
        height: 20px;
        background: radial-gradient(circle, #fff9c4 0%, transparent 70%);
        border-radius: 50%;
        animation: sparkle 1.5s infinite;
        pointer-events: none;
        filter: drop-shadow(0 0 5px #fff9c4);
    }
    #petPic.bounce {
      animation: bounce 0.6s ease;
    }
    #petSpeech {
      font-style: italic;
      font-size: 1.1rem;
      text-align: center;
      margin-top: 1rem; 
    }
    #happinessBar {
      width: 100%;
      margin-top: 1rem;
      height: 20px; 
      appearance: none;
      -webkit-appearance: none;
      border-radius: 10px;
      overflow: hidden;
      background: #eee;
      position: relative;
    }
    #happinessBar::-webkit-progress-value {
        transition: width 1s ease;
        background-color: #ffcc70;
        border-radius: 10px;
    }
    #happinessBar::-moz-progress-bar {
        transition: width 1s ease;
        background-color: #ffcc70;
        border-radius: 10px;
    }
    #petForm{
        background-color: #db7070;
        padding: 2rem;
        align-items: center;
        justify-content: center;
        border-radius: 12px;
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    }
    input[type="number"] {
        width: 100%;         /* Make it fill the contner width */
        padding: 0.5rem;      /* Add space inside the box */
        font-size: 1.1rem;    /* Make the text larger */
        border: 1px solid #ccc;
        border-radius: 6px;
        box-sizing: border-box; /* Prevent padding from affecting total width */
    }
</style>

<body data-baseurl="{{ site.baseurl }}">
  <h1>ML Pet Trainer 🐶</h1>
  <form id="petForm">
    <label for="petSelect">Choose your pet:</label>
      <select id="petSelect" name="petSelect">
        <option value="dog">Dog 🐶</option>
        <option value="cat">Cat 🐱</option>
     </select>
    <label for="food">Food (1–5):</label>
    <input type="number" id="food" name="food" min="1" max="5" required>
    <label for="play">Play (1–5):</label>
    <input type="number" id="play" name="play" min="1" max="5" required>
    <label for="sleep">Sleep (1–5):</label>
    <input type="number" id="sleep" name="sleep" min="1" max="5" required>
    <button type="submit">Predict Happiness</button>
  </form>


  <div id="result"></div>

  <progress id="happinessBar" value="0" max="10"></progress>

  <div id="petImage" style="position: relative; display: inline-block;">
    <img id="petPic" alt="AI Pet">
  </div>

  <div id="petSpeech"></div>


  <script>
    import { pythonURI, fetchOptions } from "/assets/js/api/config.js";

    const form = document.getElementById('petForm');
    const petSelect = document.getElementById('petSelect');
    const resultDiv = document.getElementById('result');
    const petPic = document.getElementById('petPic');
    const petSpeech = document.getElementById('petSpeech');
    const happinessBar = document.getElementById('happinessBar');
    const baseUrl = document.body.dataset.baseurl;  

// Update image when user selects a different pet
    petSelect.addEventListener('change', () => {
      const selectedPet = petSelect.value;
      petPic.src = `${baseUrl}/images/${selectedPet}-neutral.png`;
      petSpeech.textContent = "I'm ready for training!";
    });
    const defaultPet = petSelect.value;
    petPic.src = `${baseUrl}/images/${defaultPet}-neutral.png`;

form.addEventListener('submit', async (e) => {
  e.preventDefault();

  const food = parseInt(document.getElementById('food').value);
  const play = parseInt(document.getElementById('play').value);
  const sleep = parseInt(document.getElementById('sleep').value);

  const pet = petSelect.value;  // get selected pet


  try {
    const response = await fetch(pythonURI + '/api/pet/predict', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ food, play, sleep })
    });

    const data = await response.json();
    const happiness = data.predicted_happiness;

    resultDiv.innerHTML = `<h3>Predicted Happiness: ${happiness}</h3>`;
    happinessBar.value = happiness;

    // Update pet image and speech 
    let imageSrc = `${baseUrl}/images/${pet}-neutral.png`;
    let message = "I'm okay... maybe a snack?";

    if (happiness > 8) {
      imageSrc = `${baseUrl}/images/${pet}-happy.png`;
      message = "I'm feeling pawsome! 🐾";
      const sparkle = document.createElement('div');
      sparkle.className = 'sparkle';
      sparkle.style.top = `${Math.random() * 100}px`;
      sparkle.style.left = `${Math.random() * 300}px`;
      document.getElementById('petImage').appendChild(sparkle);
      setTimeout(() => sparkle.remove(), 1500);

    } else if (happiness < 5) {
      imageSrc = `${baseUrl}/images/${pet}-sad.png`;
      message = "I'm feeling ruff... 🥺";
    }

    petPic.src = imageSrc;
    petSpeech.textContent = message;

    // Trigger bounce animation
    petPic.classList.remove('bounce');
    void petPic.offsetWidth;
    petPic.classList.add('bounce');
    
  } catch (err) {
    console.error('Error predicting happiness:', err);
    resultDiv.innerHTML = `<p style="color: red;">Something went wrong. Please try again.</p>`;
  }
});
  </script>

</body>
<a href="https://open-coding-society.github.io/datascience_frontend/pets/" target="_blank">
  <button style="padding:10px 20px; font-size:16px; border:5x; background-color:#4CAF50; color:white; border-radius:6px; cursor:pointer;">
    More Info Here
  </button>
</a>

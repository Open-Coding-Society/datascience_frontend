---
layout: post
title: Quiz Flashcards (Diagnostic)
search_exclude: true
permalink: /quiz/
---

  <style>
    body {
      font-family: sans-serif;
      padding: 20px;
    }
    .card {
      max-width: 600px;
      margin: auto;
      padding: 20px;
      border: 2px solid #444;
      border-radius: 12px;
      background:rgb(190, 65, 65);
      text-align: center;
    }
    .btn {
      padding: 10px 20px;
      margin: 10px;
      border: none;
      border-radius: 5px;
      background: #4f46e5;
      color: white;
      cursor: pointer;
    }
    .btn:hover {
      background: #3730a3;
    }
    .category-status {
      margin: 15px auto;
      max-width: 600px;
      font-size: 16px;
    }
  </style>


<h1>📊 Data Science Quiz</h1>

<select id="category-select" onchange="selectCategory(this.value)">
  <option value="">-- Select a Category --</option>
  <option value="modeling">Modeling</option>
  <option value="statistics">Statistics</option>
</select>

<div class="category-status" id="mastery-status"></div>

<div class="card" id="quiz-card" style="display:none;">
  <h2 id="question">Loading...</h2>
  <div>
    <button class="btn" onclick="submitAnswer(true)">I knew it</button>
    <button class="btn" onclick="submitAnswer(false)">I didn’t know</button>
  </div>
  <button class="btn" onclick="showAnswer()">Show Answer</button>
  <div id="answer" style="margin-top:10px; font-weight:bold;"></div>
</div>

<script>
const questions = {
  modeling: [
    { q: "What is a predictive model?", a: "A model that forecasts outcomes based on input data." },
    { q: "What is overfitting?", a: "Model performs well on training but poorly on new data." },
    { q: "What is regularization?", a: "A technique to reduce model complexity and overfitting." }
  ],
  statistics: [
    { q: "Define mean.", a: "Sum of all values divided by count." },
    { q: "What is variance?", a: "A measure of how spread out the data is." },
    { q: "Define standard deviation.", a: "Square root of the variance." }
  ]
};

let currentCategory = null;
let score = 0;
let total = 0;
let current = null;
let quizQueue = [];

function loadProgress() {
  const stored = JSON.parse(localStorage.getItem("ds_mastery") || "{}");
  return stored;
}

function saveProgress(data) {
  localStorage.setItem("ds_mastery", JSON.stringify(data));
}

function updateMasteryDisplay() {
  const progress = loadProgress();
  const display = Object.keys(questions).map(cat => {
    const s = progress[cat]?.score || 0;
    const label = s >= 9 ? "✅ Mastered" : `Score: ${s}/10`;
    return `<div><strong>${cat.toUpperCase()}:</strong> ${label}</div>`;
  }).join("");
  document.getElementById("mastery-status").innerHTML = display;
}

function selectCategory(cat) {
  if (!questions[cat]) return;
  currentCategory = cat;
  score = loadProgress()[cat]?.score || 0;
  total = 0;
  quizQueue = questions[cat].map(q => ({ ...q, attempts: 0 }));
  document.getElementById("quiz-card").style.display = "block";
  pickNext();
}

function pickNext() {
  const available = quizQueue.filter(q => q.attempts < 3);
  if (available.length === 0 || total >= 10) {
    endCategory();
    return;
  }
  current = available[Math.floor(Math.random() * available.length)];
  document.getElementById("question").textContent = current.q;
  document.getElementById("answer").textContent = "";
}

function showAnswer() {
  document.getElementById("answer").textContent = current.a;
}

function submitAnswer(knewIt) {
  current.attempts++;
  if (knewIt) score++;
  total++;
  updateProgress();
  pickNext();
}

function updateProgress() {
  const progress = loadProgress();
  progress[currentCategory] = { score: score };
  saveProgress(progress);
  updateMasteryDisplay();
}

function endCategory() {
  document.getElementById("question").textContent = `Done! Final Score: ${score}/10`;
  document.getElementById("answer").textContent = score >= 9 ? "✅ Mastered!" : "Keep practicing!";
  document.querySelector(".btn").style.display = "none";
}
updateMasteryDisplay();
</script>


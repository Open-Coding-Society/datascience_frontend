---
layout: post
title: Quiz Flashcards (Diagnostic)
search_exclude: true
permalink: /quiz/
---


<!-- Flashcards & Adaptive Quiz for Data Science -->
<style>
  .card { 
    width: 90%; 
    max-width: 500px; 
    margin: 20px auto; 
    padding: 20px; 
    border: 2px solid #444; 
    border-radius: 12px; 
    text-align: center; 
    background:rgb(199, 95, 95);
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    font-family: sans-serif;
  }
  .card h2 { margin-bottom: 10px; min-height: 60px; }
  .btn {
    padding: 10px 20px;
    margin: 8px;
    border: none;
    border-radius: 6px;
    background: #4f46e5;
    color: white;
    cursor: pointer;
  }
  .btn:hover { background: #3730a3; }
</style>

<div class="card" id="quiz-card" style="display: none;">
  <h2 id="question">Loading...</h2>
  <div id="options"></div>
  <button class="btn" onclick="flipCard()">Flip</button>
  <button class="btn" onclick="nextCard()">Next</button>
  <div id="feedback" style="margin-top:10px; font-weight: bold;"></div>
</div>
<noscript>Please enable JavaScript to view flashcards.</noscript>

<script>
document.addEventListener("DOMContentLoaded", () => {
  const flashcards = [
    { q: "What is the difference between supervised and unsupervised learning?", a: "Supervised uses labeled data, unsupervised does not." },
    { q: "What is overfitting?", a: "When a model learns the training data too well and performs poorly on new data." },
    { q: "Name a commonly used dimensionality reduction technique.", a: "Principal Component Analysis (PCA)." },
    { q: "What metric is used to evaluate classification models?", a: "Accuracy, Precision, Recall, F1-score, etc." },
    { q: "What is a confusion matrix?", a: "A table showing TP, FP, TN, FN used to evaluate classification models." },
    { q: "What is cross-validation?", a: "A technique for assessing how a model generalizes to unseen data." },
  ];

  let current = 0;
  let flipped = false;

  const card = document.getElementById("quiz-card");
  const questionEl = document.getElementById("question");
  const feedbackEl = document.getElementById("feedback");

  function showCard() {
    const item = flashcards[current];
    questionEl.textContent = flipped ? item.a : item.q;
    feedbackEl.textContent = "";
  }

  window.flipCard = () => {
    flipped = !flipped;
    showCard();
  };

  window.nextCard = () => {
    flipped = false;
    current = (current + 1) % flashcards.length;
    showCard();
  };

  // Initialize
  card.style.display = "block";
  showCard();
});
</script>

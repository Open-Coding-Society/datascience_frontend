---
layout: post
title: Sentiment Analysis
search_exclude: true
permalink: /sentiment/
---
# Interactive Sentiment Analysis Tool

Type your own text or click **Generate Random Review** to try an example. Then click **Analyze Sentiment** to see the result based on a custom-trained model.

<textarea id="input-text" placeholder="Write your review here..." rows="5" cols="60"></textarea>

<br><br>

<button id="generate-btn">Generate Random Review</button>
<button id="analyze-btn">Analyze Sentiment</button>

<p id="result" style="font-size: 1.2em; font-weight: bold; margin-top: 20px;"></p>

<script>
const trainingData = [
  { text: "I absolutely love this!", label: 1 },
  { text: "This product exceeded my expectations.", label: 1 },
  { text: "Amazing quality and fantastic customer service.", label: 1 },
  { text: "I'm very happy with my purchase.", label: 1 },
  { text: "Highly recommend this to everyone.", label: 1 },
  { text: "Best experience I have ever had.", label: 1 },
  { text: "Works perfectly and looks great.", label: 1 },
  { text: "I’m impressed with how well it works.", label: 1 },
  { text: "Five stars for this awesome product.", label: 1 },
  { text: "This is a must-have item!", label: 1 },
  { text: "The quality is top-notch.", label: 1 },
  { text: "I will definitely buy this again.", label: 1 },
  { text: "Exceeded all my expectations.", label: 1 },
  { text: "Very satisfied with the purchase.", label: 1 },
  { text: "Love it! Worth every penny.", label: 1 },
  { text: "Customer support was very helpful.", label: 1 },
  { text: "Fast shipping and great packaging.", label: 1 },
  { text: "Easy to use and very effective.", label: 1 },
  { text: "I’m so glad I found this.", label: 1 },
  { text: "Perfect for what I needed.", label: 1 },
  { text: "Outstanding performance so far.", label: 1 },
  { text: "Absolutely fantastic product.", label: 1 },
  { text: "I love the design and functionality.", label: 1 },
  { text: "It works exactly as described.", label: 1 },
  { text: "Best value for the price.", label: 1 },
  { text: "I feel this was a great investment.", label: 1 },
  { text: "Highly effective and reliable.", label: 1 },
  { text: "This product makes my life easier.", label: 1 },
  { text: "The results were better than expected.", label: 1 },
  { text: "I'm really happy with this purchase.", label: 1 },
  { text: "The best choice I made this year.", label: 1 },
  { text: "Wonderful experience overall.", label: 1 },
  { text: "Simply the best!", label: 1 },

  // 33 Neutral samples
  { text: "It's okay, nothing special.", label: 0.5 },
  { text: "Average quality, works as expected.", label: 0.5 },
  { text: "Neither good nor bad.", label: 0.5 },
  { text: "It's fine for casual use.", label: 0.5 },
  { text: "Meets my basic needs.", label: 0.5 },
  { text: "Not bad, but not great either.", label: 0.5 },
  { text: "Service was average.", label: 0.5 },
  { text: "Product is acceptable.", label: 0.5 },
  { text: "Decent, but could be improved.", label: 0.5 },
  { text: "Nothing remarkable to report.", label: 0.5 },
  { text: "The experience was average.", label: 0.5 },
  { text: "It's okay for the price.", label: 0.5 },
  { text: "Neutral feelings about this.", label: 0.5 },
  { text: "The product works as advertised.", label: 0.5 },
  { text: "It does the job.", label: 0.5 },
  { text: "Not too impressed, not disappointed.", label: 0.5 },
  { text: "An average experience overall.", label: 0.5 },
  { text: "Neither good nor bad service.", label: 0.5 },
  { text: "I have no strong feelings either way.", label: 0.5 },
  { text: "Could be better, could be worse.", label: 0.5 },
  { text: "Fair quality for the price.", label: 0.5 },
  { text: "It’s okay, but there are better options.", label: 0.5 },
  { text: "Product is decent, nothing exceptional.", label: 0.5 },
  { text: "Average, meets expectations.", label: 0.5 },
  { text: "Nothing special, but not bad.", label: 0.5 },
  { text: "Functional, but lacks excitement.", label: 0.5 },
  { text: "Neutral reaction to this product.", label: 0.5 },
  { text: "It’s alright, nothing to rave about.", label: 0.5 },
  { text: "Satisfactory performance.", label: 0.5 },
  { text: "It’s just fine.", label: 0.5 },
  { text: "Neither happy nor unhappy with this.", label: 0.5 },
  { text: "Product works okay.", label: 0.5 },

  // 33 Negative samples
  { text: "I hate this product.", label: 0 },
  { text: "Terrible quality and poor service.", label: 0 },
  { text: "Very disappointed with my purchase.", label: 0 },
  { text: "Waste of money.", label: 0 },
  { text: "The product broke after one use.", label: 0 },
  { text: "Not worth the price.", label: 0 },
  { text: "Horrible experience overall.", label: 0 },
  { text: "It did not meet my expectations.", label: 0 },
  { text: "Worst purchase I have ever made.", label: 0 },
  { text: "The item arrived damaged.", label: 0 },
  { text: "Extremely dissatisfied.", label: 0 },
  { text: "Very poor customer support.", label: 0 },
  { text: "This product is a scam.", label: 0 },
  { text: "I regret buying this.", label: 0 },
  { text: "The quality is awful.", label: 0 },
  { text: "Not as described.", label: 0 },
  { text: "Product stopped working quickly.", label: 0 },
  { text: "It was a complete waste.", label: 0 },
  { text: "Unacceptable performance.", label: 0 },
  { text: "Do not buy this.", label: 0 },
  { text: "Very bad experience.", label: 0 },
  { text: "The product is useless.", label: 0 },
  { text: "I’m very unhappy with this.", label: 0 },
  { text: "Terrible, won’t recommend.", label: 0 },
  { text: "It broke immediately.", label: 0 },
  { text: "Not recommended at all.", label: 0 },
  { text: "Poorly made and unreliable.", label: 0 },
  { text: "This is disappointing.", label: 0 },
  { text: "Worst customer service ever.", label: 0 },
  { text: "Doesn’t work as expected.", label: 0 },
  { text: "Completely dissatisfied.", label: 0 },
  { text: "Very frustrating experience.", label: 0 }
];

// Simple tokenizer: lowercases, splits on spaces, removes punctuation
function tokenize(text) {
  return text
    .toLowerCase()
    .replace(/[^\w\s]|_/g, "")
    .split(/\s+/)
    .filter(Boolean);
}

// Build a vocabulary from training data
const vocab = {};
let vocabIndex = 0;
trainingData.forEach(({ text }) => {
  const tokens = tokenize(text);
  tokens.forEach(token => {
    if (!(token in vocab)) {
      vocab[token] = vocabIndex++;
    }
  });
});

// Converts text into a vector based on vocabulary counts (Bag of Words)
function textToVector(text) {
  const vec = new Array(vocabIndex).fill(0);
  const tokens = tokenize(text);
  tokens.forEach(token => {
    if (token in vocab) {
      vec[vocab[token]] += 1;
    }
  });
  return vec;
}

// Logistic Regression Model
class LogisticRegression {
  constructor(featureCount, learningRate = 0.1) {
    this.weights = new Array(featureCount).fill(0);
    this.bias = 0;
    this.learningRate = learningRate;
  }

  sigmoid(z) {
    return 1 / (1 + Math.exp(-z));
  }

  predict(features) {
    let z = this.bias;
    for (let i = 0; i < features.length; i++) {
      z += this.weights[i] * features[i];
    }
    return this.sigmoid(z);
  }

  train(features, label) {
    const pred = this.predict(features);
    const error = label - pred;
    for (let i = 0; i < this.weights.length; i++) {
      this.weights[i] += this.learningRate * error * features[i];
    }
    this.bias += this.learningRate * error;
  }
}

// Prepare training vectors and labels
const trainX = trainingData.map(d => textToVector(d.text));
const trainY = trainingData.map(d => d.label);

// Initialize and train model
const model = new LogisticRegression(vocabIndex, 0.1);

// Train for multiple epochs
const epochs = 30;
for (let epoch = 0; epoch < epochs; epoch++) {
  for (let i = 0; i < trainX.length; i++) {
    model.train(trainX[i], trainY[i]);
  }
}

// Random samples for Generate button (feel free to adjust or expand)
const randomSamples = trainingData.map(d => d.text);

document.getElementById('generate-btn').onclick = () => {
  const idx = Math.floor(Math.random() * randomSamples.length);
  document.getElementById('input-text').value = randomSamples[idx];
  document.getElementById('result').textContent = '';
};

document.getElementById('analyze-btn').onclick = () => {
  const inputText = document.getElementById('input-text').value.trim();
  const resultP = document.getElementById('result');

  if (!inputText) {
    resultP.style.color = 'red';
    resultP.textContent = 'Please enter or generate a review first.';
    return;
  }

  const inputVec = textToVector(inputText);
  const score = model.predict(inputVec);

  // Interpret score thresholds for labels (you can tune these)
  let label, color;
  if (score >= 0.7) {
    label = 'Positive';
    color = 'green';
  } else if (score > 0.4) {
    label = 'Neutral';
    color = 'orange';
  } else {
    label = 'Negative';
    color = 'red';
  }

  resultP.style.color = color;
  resultP.innerHTML = `Sentiment: <strong>${label}</strong> (Score: ${score.toFixed(3)})`;
};
</script>
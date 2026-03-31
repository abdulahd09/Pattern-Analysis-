# Pattern-Analysis-
<!DOCTYPE html>
<html>
<head>
  <title>AI Pattern Analyzer</title>
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@4/dist/tesseract.min.js"></script>

  <style>
    body {
      font-family: Arial;
      text-align: center;
      padding: 20px;
      background: #111;
      color: white;
    }

    input, button {
      padding: 10px;
      margin: 5px;
      border-radius: 8px;
      border: none;
    }

    button {
      background: #00c853;
      color: white;
      cursor: pointer;
    }

    #output {
      margin-top: 20px;
      font-size: 18px;
    }
  </style>
</head>
<body>

<h2>🔥 AI Pattern Betting Assistant</h2>

<!-- OCR Upload -->
<input type="file" id="imageInput">
<button onclick="readImage()">📸 Read Screenshot</button>

<br><br>

<!-- Manual Input -->
<input type="text" id="numbers" placeholder="Last 10 numbers (auto-filled)">
<br>

<input type="number" id="money" placeholder="Enter Balance">
<br>

<button onclick="start()">🚀 Analyze</button>

<div id="output"></div>

<script>
let level = 1;
let baseBet = 10;
let currentBet = baseBet;

function getColor(num) {
  if (num === 0) return ["Purple","Red"];
  if (num === 5) return ["Purple","Green"];
  if (num % 2 === 0) return ["Red"];
  return ["Green"];
}

function getSize(num) {
  return num >= 5 ? "Big" : "Small";
}

// OCR FUNCTION
function readImage() {
  const file = document.getElementById('imageInput').files[0];

  if (!file) {
    alert("Upload image first");
    return;
  }

  Tesseract.recognize(file, 'eng')
  .then(({ data: { text } }) => {

    let numbers = text.match(/\d/g);

    if (!numbers || numbers.length < 10) {
      alert("❌ Not enough numbers detected");
      return;
    }

    numbers = numbers.slice(-10);

    document.getElementById("numbers").value = numbers.join(",");
    alert("✅ Numbers detected: " + numbers.join(","));
  });
}

// ANALYSIS
function start() {
  let nums = document.getElementById("numbers").value
    .split(",").map(n => parseInt(n.trim()));

  if (nums.length !== 10 || nums.some(isNaN)) {
    alert("Enter exactly 10 numbers");
    return;
  }

  analyze(nums);
}

function analyze(nums) {
  let big=0, small=0, red=0, green=0, purple=0;

  nums.forEach(n => {
    getSize(n) === "Big" ? big++ : small++;

    getColor(n).forEach(c => {
      if(c==="Red") red++;
      if(c==="Green") green++;
      if(c==="Purple") purple++;
    });
  });

  let decision = "";
  let confidence = 0;

  if (Math.abs(big-small) >= 3) {
    decision = big>small ? "Big" : "Small";
    confidence = 75;
  }

  let maxColor = Math.max(red,green,purple);
  if (maxColor >= 6) {
    decision = red===maxColor?"Red":green===maxColor?"Green":"Purple";
    confidence = 80;
  }

  if (!decision) {
    decision = "SKIP ❌";
    confidence = 50;
  }

  document.getElementById("output").innerHTML = `
    📊 Big:${big} | Small:${small}<br>
    🎨 Red:${red} | Green:${green} | Purple:${purple}<br><br>

    👉 Bet: <b>${decision}</b><br>
    🔥 Confidence: ${confidence}%<br><br>

    💰 Level: ${level}<br>
    💸 Bet Amount: ${currentBet}<br><br>

    <button onclick="win()">✅ Win</button>
    <button onclick="loss()">❌ Loss</button>
  `;
}

// LEVEL SYSTEM
function win() {
  level = 1;
  currentBet = baseBet;
  alert("✅ Back to Level 1");
}

function loss() {
  level++;
  currentBet *= 2;
  alert("❌ Move to Level " + level);
}
</script>

</body>
</html>

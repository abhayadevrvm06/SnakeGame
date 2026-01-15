<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Snake — Balanced Speed</title>
  <style>
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: #0b1020;
      font-family: Arial, sans-serif;
      color: white;
    }
    canvas {
      background: #111a2b;
      border: 2px solid #7ef36a;
      box-shadow: 0 0 20px rgba(126, 243, 106, 0.2);
    }
    #overlay {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: rgba(0,0,0,0.9);
      padding: 30px 50px;
      border-radius: 12px;
      text-align: center;
      border: 1px solid #333;
    }
    #overlay h1 {
      margin: 0 0 20px 0;
      font-size: 32px;
      color: #7ef36a;
    }
    #overlay select, #overlay button {
      margin-top: 15px;
      padding: 12px 24px;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      cursor: pointer;
      width: 100%;
      box-sizing: border-box;
    }
    #overlay select {
      background: #222;
      color: white;
      border: 1px solid #444;
    }
    #overlay button {
      background: #7ef36a;
      color: #0b1020;
      font-weight: bold;
      transition: background 0.2s;
    }
    #overlay button:hover {
      background: #6ce05a;
    }
  </style>
</head>
<body>
  
  <canvas id="gameCanvas" width="400" height="400"></canvas>

  <div id="overlay">
    <h1>Snake Game</h1>
    <label for="speed">Starting Difficulty:</label><br>
    <select id="speed">
      <option value="250">Easy (Warmup)</option>
      <option value="180" selected>Normal</option>
      <option value="130">Hard</option>
      <option value="90">Extreme</option>
    </select><br>
    <button onclick="startGame()">Play</button>
  </div>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    const overlay = document.getElementById("overlay");
    const speedSelect = document.getElementById("speed");

    const gridSize = 20;
    let snake, apple, score, gameInterval, running;
    let dx, dy, currentSpeed;
    let moveQueue = []; 

    function initGame() {
      snake = [{ x: 200, y: 200 }, { x: 180, y: 200 }, { x: 160, y: 200 }];
      dx = gridSize;
      dy = 0;
      score = 0;
      moveQueue = []; 
      apple = randomApple();
      running = true;
    }

    function startGame() {
      currentSpeed = parseInt(speedSelect.value);
      overlay.style.display = "none";
      initGame();
      
      clearInterval(gameInterval);
      gameInterval = setInterval(gameLoop, currentSpeed);
    }

    function endGame() {
      running = false;
      clearInterval(gameInterval);
      overlay.style.display = "block";
      overlay.querySelector("h1").innerText = "Game Over!";
      overlay.querySelector("label").innerText = "Final Score: " + score;
    }

    function randomApple() {
      return {
        x: Math.floor(Math.random() * (canvas.width / gridSize)) * gridSize,
        y: Math.floor(Math.random() * (canvas.height / gridSize)) * gridSize
      };
    }

    // --- NEW LOGIC HERE ---
    function increaseSpeed() {
        const maxSpeedCap = 50; // The Limit (Lower number = Faster game)
        let speedDecay = 0;

        // Stage 1: If

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Neon Snake</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;800&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg-color: #0f172a;
      --panel-color: #1e293b;
      --accent-color: #7ef36a;
      --accent-glow: rgba(126, 243, 106, 0.5);
      --danger-color: #ff4757;
      --text-color: #f8fafc;
    }

    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      background-color: var(--bg-color);
      background-image: 
        linear-gradient(rgba(255, 255, 255, 0.03) 1px, transparent 1px),
        linear-gradient(90deg, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
      background-size: 40px 40px;
      font-family: 'Poppins', sans-serif;
      color: var(--text-color);
    }

    .game-container {
      position: relative;
      background: var(--panel-color);
      padding: 20px;
      border-radius: 20px;
      box-shadow: 
        0 20px 50px rgba(0, 0, 0, 0.5),
        0 0 0 1px rgba(255, 255, 255, 0.1);
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 15px;
    }

    .hud {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0 10px;
      box-sizing: border-box;
      font-size: 14px;
      color: #94a3b8;
      font-weight: 600;
    }

    .score-value {
      font-size: 24px;
      color: var(--text-color);
      font-family: 'Courier New', monospace;
    }

    canvas {
      background: #0b1120;
      border-radius: 8px;
      box-shadow: inset 0 0 20px rgba(0,0,0,0.5);
      border: 2px solid #334155;
      cursor: none;
    }

    /* Overlay Styling */
    #overlay {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(15, 23, 42, 0.6);
      backdrop-filter: blur(8px);
      border-radius: 20px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      z-index: 10;
      transition: opacity 0.3s ease;
    }

    .menu-content {
      background: rgba(30, 41, 59, 0.9);
      padding: 30px;
      border-radius: 16px;
      border: 1px solid rgba(255,255,255,0.1);
      text-align: center;
      box-shadow: 0 10px 30px rgba(0,0,0,0.3);
      width: 250px;
    }

    h1 {
      font-size: 42px;
      margin: 0 0 10px 0;
      text-transform: uppercase;
      letter-spacing: 2px;
      background: linear-gradient(45deg, var(--accent-color), #4ade80);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      text-shadow: 0 4px 20px var(--accent-glow);
    }
    
    /* Specific style for Game Over text */
    h1.game-over-title {
        background: linear-gradient(45deg, #ff4757, #ff6b81);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        text-shadow: 0 4px 20px rgba(255, 71, 87, 0.5);
        font-size: 36px;
    }

    label {
      font-size: 12px;
      text-transform: uppercase;
      letter-spacing: 1px;
      color: #94a3b8;
      display: block;
      margin-bottom: 8px;
    }

    select {
      width: 100%;
      padding: 12px;
      background: #0f172a;
      border: 1px solid #334155;
      border-radius: 8px;
      color: white;
      font-family: inherit;
      cursor: pointer;
      margin-bottom: 20px;
      outline: none;
      transition: border-color 0.2s;
    }
    
    select:hover, select:focus {
      border-color: var(--accent-color);
    }

    /* Primary Button */
    .btn-primary {
      width: 100%;
      padding: 14px;
      background: var(--accent-color);
      color: #0b1020;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      font-weight: 800;
      text-transform: uppercase;
      cursor: pointer;
      transition: all 0.2s;
      box-shadow: 0 4px 15px var(--accent-glow);
      margin-bottom: 10px;
    }

    .btn-primary:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 20px var(--accent-glow);
      filter: brightness(1.1);
    }

    /* Secondary Button (Outline) */
    .btn-secondary {
      width: 100%;
      padding: 12px;
      background: transparent;
      color: #94a3b8;
      border: 2px solid #334155;
      border-radius: 8px;
      font-size: 14px;
      font-weight: 600;
      text-transform: uppercase;
      cursor: pointer;
      transition: all 0.2s;
    }

    .btn-secondary:hover {
      border-color: var(--text-color);
      color: var(--text-color);
      background: rgba(255,255,255,0.05);
    }

    #finalScoreDisplay {
      font-size: 18px;
      margin-bottom: 20px;
      color: var(--danger-color);
      font-weight: bold;
    }

    .hidden {
      display: none !important;
    }
  </style>
</head>
<body>

  <div class="game-container">
    <div class="hud">
      <span>SPEED: <span id="speedDisplay" style="color: var(--accent-color)">NORMAL</span></span>
      <span>SCORE: <span id="scoreDisplay" class="score-value">000</span></span>
    </div>

    <canvas id="gameCanvas" width="400" height="400"></canvas>

    <div id="overlay">
      <div class="menu-content">
        
        <div id="startScreen">
          <h1>NEON SNAKE</h1>
          <label for="speed">Select Difficulty</label>
          <select id="speed">
            <option value="250">Easy</option>
            <option value="180" selected>Normal</option>
            <option value="130">Hard</option>
            <option value="90">Extreme</option>
          </select>
          <button class="btn-primary" onclick="startGame()">Start Game</button>
        </div>

        <div id="gameOverScreen" class="hidden">
          <h1 class="game-over-title">GAME OVER</h1>
          <div id="finalScoreDisplay"></div>
          
          <button class="btn-primary" onclick="startGame()">Try Again</button>
          <button class="btn-secondary" onclick="showMainMenu()">Main Menu</button>
        </div>

      </div>
    </div>
  </div>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    
    // UI Elements
    const overlay = document.getElementById("overlay");
    const startScreen = document.getElementById("startScreen");
    const gameOverScreen = document.getElementById("gameOverScreen");
    
    const speedSelect = document.getElementById("speed");
    const scoreEl = document.getElementById("scoreDisplay");
    const speedEl = document.getElementById("speedDisplay");
    const finalScoreEl = document.getElementById("finalScoreDisplay");

    const gridSize = 20;
    const tileCount = canvas.width / gridSize;
    
    let snake, apple, score, gameInterval, running;
    let dx, dy, currentSpeed;
    let moveQueue = []; 

    function initGame() {
      snake = [{ x: 200, y: 200 }, { x: 180, y: 200 }, { x: 160, y: 200 }];
      dx = gridSize;
      dy = 0;
      score = 0;
      updateScoreUI();
      moveQueue = []; 
      apple = randomApple();
      running = true;
    }

    function showMainMenu() {
        overlay.classList.remove("hidden");
        startScreen.classList.remove("hidden");
        gameOverScreen.classList.add("hidden");
    }

    function startGame() {
      // Get speed from the selector
      currentSpeed = parseInt(speedSelect.value);
      
      // Hide UI
      overlay.classList.add("hidden");
      
      initGame();
      
      clearInterval(gameInterval);
      gameInterval = setInterval(gameLoop, currentSpeed);
    }

    function endGame() {
      running = false;
      clearInterval(gameInterval);
      
      // Show Overlay
      overlay.classList.remove("hidden");
      
      // Toggle Screens inside Overlay
      startScreen.classList.add("hidden");
      gameOverScreen.classList.remove("hidden");
      
      finalScoreEl.innerText = "FINAL SCORE: " + score;
    }

    function randomApple() {
      return {
        x: Math.floor(Math.random() * tileCount) * gridSize,
        y: Math.floor(Math.random() * tileCount) * gridSize
      };
    }

    function updateScoreUI() {
        scoreEl.innerText = score.toString().padStart(3, '0');
        
        let speedText = "MAX";
        if(currentSpeed > 150) speedText = "SLOW";
        else if(currentSpeed > 100) speedText = "MED";
        else if(currentSpeed > 60) speedText = "FAST";
        
        speedEl.innerText = speedText;
    }

    function increaseSpeed() {
        const maxSpeedCap = 50; 
        let speedDecay = 0;

        if (currentSpeed > 150) speedDecay = 5;
        else if (currentSpeed > 100) speedDecay = 2;
        else speedDecay = 1;

        if (currentSpeed - speedDecay >= maxSpeedCap) {
            currentSpeed -= speedDecay;
            clearInterval(gameInterval);
            gameInterval = setInterval(gameLoop, currentSpeed);
        }
    }

    function drawGrid() {
        ctx.strokeStyle = "#1e293b"; 
        ctx.lineWidth = 1;

        for (let x = 0; x <= canvas.width; x += gridSize) {
            ctx.beginPath();
            ctx.moveTo(x, 0);
            ctx.lineTo(x, canvas.height);
            ctx.stroke();
        }

        for (let y = 0; y <= canvas.height; y += gridSize) {
            ctx.beginPath();
            ctx.moveTo(0, y);
            ctx.lineTo(canvas.width, y);
            ctx.stroke();
        }
    }

    function gameLoop() {
      if (!running) return;

      if (moveQueue.length > 0) {
          const nextMove = moveQueue.shift();
          dx = nextMove.x;
          dy = nextMove.y;
      }

      const head = { x: snake[0].x + dx, y: snake[0].y + dy };

      // Wraparound
      if (head.x < 0) head.x = canvas.width - gridSize;
      if (head.x >= canvas.width) head.x = 0;
      if (head.y < 0) head.y = canvas.height - gridSize;
      if (head.y >= canvas.height) head.y = 0;

      // Self Collision
      for (let part of snake) {
        if (part.x === head.x && part.y === head.y) {
          endGame();
          return;
        }
      }

      snake.unshift(head);

      // Eat Apple
      if (head.x === apple.x && head.y === apple.y) {
        score++;
        apple = randomApple();
        increaseSpeed();
        updateScoreUI();
      } else {
        snake.pop();
      }

      // Draw
      ctx.fillStyle = "#0b1120";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      drawGrid();

      // Apple
      ctx.fillStyle = "#ff4757"; 
      ctx.shadowBlur = 15;
      ctx.shadowColor = "#ff4757";
      ctx.fillRect(apple.x + 2, apple.y + 2, gridSize - 4, gridSize - 4);
      ctx.shadowBlur = 0; 

      // Snake
      for (let i = 0; i < snake.length; i++) {
        if (i === 0) {
            ctx.fillStyle = "#a3ff90";
            ctx.shadowBlur = 10;
            ctx.shadowColor = "#7ef36a";
        } else {
            ctx.fillStyle = "#7ef36a";
            ctx.shadowBlur = 0;
        }
        ctx.fillRect(snake[i].x + 1, snake[i].y + 1, gridSize - 2, gridSize - 2);
      }
      ctx.shadowBlur = 0; 
    }

    document.addEventListener("keydown", e => {
      let lastDx = dx;
      let lastDy = dy;
      
      if (moveQueue.length > 0) {
          const lastMove = moveQueue[moveQueue.length - 1];
          lastDx = lastMove.x;
          lastDy = lastMove.y;
      }

      let newDx = lastDx;
      let newDy = lastDy;

      if (e.key === "ArrowUp" && lastDy === 0) {
          newDx = 0; newDy = -gridSize;
      } else if (e.key === "ArrowDown" && lastDy === 0) {
          newDx = 0; newDy = gridSize;
      } else if (e.key === "ArrowLeft" && lastDx === 0) {
          newDx = -gridSize; newDy = 0;
      } else if (e.key === "ArrowRight" && lastDx === 0) {
          newDx = gridSize; newDy = 0;
      }

      if (newDx !== lastDx || newDy !== lastDy) {
          moveQueue.push({ x: newDx, y: newDy });
      }
    });
    
    // Initial draw to make the background look nice before starting
    drawGrid();
  </script>
</body>
</html>

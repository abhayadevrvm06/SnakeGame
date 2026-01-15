<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Snake: Retro Collection</title>
  <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
  <style>
    /* --- THEME DEFINITIONS --- */
    :root {
      /* Default to 'nokia' variables initially */
      --bg-color: #c7f0d8;
      --panel-color: #accfa6;
      --border-color: #43523d;
      --accent-color: #43523d;
      --snake-color: #43523d;
      --apple-color: #43523d;
      --text-color: #43523d;
      --glow: 0;
      --font-family: 'Press Start 2P', cursive;
    }
    /* THEME: NOKIA 3310 */
    [data-theme="nokia"] {
      --bg-color: #9fac95; /* The outer casing color */
      --panel-color: #c7f0d8; /* The screen backlight */
      --border-color: #43523d;
      --accent-color: #43523d;
      --snake-color: #43523d;
      --apple-color: #43523d;
      --text-color: #43523d;
      --glow: 0;
    }
    /* THEME: GAME BOY */
    [data-theme="gameboy"] {
      --bg-color: #8b956d;
      --panel-color: #9bbc0f; /* Lightest green */
      --border-color: #0f380f; /* Darkest green */
      --accent-color: #306230;
      --snake-color: #0f380f;
      --apple-color: #0f380f;
      --text-color: #0f380f;
      --glow: 0;
    }
    /* THEME: NEON (Cyberpunk) */
    [data-theme="neon"] {
      --bg-color: #050505;
      --panel-color: #111;
      --border-color: #333;
      --accent-color: #0ff;
      --snake-color: #0f0;
      --apple-color: #f0f;
      --text-color: #fff;
      --glow: 15px;
    }
    /* THEME: CRT ARCADE */
    [data-theme="arcade"] {
      --bg-color: #1a1a2e;
      --panel-color: #16213e;
      --border-color: #e94560;
      --accent-color: #fcd307;
      --snake-color: #4cc9f0;
      --apple-color: #f72585;
      --text-color: #fff;
      --glow: 5px;
    }
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      background-color: var(--bg-color);
      font-family: var(--font-family);
      color: var(--text-color);
      overflow: hidden;
      transition: background-color 0.5s;
    }
    .game-shell {
      position: relative;
      background: var(--bg-color);
      padding: 40px 20px 60px 20px;
      border-radius: 20px;
      border: 4px solid var(--border-color);
      box-shadow: 10px 10px 0px rgba(0,0,0,0.2);
    }
    .game-container {
      position: relative;
      background: var(--panel-color);
      padding: 5px;
      border: 4px solid var(--border-color);
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    /* Scanline Effect Overlay */
    .scanlines {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: linear-gradient(
        to bottom,
        rgba(255,255,255,0),
        rgba(255,255,255,0) 50%,
        rgba(0,0,0,0.1) 50%,
        rgba(0,0,0,0.1)
      );
      background-size: 100% 4px;
      pointer-events: none;
      z-index: 20;
      opacity: 0.6;
    }
    .hud {
      width: 100%;
      display: flex;
      justify-content: space-between;
      margin-bottom: 10px;
      font-size: 10px;
      line-height: 1.5;
    }
    canvas {
      background: var(--panel-color);
      image-rendering: pixelated; /* Keeps pixels sharp */
    }
    #overlay {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.7);
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      z-index: 30;
    }
    .menu-content {
      text-align: center;
      color: #fff; /* Always white text on overlay for readability */
      width: 300px;
    }
    h1 {
      font-size: 30px;
      margin-bottom: 20px;
      text-transform: uppercase;
      text-shadow: 4px 4px 0px #000;
      color: #fff;
    }
    .control-group {
      margin-bottom: 20px;
      text-align: left;
    }
    label {
      font-size: 10px;
      display: block;
      margin-bottom: 5px;
      color: #ccc;
    }
    select {
      width: 100%;
      padding: 10px;
      font-family: 'Press Start 2P';
      font-size: 10px;
      margin-bottom: 15px;
      cursor: pointer;
    }
    button {
      width: 100%;
      padding: 15px;
      font-family: 'Press Start 2P';
      font-size: 12px;
      background: #fff;
      color: #000;
      border: 4px solid #000;
      cursor: pointer;
      text-transform: uppercase;
      margin-bottom: 10px;
      box-shadow: 4px 4px 0px #555;
    }
    button:active {
      transform: translate(2px, 2px);
      box-shadow: 2px 2px 0px #555;
    }
    .hidden { display: none !important; }
  </style>
</head>
<body data-theme="nokia">

  <div class="game-shell">
    <div class="game-container">
      <div class="scanlines"></div>  
      <div class="hud">
        <div>SCORE: <span id="scoreDisplay">0</span></div>
        <div>BEST: <span id="highScoreDisplay">0</span></div>
      </div>
      <canvas id="gameCanvas" width="400" height="400"></canvas>
      <div id="overlay">
        <div class="menu-content">          
          <div id="startScreen">
            <h1>SNAKE</h1>            
            <div class="control-group">
              <label>Select Theme:</label>
              <select id="themeSelect" onchange="changeTheme()">
                <option value="nokia">Nokia 3310</option>
                <option value="gameboy">Game Boy Classic</option>
                <option value="arcade">Arcade CRT</option>
                <option value="neon">Cyber Neon</option>
              </select>
              <label>Select Speed:</label>
              <select id="speedSelect">
                <option value="200">Normal</option>
                <option value="120">Fast</option>
                <option value="80">Insane</option>
              </select>
            </div>
            <button onclick="startGame()">INSERT COIN</button>
          </div>
          <div id="gameOverScreen" class="hidden">
            <h1>GAME OVER</h1>
            <p style="margin-bottom: 20px">SCORE: <span id="finalScore"></span></p>
            <button onclick="startGame()">RETRY</button>
            <button onclick="showMainMenu()">MENU</button>
          </div>
          <div id="pauseScreen" class="hidden">
            <h1>PAUSED</h1>
            <button onclick="togglePause()">RESUME</button>
            <button onclick="quitToMenu()">QUIT</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    
    // Elements
    const body = document.body;
    const overlay = document.getElementById("overlay");
    const startScreen = document.getElementById("startScreen");
    const gameOverScreen = document.getElementById("gameOverScreen");
    const pauseScreen = document.getElementById("pauseScreen");
    const themeSelect = document.getElementById("themeSelect");
    const speedSelect = document.getElementById("speedSelect");

    const gridSize = 20;
    const tileCount = canvas.width / gridSize;

    let snake, apple, score, highScore, gameInterval, running, isPaused;
    let dx, dy, currentSpeed;
    let moveQueue = [];

    // --- AUDIO (8-bit beeps) ---
    const AudioContext = window.AudioContext || window.webkitAudioContext;
    const audioCtx = new AudioContext();

    function playSound(type) {
        if (audioCtx.state === 'suspended') audioCtx.resume();
        const osc = audioCtx.createOscillator();
        const gainNode = audioCtx.createGain();
        osc.connect(gainNode);
        gainNode.connect(audioCtx.destination);
        const now = audioCtx.currentTime;

        osc.type = 'square'; // 8-bit sound wave

        if (type === 'eat') {
            osc.frequency.setValueAtTime(600, now);
            osc.frequency.exponentialRampToValueAtTime(1200, now + 0.1);
            gainNode.gain.setValueAtTime(0.1, now);
            gainNode.gain.linearRampToValueAtTime(0, now + 0.1);
            osc.start(now);
            osc.stop(now + 0.1);
        } else if (type === 'die') {
            osc.frequency.setValueAtTime(300, now);
            osc.frequency.linearRampToValueAtTime(50, now + 0.3);
            gainNode.gain.setValueAtTime(0.2, now);
            gainNode.gain.linearRampToValueAtTime(0, now + 0.3);
            osc.start(now);
            osc.stop(now + 0.3);
        }
    }

    // --- THEME ENGINE ---
    function changeTheme() {
      const selectedTheme = themeSelect.value;
      body.setAttribute("data-theme", selectedTheme);
      // Redraw immediately to reflect changes if game is paused/stopped
      if (!running || isPaused) {
        draw(); 
      }
    }

    // --- GAME ENGINE ---
    function loadHighScore() {
        highScore = localStorage.getItem('snakeHighScore') || 0;
        document.getElementById("highScoreDisplay").innerText = highScore;
    }

    function initGame() {
      snake = [{x: 200, y: 200}, {x: 180, y: 200}, {x: 160, y: 200}];
      dx = gridSize;
      dy = 0;
      score = 0;
      document.getElementById("scoreDisplay").innerText = score;
      moveQueue = [];
      apple = randomApple();
      running = true;
      isPaused = false;
    }

    function startGame() {
      // Ensure audio context is ready
      if (audioCtx.state === 'suspended') audioCtx.resume();
      
      currentSpeed = parseInt(speedSelect.value);
      initGame();
      
      overlay.classList.add("hidden");
      startScreen.classList.add("hidden");
      gameOverScreen.classList.add("hidden");
      pauseScreen.classList.add("hidden");

      clearInterval(gameInterval);
      gameInterval = setInterval(gameLoop, currentSpeed);
    }

    function showMainMenu() {
        overlay.classList.remove("hidden");
        startScreen.classList.remove("hidden");
        gameOverScreen.classList.add("hidden");
        pauseScreen.classList.add("hidden");
        draw(); // Draw one frame so it's not blank
    }

    function togglePause() {
        if (!running) return;
        isPaused = !isPaused;
        
        if (isPaused) {
            clearInterval(gameInterval);
            overlay.classList.remove("hidden");
            pauseScreen.classList.remove("hidden");
        } else {
            overlay.classList.add("hidden");
            pauseScreen.classList.add("hidden");
            gameInterval = setInterval(gameLoop, currentSpeed);
        }
    }

    function endGame() {
        running = false;
        clearInterval(gameInterval);
        playSound('die');
        
        if (score > highScore) {
            highScore = score;
            localStorage.setItem('snakeHighScore', highScore);
            document.getElementById("highScoreDisplay").innerText = highScore;
        }

        document.getElementById("finalScore").innerText = score;
        overlay.classList.remove("hidden");
        gameOverScreen.classList.remove("hidden");
    }

    function randomApple() {
      let newApple;
      let collision;
      do {
        collision = false;
        newApple = {
          x: Math.floor(Math.random() * tileCount) * gridSize,
          y: Math.floor(Math.random() * tileCount) * gridSize
        };
        for(let part of snake) {
          if(part.x === newApple.x && part.y === newApple.y) collision = true;
        }
      } while(collision);
      return newApple;
    }

    function gameLoop() {
      if(isPaused || !running) return;

      if(moveQueue.length > 0) {
        const move = moveQueue.shift();
        dx = move.x;
        dy = move.y;
      }

      const head = { x: snake[0].x + dx, y: snake[0].y + dy };

      // Walls (Wrap-around)
      if(head.x < 0) head.x = canvas.width - gridSize;
      if(head.x >= canvas.width) head.x = 0;
      if(head.y < 0) head.y = canvas.height - gridSize;
      if(head.y >= canvas.height) head.y = 0;

      // Self Collision
      for(let part of snake) {
        if(part.x === head.x && part.y === head.y) {
          endGame();
          return;
        }
      }

      snake.unshift(head);

      if(head.x === apple.x && head.y === apple.y) {
        score++;
        document.getElementById("scoreDisplay").innerText = score;
        playSound('eat');
        apple = randomApple();
      } else {
        snake.pop();
      }

      draw();
    }

    function draw() {
      // Get colors from CSS variables
      const style = getComputedStyle(body);
      const bgColor = style.getPropertyValue('--panel-color');
      const snakeColor = style.getPropertyValue('--snake-color');
      const appleColor = style.getPropertyValue('--apple-color');
      const glow = style.getPropertyValue('--glow');

      // Clear Canvas
      ctx.fillStyle = bgColor;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Apply Glow (if theme has it)
      ctx.shadowBlur = parseInt(glow);
      ctx.shadowColor = snakeColor;

      // Draw Snake
      ctx.fillStyle = snakeColor;
      for(let i=0; i<snake.length; i++) {
        let part = snake[i];
        
        // "Gap" style for Nokia/Gameboy to make it look like separate pixels
        if (themeSelect.value === 'nokia' || themeSelect.value === 'gameboy') {
             ctx.fillRect(part.x + 1, part.y + 1, gridSize - 2, gridSize - 2);
        } else {
            // Smooth for Arcade/Neon
             ctx.fillRect(part.x, part.y, gridSize, gridSize);
        }
      }

      // Draw Apple
      ctx.shadowColor = appleColor;
      ctx.fillStyle = appleColor;
      if (themeSelect.value === 'nokia') {
         // Hollow square for Nokia apple
         ctx.fillRect(apple.x + 4, apple.y + 4, gridSize - 8, gridSize - 8);
         ctx.strokeRect(apple.x + 2, apple.y + 2, gridSize - 4, gridSize - 4);
      } else {
         ctx.fillRect(apple.x + 2, apple.y + 2, gridSize - 4, gridSize - 4);
      }
      
      ctx.shadowBlur = 0; // Reset glow
    }

    // Input Handling
    document.addEventListener("keydown", e => {
      if(e.code === "Space") {
        if(!running && startScreen.classList.contains('hidden')) return; // Game over state
        if(!running) startGame();
        else togglePause();
        return;
      }
      
      const key = e.key;
      let lastDx = dx; 
      let lastDy = dy;
      
      // Peek at queue if exists
      if(moveQueue.length > 0) {
        lastDx = moveQueue[moveQueue.length-1].x;
        lastDy = moveQueue[moveQueue.length-1].y;
      }
      
      let newDx = lastDx; 
      let newDy = lastDy;

      if(key === "ArrowUp" && lastDy === 0) { newDx = 0; newDy = -gridSize; }
      if(key === "ArrowDown" && lastDy === 0) { newDx = 0; newDy = gridSize; }
      if(key === "ArrowLeft" && lastDx === 0) { newDx = -gridSize; newDy = 0; }
      if(key === "ArrowRight" && lastDx === 0) { newDx = gridSize; newDy = 0; }

      if(newDx !== lastDx || newDy !== lastDy) {
        moveQueue.push({x: newDx, y: newDy});
      }
    });

    // Start
    loadHighScore();
    changeTheme(); // Apply initial theme
  </script>
</body>
</html>

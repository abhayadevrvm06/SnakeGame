<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Snake: Elegant Edition</title>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700&family=Lato:wght@300;400&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg-color: #121212;
      --panel-color: #1e1e1e;
      --accent-color: #d4af37; /* Metallic Gold */
      --secondary-accent: #f1c40f; /* Lighter Gold */
      --danger-color: #c0392b; /* Deep Crimson */
      --text-color: #e0e0e0;
      --border-color: #333333;
    }
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      background-color: var(--bg-color);
      /* Subtle noise texture instead of grid */
      background-image: radial-gradient(#333 1px, transparent 0);
      background-size: 20px 20px;
      font-family: 'Lato', sans-serif;
      color: var(--text-color);
      overflow: hidden;
    }
    .game-container {
      position: relative;
      background: var(--panel-color);
      padding: 25px;
      border: 1px solid var(--border-color);
      border-radius: 4px;
      box-shadow: 0 10px 40px rgba(0,0,0,0.8);
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 20px;
    }
    .hud {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0 5px;
      box-sizing: border-box;
      font-size: 12px;
      letter-spacing: 2px;
      text-transform: uppercase;
      color: #888;
    }
    .score-value {
      font-family: 'Playfair Display', serif;
      font-size: 28px;
      font-weight: 700;
      color: var(--accent-color);
      margin-left: 5px;
    }    
    .high-score {
        color: #888; 
    }
    canvas {
      background: #181818;
      border: 1px solid var(--accent-color); /* Gold Border */
      box-shadow: 0 0 15px rgba(0,0,0,0.5);
      cursor: none;
    }
    /* Overlay Styling */
    #overlay {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(18, 18, 18, 0.9);
      backdrop-filter: blur(5px);
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      z-index: 10;
      transition: opacity 0.3s ease;
    }
    .menu-content {
      background: transparent;
      padding: 30px;
      text-align: center;
      width: 300px;
    }
    h1 {
      font-family: 'Playfair Display', serif;
      font-size: 48px;
      margin: 0 0 20px 0;
      color: var(--accent-color);
      letter-spacing: 1px;
      font-weight: 400;
      /* Elegant gradient text */
      background: linear-gradient(to bottom, #f1c40f, #bf953f);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }    
    h1.game-over-title {
        background: linear-gradient(to bottom, #ff5e57, #c0392b);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        font-size: 38px;
    }
    label {
      font-size: 10px;
      text-transform: uppercase;
      letter-spacing: 3px;
      color: #666;
      display: block;
      margin-bottom: 10px;
    }
    select {
      width: 100%;
      padding: 10px;
      background: transparent;
      border: 1px solid #555;
      color: var(--accent-color);
      font-family: 'Lato', sans-serif;
      text-transform: uppercase;
      letter-spacing: 1px;
      cursor: pointer;
      margin-bottom: 25px;
      outline: none;
      text-align: center;
    }    
    select:hover {
      border-color: var(--accent-color);
    }
    .btn-primary {
      width: 100%;
      padding: 12px;
      background: var(--accent-color);
      color: #121212;
      border: none;
      font-family: 'Lato', sans-serif;
      font-size: 12px;
      letter-spacing: 2px;
      font-weight: 700;
      text-transform: uppercase;
      cursor: pointer;
      transition: all 0.3s;
      margin-bottom: 15px;
    }
    .btn-primary:hover {
      background: #fff;
    }
    .btn-secondary {
      width: 100%;
      padding: 12px;
      background: transparent;
      color: #666;
      border: 1px solid #444;
      font-family: 'Lato', sans-serif;
      font-size: 11px;
      letter-spacing: 2px;
      text-transform: uppercase;
      cursor: pointer;
      transition: all 0.3s;
    }
    .btn-secondary:hover {
      border-color: var(--text-color);
      color: var(--text-color);
    }
    #finalScoreDisplay {
      font-family: 'Playfair Display', serif;
      font-size: 22px;
      margin-bottom: 30px;
      color: var(--text-color);
      font-style: italic;
    }
    .tips {
        margin-top: 20px;
        font-size: 10px;
        color: #444;
        letter-spacing: 1px;
        text-transform: uppercase;
    }
    .hidden {
      display: none !important;
    }
  </style>
</head>
<body>

  <div class="game-container">
    <div class="hud">
      <div class="score-box">
        <span>Score <span id="scoreDisplay" class="score-value">0</span></span>
      </div>
      <div class="score-box">
         <span class="high-score">Best <span id="highScoreDisplay" class="score-value">0</span></span>
      </div>
    </div>
    <canvas id="gameCanvas" width="400" height="400"></canvas>
    <div id="overlay">
      <div class="menu-content">    
        <div id="startScreen">
          <h1>The Snake</h1>
          <label for="speed">Select Pace</label>
          <select id="speed">
            <option value="250">Leisurely (Easy)</option>
            <option value="180" selected>Standard (Normal)</option>
            <option value="130">Rapid (Hard)</option>
            <option value="90">Intense (Extreme)</option>
          </select>
          <button class="btn-primary" onclick="startGame()">Begin</button>
          <div class="tips">Spacebar to Pause</div>
        </div>
        <div id="gameOverScreen" class="hidden">
          <h1 class="game-over-title">Game Over</h1>
          <div id="finalScoreDisplay"></div>
          <button class="btn-primary" onclick="startGame()">Try Again</button>
          <button class="btn-secondary" onclick="showMainMenu()">Return to Menu</button>
        </div>
        <div id="pauseScreen" class="hidden">
            <h1 style="color: #fff; font-size: 32px;">Paused</h1>
            <button class="btn-primary" onclick="togglePause()">Resume</button>
            <button class="btn-secondary" onclick="quitToMenu()">Quit</button>
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
    const pauseScreen = document.getElementById("pauseScreen");
    
    const speedSelect = document.getElementById("speed");
    const scoreEl = document.getElementById("scoreDisplay");
    const highScoreEl = document.getElementById("highScoreDisplay");
    const finalScoreEl = document.getElementById("finalScoreDisplay");

    const gridSize = 20;
    const tileCount = canvas.width / gridSize;
    
    let snake, apple, score, highScore, gameInterval, running, isPaused;
    let dx, dy, currentSpeed;
    let moveQueue = []; 
    let particles = [];

    // --- AUDIO SYSTEM (Retuned for elegance) ---
    const AudioContext = window.AudioContext || window.webkitAudioContext;
    const audioCtx = new AudioContext();

    function playSound(type) {
        if (audioCtx.state === 'suspended') audioCtx.resume();
        const osc = audioCtx.createOscillator();
        const gainNode = audioCtx.createGain();
        osc.connect(gainNode);
        gainNode.connect(audioCtx.destination);

        const now = audioCtx.currentTime;

        if (type === 'eat') {
            // A soft "chime" sound instead of a beep
            osc.type = 'sine';
            osc.frequency.setValueAtTime(500, now);
            osc.frequency.exponentialRampToValueAtTime(800, now + 0.1);
            gainNode.gain.setValueAtTime(0.1, now);
            gainNode.gain.linearRampToValueAtTime(0, now + 0.15);
            osc.start(now);
            osc.stop(now + 0.15);
        } else if (type === 'die') {
            // A low hum for game over
            osc.type = 'triangle';
            osc.frequency.setValueAtTime(100, now);
            osc.frequency.linearRampToValueAtTime(50, now + 0.5);
            gainNode.gain.setValueAtTime(0.2, now);
            gainNode.gain.linearRampToValueAtTime(0, now + 0.5);
            osc.start(now);
            osc.stop(now + 0.5);
        }
    }

    // --- LOCAL STORAGE ---
    function loadHighScore() {
        const saved = localStorage.getItem('snakeHighScore');
        highScore = saved ? parseInt(saved) : 0;
        highScoreEl.innerText = highScore;
    }

    function saveHighScore() {
        if (score > highScore) {
            highScore = score;
            localStorage.setItem('snakeHighScore', highScore);
            highScoreEl.innerText = highScore;
        }
    }

    // --- PARTICLE SYSTEM (Gold Dust) ---
    function createParticles(x, y) {
        for(let i = 0; i < 8; i++) {
            particles.push({
                x: x + gridSize/2,
                y: y + gridSize/2,
                vx: (Math.random() - 0.5) * 4, 
                vy: (Math.random() - 0.5) * 4, 
                life: 1.0, 
                color: "#d4af37" // Gold dust
            });
        }
    }

    function updateParticles() {
        for(let i = particles.length - 1; i >= 0; i--) {
            let p = particles[i];
            p.x += p.vx;
            p.y += p.vy;
            p.life -= 0.04; 
            
            if(p.life <= 0) {
                particles.splice(i, 1);
            } else {
                ctx.globalAlpha = p.life;
                ctx.fillStyle = p.color;
                ctx.fillRect(p.x, p.y, 2, 2); // Tiny dust particles
                ctx.globalAlpha = 1.0;
            }
        }
    }

    // --- GAME LOGIC ---

    function initGame() {
      // Start centered
      snake = [{ x: 200, y: 200 }, { x: 180, y: 200 }, { x: 160, y: 200 }];
      dx = gridSize;
      dy = 0;
      score = 0;
      particles = [];
      updateScoreUI();
      moveQueue = []; 
      apple = randomApple();
      running = true;
      isPaused = false;
      loadHighScore();
    }

    function showMainMenu() {
        overlay.classList.remove("hidden");
        startScreen.classList.remove("hidden");
        gameOverScreen.classList.add("hidden");
        pauseScreen.classList.add("hidden");
    }

    function startGame() {
      currentSpeed = parseInt(speedSelect.value);
      overlay.classList.add("hidden");
      initGame();
      clearInterval(gameInterval);
      gameInterval = setInterval(gameLoop, currentSpeed);
    }

    function quitToMenu() {
        running = false;
        clearInterval(gameInterval);
        showMainMenu();
    }

    function togglePause() {
        if(!running) return;
        isPaused = !isPaused;
        
        if (isPaused) {
            clearInterval(gameInterval);
            overlay.classList.remove("hidden");
            startScreen.classList.add("hidden");
            pauseScreen.classList.remove("hidden");
            gameOverScreen.classList.add("hidden");
        } else {
            overlay.classList.add("hidden");
            gameInterval = setInterval(gameLoop, currentSpeed);
        }
    }

    function endGame() {
      running = false;
      clearInterval(gameInterval);
      playSound('die');
      saveHighScore();
      
      overlay.classList.remove("hidden");
      startScreen.classList.add("hidden");
      pauseScreen.classList.add("hidden");
      gameOverScreen.classList.remove("hidden");
      
      finalScoreEl.innerText = "Final Score: " + score;
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
          for (let part of snake) {
              if (part.x === newApple.x && part.y === newApple.y) {
                  collision = true;
                  break;
              }
          }
      } while (collision);
      return newApple;
    }

    function updateScoreUI() {
        scoreEl.innerText = score;
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

      if (head.x === apple.x && head.y === apple.y) {
        score++;
        playSound('eat');
        createParticles(apple.x, apple.y); 
        apple = randomApple();
        increaseSpeed();
        updateScoreUI();
      } else {
        snake.pop();
      }

      // --- ELEGANT DRAWING ---
      
      // Clear with matte background
      ctx.fillStyle = "#181818";
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      
      // Subtle Grid (Dots)
      ctx.fillStyle = "#222"; 
      for (let x = 0; x <= canvas.width; x += gridSize) {
          for (let y = 0; y <= canvas.height; y += gridSize) {
              ctx.fillRect(x-1, y-1, 2, 2);
          }
      }
      
      updateParticles();

      // Apple (Deep Crimson, no glow)
      ctx.fillStyle = "#c0392b"; 
      ctx.fillRect(apple.x + 2, apple.y + 2, gridSize - 4, gridSize - 4);

      // Snake (Gold Gradient Effect)
      for (let i = 0; i < snake.length; i++) {
        if (i === 0) {
            ctx.fillStyle = "#d4af37"; // Metallic Gold Head
        } else {
            // Alternating subtle gold shades for body
            ctx.fillStyle = i % 2 === 0 ? "#bca136" : "#a88f32";
        }
        ctx.fillRect(snake[i].x, snake[i].y, gridSize, gridSize);
        
        // Slight border to separate segments
        ctx.strokeStyle = "#181818";
        ctx.lineWidth = 1;
        ctx.strokeRect(snake[i].x, snake[i].y, gridSize, gridSize);
      }
    }

    document.addEventListener("keydown", e => {
      if (e.code === "Space" || e.code === "Escape") {
          togglePause();
          return;
      }
      
      if (isPaused || !running) return;

      let lastDx = dx;
      let lastDy = dy;
      if (moveQueue.length > 0) {
          const lastMove = moveQueue[moveQueue.length - 1];
          lastDx = lastMove.x;
          lastDy = lastMove.y;
      }
      let newDx = lastDx;
      let newDy = lastDy;

      if (e.key === "ArrowUp" && lastDy === 0) { newDx = 0; newDy = -gridSize; }
      else if (e.key === "ArrowDown" && lastDy === 0) { newDx = 0; newDy = gridSize; }
      else if (e.key === "ArrowLeft" && lastDx === 0) { newDx = -gridSize; newDy = 0; }
      else if (e.key === "ArrowRight" && lastDx === 0) { newDx = gridSize; newDy = 0; }

      if (newDx !== lastDx || newDy !== lastDy) {
          moveQueue.push({ x: newDx, y: newDy });
      }
    });
    
    // Initial setup
    loadHighScore();
    
    // Draw empty grid on load
    ctx.fillStyle = "#181818";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = "#222"; 
      for (let x = 0; x <= canvas.width; x += gridSize) {
          for (let y = 0; y <= canvas.height; y += gridSize) {
              ctx.fillRect(x-1, y-1, 2, 2);
          }
      }
  </script>
</body>
</html>

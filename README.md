<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Snake: Ultimate Edition</title>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=Lato:wght@400;700&family=Press+Start+2P&family=Orbitron:wght@500&display=swap" rel="stylesheet">
  
  <style>
    /* --- THEME VARIABLES --- */
    :root {
      /* Default (Elegant) */
      --bg-color: #121212;
      --panel-color: #1e1e1e;
      --accent-color: #d4af37;
      --snake-head: #d4af37;
      --snake-body: #bca136;
      --apple-color: #c0392b;
      --text-color: #e0e0e0;
      --font-main: 'Lato', sans-serif;
      --font-header: 'Playfair Display', serif;
      --glow: 0 0 0 transparent;
      --grid-opacity: 0.1;
    }

    /* Classic Theme (Nokia Style) */
    body.theme-classic {
      --bg-color: #9bbc0f;
      --panel-color: #8bac0f;
      --accent-color: #0f380f;
      --snake-head: #0f380f;
      --snake-body: #306230;
      --apple-color: #0f380f;
      --text-color: #0f380f;
      --font-main: 'Press Start 2P', monospace; /* Retro Pixel Font */
      --font-header: 'Press Start 2P', monospace;
      --glow: none;
      --grid-opacity: 0.2;
    }

    /* Neon Theme (Cyberpunk) */
    body.theme-neon {
      --bg-color: #050510;
      --panel-color: #101025;
      --accent-color: #00ffcc;
      --snake-head: #00ffcc;
      --snake-body: #00ccaa;
      --apple-color: #ff00ff;
      --text-color: #fff;
      --font-main: 'Orbitron', sans-serif; /* Sci-Fi Font */
      --font-header: 'Orbitron', sans-serif;
      --glow: 0 0 10px;
    }

    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      background-color: var(--bg-color);
      font-family: var(--font-main);
      color: var(--text-color);
      overflow: hidden;
      touch-action: none;
      transition: background-color 0.5s ease, color 0.5s ease;
    }

    .game-container {
      position: relative;
      background: var(--panel-color);
      padding: 20px;
      border: 2px solid var(--accent-color);
      border-radius: 4px;
      box-shadow: 0 10px 40px rgba(0,0,0,0.5);
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 15px;
      max-width: 95vw;
      transition: background 0.5s ease, border-color 0.5s ease;
    }

    /* HUD */
    .hud {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 12px;
      text-transform: uppercase;
      opacity: 0.8;
    }

    .score-value {
      font-family: var(--font-header);
      font-size: 24px;
      color: var(--accent-color);
      text-shadow: var(--glow) var(--accent-color);
    }

    canvas {
      background: rgba(0,0,0,0.1);
      border: 1px solid var(--accent-color);
      box-shadow: var(--glow) var(--accent-color);
      cursor: pointer;
      touch-action: none;
    }

    /* Menus & Overlay */
    #overlay {
      position: absolute;
      top: 0; left: 0; width: 100%; height: 100%;
      background: rgba(0, 0, 0, 0.85);
      backdrop-filter: blur(4px);
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      z-index: 10;
      transition: opacity 0.3s;
    }

    .menu-content {
      width: 320px;
      max-width: 90%;
      text-align: center;
    }

    h1 {
      font-family: var(--font-header);
      font-size: 36px;
      margin-bottom: 20px;
      color: var(--accent-color);
      text-shadow: var(--glow) var(--accent-color);
      line-height: 1.2;
    }

    /* Controls */
    .control-group {
      margin-bottom: 15px;
      text-align: left;
    }

    label {
      font-size: 10px;
      text-transform: uppercase;
      display: block;
      margin-bottom: 5px;
      opacity: 0.7;
    }

    select, button {
      width: 100%;
      padding: 12px;
      font-family: var(--font-main);
      text-transform: uppercase;
      border-radius: 4px;
      cursor: pointer;
      outline: none;
      font-size: 12px;
    }

    select {
      background: transparent;
      border: 1px solid var(--accent-color);
      color: var(--text-color);
      margin-bottom: 10px;
    }

    option {
      background: var(--panel-color);
      color: var(--text-color);
    }

    .btn-primary {
      background: var(--accent-color);
      color: var(--bg-color); /* Contrast text */
      border: none;
      font-weight: bold;
      box-shadow: var(--glow) var(--accent-color);
    }
    
    .btn-primary:hover { filter: brightness(1.2); }

    .btn-secondary {
      background: transparent;
      border: 1px solid var(--text-color);
      color: var(--text-color);
      margin-top: 10px;
    }

    /* Fact Box */
    .fact-container {
      margin-top: 25px;
      padding-top: 15px;
      border-top: 1px solid var(--accent-color);
      min-height: 80px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }

    .fact-text {
      font-style: italic;
      font-size: 12px;
      opacity: 0.8;
      line-height: 1.4;
      margin-bottom: 10px;
    }

    .fact-btn {
      background: none;
      border: none;
      color: var(--accent-color);
      font-size: 10px;
      cursor: pointer;
      padding: 5px;
    }
    .fact-btn:hover { text-decoration: underline; }

    .hidden { display: none !important; }
  </style>
</head>
<body class="theme-elegant"> <div class="game-container">    
    <div class="hud">
      <div>Score <span id="scoreDisplay" class="score-value">0</span></div>
      <div>Best <span id="highScoreDisplay" class="score-value">0</span></div>
    </div>
    <canvas id="gameCanvas" width="360" height="400"></canvas>
    <div id="overlay">
      <div class="menu-content">        
        <div id="startScreen">
          <h1>SNAKE</h1>         
          <div class="control-group">
            <label>Theme</label>
            <select id="themeSelect" onchange="changeTheme()">
              <option value="theme-elegant" selected>Elegant (Gold)</option>
              <option value="theme-neon">Cyberpunk (Neon)</option>
              <option value="theme-classic">Nokia (Classic)</option>
            </select>
          </div>
          <div class="control-group">
            <label>Difficulty</label>
            <select id="speedSelect">
              <option value="200">Slow</option>
              <option value="130" selected>Normal</option>
              <option value="80">Fast</option>
            </select>
          </div>
          <button class="btn-primary" onclick="startGame()">Start Game</button>
          <div style="font-size:10px; margin-top:10px; opacity:0.6">Swipe to Move • Tap to Pause</div>
          <div class="fact-container">
            <div id="fact-text" class="fact-text">Loading fact...</div>
            <button class="fact-btn" onclick="showRandomFact()">Next Fact ↻</button>
          </div>
        </div>
        <div id="gameOverScreen" class="hidden">
          <h1>GAME OVER</h1>
          <div style="font-size: 18px; margin-bottom: 20px;">Score: <span id="finalScoreDisplay"></span></div>
          <button class="btn-primary" onclick="startGame()">Try Again</button>
          <button class="btn-secondary" onclick="showMainMenu()">Menu</button>
        </div>
        <div id="pauseScreen" class="hidden">
          <h1>PAUSED</h1>
          <button class="btn-primary" onclick="togglePause()">Resume</button>
          <button class="btn-secondary" onclick="quitToMenu()">Quit</button>
        </div>
      </div>
    </div>
  </div>

  <script>
    /* --- CONFIG & STATE --- */
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    const gridSize = 20;
    const tileCountX = canvas.width / gridSize;
    const tileCountY = canvas.height / gridSize;

    // Elements
    const overlay = document.getElementById("overlay");
    const startScreen = document.getElementById("startScreen");
    const gameOverScreen = document.getElementById("gameOverScreen");
    const pauseScreen = document.getElementById("pauseScreen");
    const scoreEl = document.getElementById("scoreDisplay");
    const highScoreEl = document.getElementById("highScoreDisplay");
    const finalScoreEl = document.getElementById("finalScoreDisplay");
    const factTextEl = document.getElementById("fact-text");
    const themeSelect = document.getElementById("themeSelect");

    // Game Variables
    let snake = [], apple = {}, moveQueue = [], particles = [];
    let dx = gridSize, dy = 0;
    let score = 0, highScore = 0;
    let gameInterval, running = false, isPaused = false;
    let currentSpeed = 130;

    /* --- THEME SYSTEM --- */
    function changeTheme() {
      const selectedTheme = themeSelect.value;
      document.body.className = selectedTheme;
      localStorage.setItem('snakeTheme', selectedTheme);
      // Redraw empty grid to show new colors immediately
      if(!running) drawGameStatic();
    }

    // Load saved theme on startup
    const savedTheme = localStorage.getItem('snakeTheme');
    if(savedTheme) {
      document.body.className = savedTheme;
      themeSelect.value = savedTheme;
    }

    /* --- FACT SYSTEM --- */
    const snakeFacts = [
        "The original Snake was launched in 1997 on the Nokia 6110.",
        "A perfect game of Snake ends when the snake fills the whole screen.",
        "The concept dates back to the 1976 arcade game 'Blockade'.",
        "Snake was pre-installed on over 400 million mobile phones.",
        "Google Maps added a version of Snake for April Fools' Day 2017.",
        "The highest possible score in Nokia Snake is roughly 2008.",
        "Snake II on the Nokia 3310 introduced mazes and walls.",
        "The creator, Taneli Armanto, didn't trademark the game mechanics.",
        "In some versions, the snake speeds up as it eats more.",
        "There are over 400 versions of Snake on the App Store.",
        "The Apple in the original game was just a single black pixel.",
        "MoMA in New York added Snake to its collection in 2013.",
        "Russian devs made a clone called 'Python' for Soviet PCs in the 80s.",
        "The game is known as 'Nibbles' in QBasic.",
        "Multiplayer Snake existed on phones via Infrared ports.",
        "A 3D version 'Snakes' was released for the Nokia N-Gage.",
        "It is physically impossible to react to the fastest speed levels.",
        "Some calculators have hidden Snake games programmed by students.",
        "The snake cannot move diagonally in the classic version.",
        "Snake was the first mobile game for millions of people.",
        "A 'perfect' AI solver can beat Snake by following a specific cycle.",
        "The snake usually grows by 1 unit, but some mods vary this.",
        "In 2015, the creator released a sequel called 'Snake Rewind'.",
        "YouTube's buffering circle could play Snake in 2010.",
        "The game is a classic example of array data structures in coding."
    ];

    function showRandomFact() {
        const randomIndex = Math.floor(Math.random() * snakeFacts.length);
        factTextEl.style.opacity = 0;
        setTimeout(() => {
            factTextEl.innerText = snakeFacts[randomIndex];
            factTextEl.style.opacity = 1;
        }, 200);
    }
    factTextEl.style.transition = "opacity 0.2s ease";

    /* --- AUDIO --- */
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
            osc.frequency.setValueAtTime(600, now);
            osc.frequency.exponentialRampToValueAtTime(1000, now + 0.1);
            gainNode.gain.setValueAtTime(0.1, now);
            gainNode.gain.linearRampToValueAtTime(0, now + 0.1);
            osc.start(now);
            osc.stop(now + 0.1);
        } else if (type === 'die') {
            osc.type = 'sawtooth';
            osc.frequency.setValueAtTime(100, now);
            osc.frequency.linearRampToValueAtTime(50, now + 0.5);
            gainNode.gain.setValueAtTime(0.1, now);
            gainNode.gain.linearRampToValueAtTime(0, now + 0.5);
            osc.start(now);
            osc.stop(now + 0.5);
        }
    }

    /* --- GAME ENGINE --- */
    function initGame() {
      const startX = Math.floor(tileCountX / 2) * gridSize;
      const startY = Math.floor(tileCountY / 2) * gridSize;
      snake = [{x: startX, y: startY}, {x: startX-gridSize, y: startY}, {x: startX-gridSize*2, y: startY}];
      apple = randomApple();
      score = 0;
      dx = gridSize; dy = 0;
      moveQueue = [];
      particles = [];
      updateScore();
      loadHighScore();
    }

    function startGame() {
        currentSpeed = parseInt(document.getElementById("speedSelect").value);
        overlay.classList.add("hidden");
        running = true;
        isPaused = false;
        initGame();
        if(gameInterval) clearInterval(gameInterval);
        gameInterval = setInterval(gameLoop, currentSpeed);
    }

    function showMainMenu() {
        running = false;
        clearInterval(gameInterval);
        overlay.classList.remove("hidden");
        startScreen.classList.remove("hidden");
        gameOverScreen.classList.add("hidden");
        pauseScreen.classList.add("hidden");
        showRandomFact();
        drawGameStatic();
    }

    function togglePause() {
        if (!running) return;
        isPaused = !isPaused;
        if (isPaused) {
            clearInterval(gameInterval);
            overlay.classList.remove("hidden");
            startScreen.classList.add("hidden");
            pauseScreen.classList.remove("hidden");
        } else {
            overlay.classList.add("hidden");
            gameInterval = setInterval(gameLoop, currentSpeed);
        }
    }

    function quitToMenu() {
        showMainMenu();
    }

    function gameOver() {
        running = false;
        clearInterval(gameInterval);
        playSound('die');
        saveHighScore();
        overlay.classList.remove("hidden");
        startScreen.classList.add("hidden");
        pauseScreen.classList.add("hidden");
        gameOverScreen.classList.remove("hidden");
        finalScoreEl.innerText = score;
    }

    function gameLoop() {
        // Movement Logic
        if (moveQueue.length) {
            const move = moveQueue.shift();
            dx = move.x; dy = move.y;
        }

        const head = { x: snake[0].x + dx, y: snake[0].y + dy };

        // Wall Loop
        if (head.x < 0) head.x = canvas.width - gridSize;
        if (head.x >= canvas.width) head.x = 0;
        if (head.y < 0) head.y = canvas.height - gridSize;
        if (head.y >= canvas.height) head.y = 0;

        // Self Collision
        if (snake.some(part => part.x === head.x && part.y === head.y)) {
            gameOver();
            return;
        }

        snake.unshift(head);

        // Eat Apple
        if (head.x === apple.x && head.y === apple.y) {
            score++;
            updateScore();
            playSound('eat');
            createParticles(apple.x, apple.y);
            apple = randomApple();
            // Speed up slightly
            if (currentSpeed > 50 && score % 5 === 0) {
                clearInterval(gameInterval);
                currentSpeed -= 2;
                gameInterval = setInterval(gameLoop, currentSpeed);
            }
        } else {
            snake.pop();
        }

        drawGame();
    }

    /* --- RENDERING --- */
    function getCSSVar(name) {
        return getComputedStyle(document.body).getPropertyValue(name).trim();
    }

    function drawGame() {
        // Clear
        ctx.fillStyle = getCSSVar('--bg-color');
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        // Grid (Subtle)
        ctx.fillStyle = getCSSVar('--text-color');
        ctx.globalAlpha = 0.1; 
        for(let i=0; i<canvas.width; i+=gridSize) ctx.fillRect(i,0,1,canvas.height);
        for(let i=0; i<canvas.height; i+=gridSize) ctx.fillRect(0,i,canvas.width,1);
        ctx.globalAlpha = 1.0;

        // Particles
        updateParticles();

        // Apple
        ctx.fillStyle = getCSSVar('--apple-color');
        ctx.shadowBlur = document.body.classList.contains('theme-neon') ? 15 : 0;
        ctx.shadowColor = ctx.fillStyle;
        ctx.fillRect(apple.x+2, apple.y+2, gridSize-4, gridSize-4);
        ctx.shadowBlur = 0;

        // Snake
        snake.forEach((part, index) => {
            ctx.fillStyle = index === 0 ? getCSSVar('--snake-head') : getCSSVar('--snake-body');
            
            // Glow for Neon theme
            if(document.body.classList.contains('theme-neon')) {
                ctx.shadowBlur = 10;
                ctx.shadowColor = ctx.fillStyle;
            }
            
            ctx.fillRect(part.x, part.y, gridSize, gridSize);
            ctx.shadowBlur = 0;

            // Borders for non-neon themes
            if(!document.body.classList.contains('theme-neon')) {
                ctx.strokeStyle = getCSSVar('--bg-color');
                ctx.lineWidth = 1;
                ctx.strokeRect(part.x, part.y, gridSize, gridSize);
            }
        });
    }

    function drawGameStatic() {
        // Draws background when game isn't running
        ctx.fillStyle = getCSSVar('--bg-color');
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        
        ctx.fillStyle = getCSSVar('--text-color');
        ctx.globalAlpha = 0.1; 
        for(let i=0; i<canvas.width; i+=gridSize) ctx.fillRect(i,0,1,canvas.height);
        for(let i=0; i<canvas.height; i+=gridSize) ctx.fillRect(0,i,canvas.width,1);
        ctx.globalAlpha = 1.0;
    }

    /* --- UTILS --- */
    function randomApple() {
        let newApple;
        while(true) {
            newApple = {
                x: Math.floor(Math.random() * tileCountX) * gridSize,
                y: Math.floor(Math.random() * tileCountY) * gridSize
            };
            // Check collision with snake
            let collision = snake.some(p => p.x === newApple.x && p.y === newApple.y);
            if(!collision) break;
        }
        return newApple;
    }

    function createParticles(x, y) {
        const color = getCSSVar('--apple-color');
        for(let i=0; i<8; i++) {
            particles.push({
                x: x+gridSize/2, y: y+gridSize/2,
                vx: (Math.random()-0.5)*5, vy: (Math.random()-0.5)*5,
                life: 1.0, color: color
            });
        }
    }

    function updateParticles() {
        for(let i=particles.length-1; i>=0; i--) {
            let p = particles[i];
            p.x += p.vx; p.y += p.vy;
            p.life -= 0.05;
            if(p.life <= 0) particles.splice(i,1);
            else {
                ctx.globalAlpha = p.life;
                ctx.fillStyle = p.color;
                ctx.fillRect(p.x, p.y, 3, 3);
                ctx.globalAlpha = 1.0;
            }
        }
    }

    function updateScore() {
        scoreEl.innerText = score;
    }

    function saveHighScore() {
        if(score > highScore) {
            highScore = score;
            localStorage.setItem('snakeHighScore', highScore);
        }
        loadHighScore();
    }

    function loadHighScore() {
        const saved = localStorage.getItem('snakeHighScore');
        highScore = saved ? parseInt(saved) : 0;
        highScoreEl.innerText = highScore;
    }

    /* --- INPUTS --- */
    // Keyboard
    document.addEventListener("keydown", e => {
        if (e.code === "Space" || e.code === "Escape") {
            togglePause();
            return;
        }
        if (!running || isPaused) return;

        const key = e.key;
        let lastMove = moveQueue.length ? moveQueue[moveQueue.length-1] : {x: dx, y: dy};
        
        let newDx = lastMove.x, newDy = lastMove.y;

        if (key === "ArrowUp" && lastMove.y === 0) { newDx = 0; newDy = -gridSize; }
        else if (key === "ArrowDown" && lastMove.y === 0) { newDx = 0; newDy = gridSize; }
        else if (key === "ArrowLeft" && lastMove.x === 0) { newDx = -gridSize; newDy = 0; }
        else if (key === "ArrowRight" && lastMove.x === 0) { newDx = gridSize; newDy = 0; }

        if (newDx !== lastMove.x || newDy !== lastMove.y) {
            moveQueue.push({x: newDx, y: newDy});
        }
    });

    // Touch / Swipe
    let touchStartX = 0, touchStartY = 0;
    
    canvas.addEventListener('touchstart', e => {
        touchStartX = e.changedTouches[0].screenX;
        touchStartY = e.changedTouches[0].screenY;
        e.preventDefault();
    }, {passive: false});

    canvas.addEventListener('touchend', e => {
        if (isPaused && running) { togglePause(); return; }
        
        let touchEndX = e.changedTouches[0].screenX;
        let touchEndY = e.changedTouches[0].screenY;
        
        let diffX = touchEndX - touchStartX;
        let diffY = touchEndY - touchStartY;

        if(Math.abs(diffX) < 10 && Math.abs(diffY) < 10) {
            if(running) togglePause();
            return;
        }

        if (!running || isPaused) return;

        let lastMove = moveQueue.length ? moveQueue[moveQueue.length-1] : {x: dx, y: dy};
        let newDx = lastMove.x, newDy = lastMove.y;

        if (Math.abs(diffX) > Math.abs(diffY)) {
            // Horizontal
            if (diffX > 0 && lastMove.x === 0) { newDx = gridSize; newDy = 0; } // Right
            else if (diffX < 0 && lastMove.x === 0) { newDx = -gridSize; newDy = 0; } // Left
        } else {
            // Vertical
            if (diffY > 0 && lastMove.y === 0) { newDx = 0; newDy = gridSize; } // Down
            else if (diffY < 0 && lastMove.y === 0) { newDx = 0; newDy = -gridSize; } // Up
        }

        if (newDx !== lastMove.x || newDy !== lastMove.y) {
            moveQueue.push({x: newDx, y: newDy});
        }
        e.preventDefault();
    }, {passive: false});

    // Init
    loadHighScore();
    showRandomFact();
    drawGameStatic();
  </script>
</body>
</html>

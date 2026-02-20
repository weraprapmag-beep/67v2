<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>CYBER MATH: THE FINAL PROTOCOL</title>
    <style>
        :root { --neon: #0f0; --cyber: #0ff; --danger: #f00; --gold: #ff0; }
        
        body { 
            margin: 0; background: #000; color: var(--neon); 
            font-family: 'Courier New', monospace; overflow: hidden; 
            touch-action: none; -webkit-user-select: none; user-select: none;
        }

        #game-wrap { position: relative; width: 100vw; height: 100vh; display: flex; flex-direction: column; }

        /* Effects */
        .scanlines { position: absolute; inset: 0; pointer-events: none; z-index: 200; background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.1) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.03), rgba(0, 255, 0, 0.01), rgba(0, 0, 255, 0.03)); background-size: 100% 4px, 3px 100%; }
        .vignette { position: absolute; inset: 0; background: radial-gradient(circle, transparent 40%, black 150%); z-index: 150; pointer-events: none; }

        /* Start Screen */
        #start-screen { 
            position: absolute; inset: 0; background: radial-gradient(circle at center, #001a1a, #000);
            display: flex; flex-direction: column; align-items: center; justify-content: center; z-index: 100; 
        }
        .glitch-title { font-size: 3.5rem; color: #fff; text-shadow: 4px 0 var(--danger), -4px 0 var(--cyber); letter-spacing: 5px; margin-bottom: 5px; }
        .tagline { color: var(--cyber); letter-spacing: 3px; font-weight: bold; margin-bottom: 30px; }
        
        .char-preview { 
            width: 50px; height: 70px; background: var(--cyber); box-shadow: 0 0 30px var(--cyber);
            animation: float 2s infinite ease-in-out; position: relative; margin-bottom: 40px;
        }
        .char-preview::after { content: ''; position: absolute; top: 15px; right: -5px; width: 15px; height: 6px; background: #fff; box-shadow: 0 0 10px #fff; }

        @keyframes float { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-20px); } }

        #btn-start { 
            background: transparent; border: 3px solid var(--neon); color: var(--neon); 
            padding: 15px 50px; font-size: 1.5rem; cursor: pointer; transition: 0.3s;
            clip-path: polygon(10% 0, 100% 0, 90% 100%, 0 100%);
        }
        #btn-start:hover { background: var(--neon); color: #000; box-shadow: 0 0 40px var(--neon); }

        /* UI Overlay */
        #ui-overlay { 
            position: absolute; top: 0; width: 100%; padding: 20px; 
            display: flex; justify-content: space-between; z-index: 50; pointer-events: none; box-sizing: border-box; 
        }
        #question { font-size: 24px; color: #fff; background: rgba(0,0,0,0.8); padding: 10px 20px; border-left: 5px solid var(--cyber); }
        #score-box { font-size: 22px; border: 2px solid var(--neon); padding: 5px 20px; background: #000; }

        /* Canvas */
        canvas { display: block; width: 100%; flex-grow: 1; background: #000; }

        /* Mobile Controls */
        #mobile-controls { 
            height: 35vh; background: #080808; display: flex; 
            justify-content: space-between; padding: 0 40px; align-items: center; 
            border-top: 3px solid #222; 
        }
        .dpad { display: flex; flex-direction: column; gap: 20px; }
        .ctrl-btn { 
            width: 85px; height: 85px; display: flex; align-items: center; justify-content: center; 
            font-size: 35px; border-radius: 15px; border: 2px solid var(--cyber); color: var(--cyber);
            box-shadow: inset 0 0 10px var(--cyber); -webkit-tap-highlight-color: transparent;
        }
        .fire-btn { 
            width: 120px; height: 120px; border-radius: 50%; border-color: var(--danger); 
            color: var(--danger); font-size: 26px; font-weight: bold; box-shadow: 0 0 20px rgba(255,0,0,0.3);
        }
        .ctrl-btn:active { background: rgba(0, 255, 255, 0.2); transform: scale(0.95); }
        .fire-btn:active { background: rgba(255, 0, 0, 0.2); }

        /* Boss UI */
        #boss-ui { position: absolute; top: 100px; width: 70%; left: 15%; display: none; text-align: center; }
        #boss-hp-bar { width: 100%; height: 12px; background: #222; border: 1px solid #fff; margin-top: 5px; }
        #boss-hp-fill { height: 100%; background: var(--danger); box-shadow: 0 0 15px var(--danger); transition: width 0.2s; }
        #boss-warning { position: absolute; top: 40%; width: 100%; text-align: center; color: var(--danger); font-size: 2.5rem; display: none; animation: blink 0.2s infinite; z-index: 60; }
        @keyframes blink { 0% { opacity: 0; } 100% { opacity: 1; } }

        /* End Screen */
        #ending-screen { position: absolute; inset: 0; background: #000; display: flex; flex-direction: column; align-items: center; justify-content: center; z-index: 110; text-align: center; display: none; }
    </style>
</head>
<body>

<div id="game-wrap">
    <div class="scanlines"></div>
    <div class="vignette"></div>

    <div id="start-screen">
        <h1 class="glitch-title">CYBER MATH</h1>
        <p class="tagline">PROTOCOL: REBOOT 2026</p>
        <div class="char-preview"></div>
        <button id="btn-start">ACTIVATE SYSTEM</button>
    </div>

    <div id="boss-warning">CRITICAL ERROR: SYSTEM ANOMALY</div>

    <div id="ending-screen">
        <h1 style="font-size: 3rem; color: var(--cyber);">MISSION COMPLETE</h1>
        <p id="final-stats" style="font-size: 1.5rem; margin-bottom: 30px;">DATA SECURED: 0000</p>
        <button onclick="location.reload()" id="btn-start">REBOOT</button>
    </div>

    <div id="ui-overlay" style="display:none;">
        <div id="question">TARGET: ? + ?</div>
        <div id="score-box">SECURED: <span id="score-val">0000</span></div>
    </div>
    
    <div id="boss-ui">
        <div style="font-size: 12px; color: var(--danger);">CORE OVERLORD</div>
        <div id="boss-hp-bar"><div id="boss-hp-fill" style="width:100%"></div></div>
    </div>

    <canvas id="gameCanvas"></canvas>

    <div id="mobile-controls" style="display:none;">
        <div class="dpad">
            <div id="btn-up" class="ctrl-btn">▲</div>
            <div id="btn-down" class="ctrl-btn">▼</div>
        </div>
        <div class="action-zone">
            <div id="btn-fire" class="ctrl-btn fire-btn">FIRE</div>
        </div>
    </div>
</div>

<script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    let gameStarted = false, isEnding = false;
    let lastTime = performance.now();
    const interval = 1000 / 60;

    // Game Stats
    let score = 0, level = 1, isBossMode = false;
    let bullets = [], enemies = [], keys = {};
    let canShoot = true;

    const player = { x: 80, y: 150, w: 45, h: 60, speed: 8, anim: 0 };
    const boss = { active: false, hp: 50, maxHp: 50, x: 0, y: 0, dir: 1, w: 140, h: 140 };
    const question = { a: 0, b: 0, ans: 0 };

    // Audio Context
    const AudioCtx = window.AudioContext || window.webkitAudioContext;
    let audioCtx;

    function playSound(f, t='square', d=0.1, g=0.1) {
        if(!audioCtx) return;
        const o = audioCtx.createOscillator(); const gain = audioCtx.createGain();
        o.type = t; o.frequency.setValueAtTime(f, audioCtx.currentTime);
        gain.gain.setValueAtTime(g, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + d);
        o.connect(gain); gain.connect(audioCtx.destination);
        o.start(); o.stop(audioCtx.currentTime + d);
    }

    function playMusic() {
        if(!gameStarted || isEnding) return;
        playSound(isBossMode ? 60 : 45, 'sawtooth', 0.2, 0.07);
        setTimeout(playMusic, isBossMode ? 150 : 300);
    }

    function resize() { 
        canvas.width = window.innerWidth; 
        canvas.height = window.innerHeight * 0.65; 
    }
    window.addEventListener('resize', resize);
    resize();

    function generateQuestion() {
        const r = 5 + (level * 3);
        question.a = Math.floor(Math.random()*r)+1;
        question.b = Math.floor(Math.random()*r)+1;
        question.ans = question.a + question.b;
        document.getElementById('question').innerHTML = `TARGET: <span style="color:cyan">${question.a} + ${question.b}</span>`;
    }

    function shoot() {
        if(!gameStarted || isEnding || !canShoot) return;
        bullets.push({ x: player.x + 50, y: player.y + 25 + player.anim, vx: 18 });
        playSound(400, 'square', 0.1, 0.05);
        canShoot = false;
    }

    function update(dt) {
        const ratio = dt / (1000 / 60);
        player.anim = Math.sin(Date.now()/200)*6;

        if (keys['ArrowUp']) player.y -= player.speed * ratio;
        if (keys['ArrowDown']) player.y += player.speed * ratio;
        player.y = Math.max(0, Math.min(canvas.height - player.h, player.y));

        if (score >= level * 100 && !isBossMode) {
            isBossMode = true;
            document.getElementById('boss-warning').style.display = 'block';
            setTimeout(() => {
                document.getElementById('boss-warning').style.display = 'none';
                boss.active = true; boss.hp = 50; boss.x = canvas.width - 200;
                document.getElementById('boss-ui').style.display = 'block';
            }, 2000);
        }

        bullets.forEach((b, i) => {
            b.x += b.vx * ratio;
            if (boss.active && b.x > boss.x && b.y > boss.y && b.y < boss.y + boss.h) {
                bullets.splice(i, 1); boss.hp -= 2; generateQuestion();
                if (boss.hp <= 0) {
                    boss.active = false; isBossMode = false;
                    document.getElementById('boss-ui').style.display = 'none';
                    if (level >= 3) triggerEnding(); else { level++; score += 50; }
                }
            }
            if (b.x > canvas.width) bullets.splice(i, 1);
        });

        if (!isBossMode && !isEnding && Math.random() < 0.035) {
            enemies.push({ x: canvas.width, y: Math.random()*(canvas.height-60)+30, 
                val: Math.random() > 0.6 ? question.ans : Math.floor(Math.random()*50), 
                speed: (3 + level/2) * ratio });
        }

        enemies.forEach((e, i) => {
            e.x -= e.speed;
            bullets.forEach((b, bi) => {
                if (b.x > e.x && b.x < e.x+60 && b.y > e.y && b.y < e.y+60) {
                    if (e.val === question.ans) { score += 10; generateQuestion(); enemies = []; playSound(800, 'sine', 0.1); }
                    else { score = Math.max(0, score - 5); playSound(40, 'sawtooth', 0.3); }
                    bullets.splice(bi, 1); enemies.splice(i, 1);
                }
            });
            if (e.x < -60) enemies.splice(i, 1);
        });

        if (boss.active) {
            boss.y += 4 * boss.dir * ratio;
            if (boss.y <= 0 || boss.y >= canvas.height - boss.h) boss.dir *= -1;
            document.getElementById('boss-hp-fill').style.width = (boss.hp/boss.maxHp*100)+'%';
        }
        document.getElementById('score-val').innerText = score.toString().padStart(4, '0');
    }

    function draw() {
        ctx.fillStyle = '#000'; ctx.fillRect(0, 0, canvas.width, canvas.height);
        
        // Grid
        ctx.strokeStyle = '#0a0a0a';
        for(let i=0; i<canvas.width; i+=50) { ctx.beginPath(); ctx.moveTo(i,0); ctx.lineTo(i,canvas.height); ctx.stroke(); }

        // Player
        const py = player.y + player.anim;
        ctx.shadowBlur = 15; ctx.shadowColor = '#0ff';
        ctx.fillStyle = '#0ff'; ctx.fillRect(player.x, py, player.w, player.h);
        ctx.fillStyle = '#fff'; ctx.fillRect(player.x+32, py+12, 10, 5); // Eye
        ctx.shadowBlur = 0;

        // Enemies
        enemies.forEach(e => {
            ctx.fillStyle = '#001a1a'; ctx.strokeStyle = '#0f0'; ctx.lineWidth = 2;
            ctx.strokeRect(e.x, e.y, 55, 55); ctx.fillRect(e.x, e.y, 55, 55);
            ctx.fillStyle = '#fff'; ctx.font = 'bold 26px Arial'; ctx.textAlign = 'center';
            ctx.fillText(e.val, e.x + 27, e.y + 38);
        });

        ctx.fillStyle = '#ff0'; bullets.forEach(b => ctx.fillRect(b.x, b.y, 16, 6));

        if (boss.active) {
            ctx.shadowBlur = 20; ctx.shadowColor = 'red';
            ctx.fillStyle = '#f00'; ctx.fillRect(boss.x, boss.y, boss.w, boss.h);
            ctx.fillStyle = '#fff'; ctx.font = 'bold 50px Arial';
            ctx.fillText(question.ans, boss.x + boss.w/2, boss.y + boss.h/2 + 20);
            ctx.shadowBlur = 0;
        }
    }

    function triggerEnding() {
        isEnding = true;
        document.getElementById('ending-screen').style.display = 'flex';
        document.getElementById('final-stats').innerText = `DATA SECURED: ${score}`;
        document.getElementById('mobile-controls').style.display = 'none';
        document.getElementById('ui-overlay').style.display = 'none';
    }

    function gameLoop(now) {
        const dt = now - lastTime;
        if (dt >= interval) { update(dt); draw(); lastTime = now; }
        if (!isEnding) requestAnimationFrame(gameLoop);
    }

    document.getElementById('btn-start').onclick = () => {
        audioCtx = new AudioCtx();
        document.getElementById('start-screen').style.display = 'none';
        document.getElementById('ui-overlay').style.display = 'flex';
        document.getElementById('mobile-controls').style.display = 'flex';
        gameStarted = true; generateQuestion(); playMusic();
        requestAnimationFrame(gameLoop);
    };

    // --- FINAL FIXED CONTROLS (Multi-Touch Support) ---
    const inputMap = { 'btn-up': 'ArrowUp', 'btn-down': 'ArrowDown', 'btn-fire': 'Space' };
    
    Object.keys(inputMap).forEach(id => {
        const el = document.getElementById(id);
        const key = inputMap[id];
        
        el.addEventListener('pointerdown', e => {
            e.preventDefault();
            if(key === 'Space') { shoot(); } else { keys[key] = true; }
        });
        
        el.addEventListener('pointerup', e => {
            e.preventDefault();
            if(key === 'Space') { canShoot = true; } else { keys[key] = false; }
        });

        el.addEventListener('pointerleave', e => {
            if(key !== 'Space') keys[key] = false;
        });
    });

    // Keyboard
    window.addEventListener('keydown', e => { if(e.code === 'Space') shoot(); keys[e.code] = true; });
    window.addEventListener('keyup', e => { if(e.code === 'Space') canShoot = true; keys[e.code] = false; });
</script>

</body>
</html>

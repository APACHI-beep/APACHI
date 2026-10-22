<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>سفینه فضایی</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  html, body {
    width: 100%; height: 100%;
    background: #000; overflow: hidden;
    font-family: Tahoma, sans-serif;
    touch-action: none; user-select: none; -webkit-user-select: none;
  }
  #game { display: block; width: 100%; height: 100%; }
  #hud {
    position: fixed; top: 10px; left: 0; right: 0;
    padding: 0 20px; display: flex; justify-content: space-between;
    color: #0ff; text-shadow: 0 0 10px #0ff;
    font-size: 16px; font-weight: bold;
    pointer-events: none; z-index: 10;
  }
  #controls {
    position: fixed; bottom: 20px; left: 0; right: 0;
    padding: 0 20px; display: flex; justify-content: space-between;
    align-items: flex-end; z-index: 10; pointer-events: none;
  }
  .pad { display: flex; gap: 12px; pointer-events: auto; }
  .btn {
    width: 65px; height: 65px; border-radius: 50%;
    background: rgba(0,255,255,0.15); border: 2px solid #0ff;
    color: #0ff; font-size: 26px;
    display: flex; align-items: center; justify-content: center;
    box-shadow: 0 0 15px rgba(0,255,255,0.4), inset 0 0 15px rgba(0,255,255,0.2);
    cursor: pointer;
  }
  .btn.active { background: rgba(0,255,255,0.45); transform: scale(0.92); }
  #fire {
    width: 80px; height: 80px;
    background: rgba(255,80,80,0.15); border-color: #f66; color: #f66;
    box-shadow: 0 0 15px rgba(255,80,80,0.4), inset 0 0 15px rgba(255,80,80,0.2);
    font-size: 30px;
  }
  #fire.active { background: rgba(255,80,80,0.45); }
  #overlay {
    position: fixed; inset: 0;
    background: rgba(0,0,0,0.88);
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    text-align: center; z-index: 100; color: #fff; padding: 30px;
  }
  #overlay.hidden { display: none; }
  #overlay h1 { color: #0ff; font-size: 34px; margin-bottom: 15px; text-shadow: 0 0 25px #0ff; }
  #overlay p { color: #fff; font-size: 16px; margin-bottom: 25px; line-height: 1.8; }
  #startBtn {
    padding: 15px 45px; font-size: 20px; font-family: Tahoma, sans-serif;
    background: transparent; border: 2px solid #0ff; color: #0ff;
    border-radius: 50px; box-shadow: 0 0 20px rgba(0,255,255,0.5); cursor: pointer;
  }
  #startBtn:active { background: rgba(0,255,255,0.2); }
  .finalScore { color: #ff0; font-size: 24px; margin: 15px 0 25px; text-shadow: 0 0 15px #ff0; }
</style>
</head>
<body>

<canvas id="game"></canvas>

<div id="hud">
  <div>امتیاز: <span id="score">0</span></div>
  <div>جان: <span id="lives">3</span></div>
</div>

<div id="controls">
  <div class="pad">
    <div class="btn" id="left">◀</div>
    <div class="btn" id="right">▶</div>
  </div>
  <div class="pad">
    <div class="btn" id="fire">🔥</div>
  </div>
</div>

<div id="overlay">
  <h1>🚀 سفینه فضایی</h1>
  <p>با دکمه‌ها حرکت کن و شلیک کن<br>از برخورد با دشمن‌ها دوری کن!</p>
  <button id="startBtn">شروع بازی</button>
</div>

<script>
(function() {
  "use strict";

  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');
  let W = 0, H = 0;

  const ship = { x: 0, y: 0, speed: 7 };
  const stars = [];
  let lasers = [];
  let enemies = [];
  let particles = [];
  let score = 0;
  let lives = 3;
  let running = false;
  let lastTime = 0;
  let enemyTimer = 0;
  let shootTimer = 0;

  const input = { left: false, right: false, fire: false };

  function resize() {
    W = canvas.width = window.innerWidth;
    H = canvas.height = window.innerHeight;
    ship.y = H - 110;
  }

  function bindButton(id, key) {
    const el = document.getElementById(id);
    const press = e => { e.preventDefault(); input[key] = true; el.classList.add('active'); };
    const release = e => { if (e) e.preventDefault(); input[key] = false; el.classList.remove('active'); };
    el.addEventListener('touchstart', press, { passive: false });
    el.addEventListener('touchend', release, { passive: false });
    el.addEventListener('touchcancel', release, { passive: false });
    el.addEventListener('mousedown', press);
    el.addEventListener('mouseup', release);
    el.addEventListener('mouseleave', release);
  }
  bindButton('left', 'left');
  bindButton('right', 'right');
  bindButton('fire', 'fire');

  document.addEventListener('keydown', e => {
    if (e.key === 'ArrowLeft' || e.key === 'a') input.left = true;
    if (e.key === 'ArrowRight' || e.key === 'd') input.right = true;
    if (e.key === ' ' || e.key === 'ArrowUp') { input.fire = true; e.preventDefault(); }
  });
  document.addEventListener('keyup', e => {
    if (e.key === 'ArrowLeft' || e.key === 'a') input.left = false;
    if (e.key === 'ArrowRight' || e.key === 'd') input.right = false;
    if (e.key === ' ' || e.key === 'ArrowUp') input.fire = false;
  });

  function initStars() {
    stars.length = 0;
    for (let i = 0; i < 120; i++) {
      stars.push({
        x: Math.random() * W, y: Math.random() * H,
        speed: Math.random() * 2.5 + 0.5,
        size: Math.random() * 1.8 + 0.4
      });
    }
  }

  function explode(x, y, color) {
    for (let i = 0; i < 15; i++) {
      const angle = Math.random() * Math.PI * 2;
      const spd = Math.random() * 5 + 1;
      particles.push({
        x, y,
        vx: Math.cos(angle) * spd, vy: Math.sin(angle) * spd,
        life: 1, color
      });
    }
  }

  function spawnEnemy() {
    const r = 16 + Math.random() * 10;
    enemies.push({
      x: Math.random() * (W - r * 2) + r,
      y: -r * 2, r,
      speed: 1.5 + Math.random() * 1.5 + score / 300,
      wobble: Math.random() * Math.PI * 2,
      hue: 280 + Math.random() * 60
    });
  }

  function update(dt) {
    if (input.left) ship.x -= ship.speed;
    if (input.right) ship.x += ship.speed;
    ship.x = Math.max(30, Math.min(W - 30, ship.x));
    ship.y = H - 110;

    shootTimer += dt;
    if (input.fire && shootTimer > 200) {
      shootTimer = 0;
      lasers.push({ x: ship.x, y: ship.y - 30 });
    }

    for (let i = lasers.length - 1; i >= 0; i--) {
      lasers[i].y -= 13;
      if (lasers[i].y < -20) lasers.splice(i, 1);
    }

    enemyTimer += dt;
    if (enemyTimer > 900) { enemyTimer = 0; spawnEnemy(); }

    for (let i = enemies.length - 1; i >= 0; i--) {
      const e = enemies[i];
      e.y += e.speed;
      e.wobble += 0.04;
      e.x += Math.sin(e.wobble) * 0.5;

      const dx = e.x - ship.x, dy = e.y - ship.y;
      if (Math.sqrt(dx*dx + dy*dy) < e.r + 18) {
        explode(e.x, e.y, '#f44');
        enemies.splice(i, 1);
        loseLife();
        continue;
      }
      if (e.y > H + 50) { enemies.splice(i, 1); loseLife(); }
    }

    for (let i = enemies.length - 1; i >= 0; i--) {
      const e = enemies[i];
      for (let j = lasers.length - 1; j >= 0; j--) {
        const l = lasers[j];
        const dx = e.x - l.x, dy = e.y - l.y;
        if (Math.sqrt(dx*dx + dy*dy) < e.r + 6) {
          explode(e.x, e.y, 'hsl(' + e.hue + ',100%,60%)');
          enemies.splice(i, 1);
          lasers.splice(j, 1);
          score += 10;
          document.getElementById('score').textContent = score;
          break;
        }
      }
    }

    for (let i = particles.length - 1; i >= 0; i--) {
      const p = particles[i];
      p.x += p.vx; p.y += p.vy;
      p.life -= 0.03;
      if (p.life <= 0) particles.splice(i, 1);
    }

    for (const s of stars) {
      s.y += s.speed;
      if (s.y > H) { s.y = 0; s.x = Math.random() * W; }
    }
  }

  function loseLife() {
    lives--;
    document.getElementById('lives').textContent = Math.max(0, lives);
    if (navigator.vibrate) navigator.vibrate(80);
    if (lives <= 0) gameOver();
  }

  function draw() {
    ctx.fillStyle = '#000';
    ctx.fillRect(0, 0, W, H);

    for (const s of stars) {
      ctx.fillStyle = 'rgba(255,255,255,' + (0.3 + s.speed / 4) + ')';
      ctx.fillRect(s.x, s.y, s.size, s.size);
    }

    ctx.shadowBlur = 12;
    ctx.shadowColor = '#0ff';
    ctx.fillStyle = '#0ff';
    for (const l of lasers) {
      ctx.fillRect(l.x - 2, l.y - 12, 4, 22);
    }

    for (const e of enemies) {
      ctx.shadowBlur = 12;
      ctx.shadowColor = 'hsl(' + e.hue + ',100%,60%)';
      ctx.fillStyle = 'hsl(' + e.hue + ',100%,60%)';
      ctx.beginPath();
      ctx.arc(e.x, e.y, e.r, 0, Math.PI * 2);
      ctx.fill();
      ctx.shadowBlur = 0;
      ctx.fillStyle = '#000';
      ctx.beginPath();
      ctx.arc(e.x - e.r * 0.3, e.y - e.r * 0.1, e.r * 0.15, 0, Math.PI * 2);
      ctx.arc(e.x + e.r * 0.3, e.y - e.r * 0.1, e.r * 0.15, 0, Math.PI * 2);
      ctx.fill();
    }

    if (running || lives > 0) {
      ctx.save();
      ctx.translate(ship.x, ship.y);

      ctx.shadowBlur = 15;
      ctx.shadowColor = '#f80';
      ctx.fillStyle = '#f80';
      ctx.beginPath();
      ctx.moveTo(-7, 18);
      ctx.lineTo(0, 34 + Math.random() * 10);
      ctx.lineTo(7, 18);
      ctx.closePath();
      ctx.fill();

      ctx.shadowBlur = 18;
      ctx.shadowColor = '#0ff';
      ctx.fillStyle = '#0ff';
      ctx.beginPath();
      ctx.moveTo(0, -28);
      ctx.lineTo(18, 18);
      ctx.lineTo(0, 10);
      ctx.lineTo(-18, 18);
      ctx.closePath();
      ctx.fill();

      ctx.shadowBlur = 8;
      ctx.shadowColor = '#fff';
      ctx.fillStyle = '#fff';
      ctx.beginPath();
      ctx.arc(0, -4, 5, 0, Math.PI * 2);
      ctx.fill();

      ctx.restore();
    }

    ctx.shadowBlur = 0;
    for (const p of particles) {
      ctx.globalAlpha = Math.max(0, p.life);
      ctx.fillStyle = p.color;
      ctx.fillRect(p.x - 2, p.y - 2, 4, 4);
    }
    ctx.globalAlpha = 1;
  }

  function loop(time) {
    if (!running) return;
    const dt = lastTime ? time - lastTime : 16;
    lastTime = time;
    update(dt);
    draw();
    requestAnimationFrame(loop);
  }

  function startGame() {
    score = 0; lives = 3;
    lasers = []; enemies = []; particles = [];
    enemyTimer = 0; shootTimer = 0; lastTime = 0;
    ship.x = W / 2; ship.y = H - 110;
    document.getElementById('score').textContent = '0';
    document.getElementById('lives').textContent = '3';
    document.getElementById('overlay').classList.add('hidden');
    running = true;
    initStars();
    requestAnimationFrame(loop);
  }

  function gameOver() {
    running = false;
    const overlay = document.getElementById('overlay');
    overlay.innerHTML =
      '<h1>💥 Game Over</h1>' +
      '<div class="finalScore">امتیاز نهایی: ' + score + '</div>' +
      '<button id="startBtn">دوباره بازی کن</button>';
    overlay.classList.remove('hidden');
    document.getElementById('startBtn').addEventListener('click', startGame);
  }

  resize();
  window.addEventListener('resize', resize);
  window.addEventListener('orientationchange', () => setTimeout(resize, 300));

  ship.x = W / 2;
  ship.y = H - 110;
  initStars();
  draw();

  document.getElementById('startBtn').addEventListener('click', startGame);
  document.addEventListener('gesturestart', e => e.preventDefault());

})();
</script>
</body>
</html>

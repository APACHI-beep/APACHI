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
  #hud .right { text-align: right; }
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
  #overlay p { color: #fff; font-size: 16px; margin-bottom: 20px; line-height: 1.8; }
  #startBtn {
    padding: 15px 45px; font-size: 20px; font-family: Tahoma, sans-serif;
    background: transparent; border: 2px solid #0ff; color: #0ff;
    border-radius: 50px; box-shadow: 0 0 20px rgba(0,255,255,0.5); cursor: pointer;
  }
  #startBtn:active { background: rgba(0,255,255,0.2); }
  .finalScore { color: #ff0; font-size: 22px; margin: 10px 0; text-shadow: 0 0 15px #ff0; }
  .recordLine { color: #0ff; font-size: 20px; margin: 5px 0 20px; text-shadow: 0 0 15px #0ff; }
  .newRecord {
    color: #ff0; font-size: 20px; margin: 10px 0;
    animation: pulse 0.8s infinite alternate;
  }
  @keyframes pulse {
    from { transform: scale(1); text-shadow: 0 0 10px #ff0; }
    to { transform: scale(1.15); text-shadow: 0 0 30px #ff0; }
  }
  #resetRecord {
    margin-top: 15px;
    padding: 8px 20px;
    font-size: 13px;
    font-family: Tahoma, sans-serif;
    background: transparent;
    border: 1px solid #555;
    color: #888;
    border-radius: 20px;
    cursor: pointer;
  }
</style>
</head>
<body>

<canvas id="game"></canvas>

<div id="hud">
  <div>امتیاز: <span id="score">0</span></div>
  <div class="right">رکورد: <span id="highScore">0</span><br>جان: <span id="lives">3</span></div>
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
  <div class="recordLine" id="startRecord">🏆 رکورد فعلی: 0</div>
  <button id="startBtn">شروع بازی</button>
</div>

<script>
(function() {
  "use strict";

  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');
  let W = 0, H = 0;

  // ========== ذخیره رکورد ==========
  const RECORD_KEY = 'spaceship_highscore_v1';
  function getHighScore() {
    try {
      const v = localStorage.getItem(RECORD_KEY);
      return v ? parseInt(v, 10) || 0 : 0;
    } catch (e) {
      return 0;
    }
  }
  function setHighScore(v) {
    try {
      localStorage.setItem(RECORD_KEY, String(v));
    } catch (e) {}
  }
  function resetHighScore() {
    try { localStorage.removeItem(RECORD_KEY); } catch (e) {}
  }

  const ship = { x: 0, y: 0, speed: 7 };
  const stars = [];
  let lasers = [];
  let enemies = [];
  let particles = [];
  let score = 0;
  let lives = 3;
  let highScore = getHighScore();
  let running = false;
  let lastTime = 0;
  let enemyTimer = 0;
  let shootTimer = 0;

  const input = { left: false, right: false, fire: false };

  // نمایش رکورد اولیه در HUD و صفحه شروع
  document.getElementById('highScore').textContent = highScore;
  document.getElementById('startRecord').textContent = '🏆 رکورد فعلی: ' + highScore;

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

          // آپدیت زنده رکورد در HUD اگه رکورد شکسته
          if (score > highScore) {
            highScore = score;
            setHighScore(highScore);
            document.getElementById('highScore').textContent = highScore;
          }
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
    document.getElementById('highScore').textContent = highScore;
    document.getElementById('overlay').classList.add('hidden');
    running = true;
    initStars();
    requestAnimationFrame(loop);
  }

  function gameOver() {
    running = false;
    const isNewRecord = score > 0 && score >= highScore && score > 0;
    if (score > highScore) {
      highScore = score;
      setHighScore(highScore);
    }
    document.getElementById('highScore').textContent = highScore;

    const overlay = document.getElementById('overlay');
    overlay.innerHTML =
      '<h1>💥 Game Over</h1>' +
      '<div class="finalScore">امتیاز شما: ' + score + '</div>' +
      (isNewRecord
        ? '<div class="newRecord">🎉 رکورد جدید!</div>'
        : '<div class="recordLine">🏆 رکورد: ' + highScore + '</div>') +
      '<button id="startBtn">دوباره بازی کن</button>' +
      '<button id="resetRecord">پاک کردن رکورد</button>';
    overlay.classList.remove('hidden');

    document.getElementById('startBtn').addEventListener('click', startGame);
    document.getElementById('resetRecord').addEventListener('click', function() {
      if (confirm('مطمئنی می‌خوای رکورد رو پاک کنی؟')) {
        resetHighScore();
        highScore = 0;
        document.getElementById('highScore').textContent = '0';
        document.getElementById('startRecord').textContent = '🏆 رکورد فعلی: 0';
        // برگرد به صفحه شروع
        overlay.innerHTML =
          '<h1>🚀 سفینه فضایی</h1>' +
          '<p>با دکمه‌ها حرکت کن و شلیک کن<br>از برخورد با دشمن‌ها دوری کن!</p>' +
          '<div class="recordLine" id="startRecord">🏆 رکورد فعلی: 0</div>' +
          '<button id="startBtn">شروع بازی</button>';
        document.getElementById('startBtn').addEventListener('click', startGame);
      }
    });
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
</html><!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>رکورد</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  html, body {
    width: 100%; height: 100%;
    background: #0a0a1a;
    font-family: Tahoma, sans-serif;
    color: #fff;
    overflow: hidden;
  }
  body {
    display: flex;
    flex-direction: column;
  }

  /* ===== تب‌ها ===== */
  .tabs {
    display: flex;
    background: #14142a;
    border-bottom: 2px solid #0ff;
    flex-shrink: 0;
  }
  .tab {
    flex: 1;
    padding: 16px;
    text-align: center;
    font-size: 16px;
    font-weight: bold;
    color: #666;
    cursor: pointer;
    transition: all 0.2s;
    border-bottom: 3px solid transparent;
  }
  .tab.active {
    color: #0ff;
    border-bottom-color: #0ff;
    background: #1a1a35;
    text-shadow: 0 0 10px #0ff;
  }

  /* ===== محتوا ===== */
  .page {
    flex: 1;
    overflow-y: auto;
    padding: 20px;
    display: none;
  }
  .page.active { display: block; }

  /* ===== صفحه گیم ===== */
  .box {
    max-width: 500px;
    margin: 0 auto;
    background: #14142a;
    border: 1px solid #0ff4;
    border-radius: 15px;
    padding: 25px;
    box-shadow: 0 0 20px #0ff2;
  }
  .box h2 {
    color: #0ff;
    text-align: center;
    margin-bottom: 20px;
    font-size: 22px;
    text-shadow: 0 0 15px #0ff;
  }
  .field {
    margin-bottom: 18px;
  }
  .field label {
    display: block;
    font-size: 14px;
    margin-bottom: 8px;
    color: #aaa;
  }
  .field input {
    width: 100%;
    padding: 14px;
    background: #0a0a1a;
    border: 2px solid #0ff4;
    border-radius: 10px;
    color: #fff;
    font-size: 16px;
    font-family: Tahoma, sans-serif;
    outline: none;
    text-align: center;
  }
  .field input:focus {
    border-color: #0ff;
    box-shadow: 0 0 15px #0ff4;
  }
  .field input::placeholder { color: #555; }

  .btn {
    width: 100%;
    padding: 16px;
    background: #0ff;
    border: none;
    border-radius: 10px;
    color: #000;
    font-size: 18px;
    font-weight: bold;
    font-family: Tahoma, sans-serif;
    cursor: pointer;
    box-shadow: 0 0 20px #0ff6;
    transition: transform 0.1s;
  }
  .btn:active { transform: scale(0.97); }

  .msg {
    margin-top: 15px;
    padding: 12px;
    border-radius: 10px;
    text-align: center;
    font-size: 15px;
    display: none;
  }
  .msg.ok { display: block; background: #0f04; color: #0f0; border: 1px solid #0f06; }
  .msg.err { display: block; background: #f004; color: #f66; border: 1px solid #f606; }

  /* ===== لیدربورد ===== */
  .leaderboard {
    max-width: 500px;
    margin: 0 auto;
  }
  .leaderboard h2 {
    color: #ff0;
    text-align: center;
    margin-bottom: 20px;
    font-size: 24px;
    text-shadow: 0 0 15px #ff0;
  }
  .record-item {
    display: flex;
    align-items: center;
    background: #14142a;
    border: 1px solid #ff02;
    border-radius: 12px;
    padding: 14px 18px;
    margin-bottom: 10px;
  }
  .record-item.rank-1 { border-color: #ffd700; box-shadow: 0 0 15px #ffd70055; background: #1f1f10; }
  .record-item.rank-2 { border-color: #c0c0c0; background: #1f1f1f; }
  .record-item.rank-3 { border-color: #cd7f32; background: #1f150d; }

  .rank {
    font-size: 22px;
    font-weight: bold;
    width: 45px;
    text-align: center;
    color: #888;
    flex-shrink: 0;
  }
  .rank-1 .rank { color: #ffd700; text-shadow: 0 0 10px #ffd700; }
  .rank-2 .rank { color: #c0c0c0; }
  .rank-3 .rank { color: #cd7f32; }

  .name {
    flex: 1;
    font-size: 16px;
    padding: 0 12px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .points {
    font-size: 18px;
    font-weight: bold;
    color: #0ff;
    text-shadow: 0 0 10px #0ff;
    flex-shrink: 0;
  }

  .empty {
    text-align: center;
    color: #666;
    padding: 40px 20px;
    font-size: 16px;
    line-height: 1.8;
  }

  .clear-btn {
    display: block;
    margin: 20px auto 0;
    padding: 10px 25px;
    background: transparent;
    border: 1px solid #f66;
    color: #f66;
    border-radius: 25px;
    font-size: 13px;
    font-family: Tahoma, sans-serif;
    cursor: pointer;
  }
  .clear-btn:active { background: #f662; }

  @media (max-width: 400px) {
    .tab { font-size: 14px; padding: 14px; }
    .box { padding: 18px; }
  }
</style>
</head>
<body>

<!-- تب‌ها -->
<div class="tabs">
  <div class="tab active" data-tab="game">🎮 گیم</div>
  <div class="tab" data-tab="leaderboard">🏆 لیدربورد</div>
</div>

<!-- تب گیم -->
<div class="page active" id="page-game">
  <div class="box">
    <h2>ثبت رکورد</h2>
    <div class="field">
      <label>اسمت چیه؟</label>
      <input type="text" id="nameInput" placeholder="اسمت رو بنویس..." maxlength="20" autocomplete="off">
    </div>
    <div class="field">
      <label>امتیازت چقدره؟</label>
      <input type="number" id="scoreInput" placeholder="مثلاً 850" min="1" max="999999" inputmode="numeric">
    </div>
    <button class="btn" id="submitBtn">ثبت رکورد</button>
    <div class="msg" id="msg"></div>
  </div>
</div>

<!-- تب لیدربورد -->
<div class="page" id="page-leaderboard">
  <div class="leaderboard">
    <h2>🏆 ۱۰ نفر برتر</h2>
    <div id="list"></div>
    <button class="clear-btn" id="clearBtn">پاک کردن همه رکوردها</button>
  </div>
</div>

<script>
(function() {
  "use strict";

  const STORAGE_KEY = 'top10_leaderboard_v1';
  const MAX_RECORDS = 10;

  // ===== خواندن و ذخیره =====
  function loadRecords() {
    try {
      const raw = localStorage.getItem(STORAGE_KEY);
      if (!raw) return [];
      const arr = JSON.parse(raw);
      return Array.isArray(arr) ? arr : [];
    } catch (e) {
      return [];
    }
  }

  function saveRecords(records) {
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(records));
    } catch (e) {}
  }

  // ===== تب‌ها =====
  const tabs = document.querySelectorAll('.tab');
  const pages = document.querySelectorAll('.page');

  tabs.forEach(tab => {
    tab.addEventListener('click', () => {
      const target = tab.dataset.tab;
      tabs.forEach(t => t.classList.toggle('active', t === tab));
      pages.forEach(p => {
        p.classList.toggle('active', p.id === 'page-' + target);
      });
      if (target === 'leaderboard') renderLeaderboard();
    });
  });

  // ===== ثبت رکورد =====
  const nameInput = document.getElementById('nameInput');
  const scoreInput = document.getElementById('scoreInput');
  const submitBtn = document.getElementById('submitBtn');
  const msg = document.getElementById('msg');

  function showMsg(text, type) {
    msg.textContent = text;
    msg.className = 'msg ' + type;
    clearTimeout(showMsg._t);
    showMsg._t = setTimeout(() => {
      msg.className = 'msg';
    }, 3000);
  }

  submitBtn.addEventListener('click', () => {
    const name = nameInput.value.trim();
    const score = parseInt(scoreInput.value, 10);

    if (!name) {
      showMsg('❌ اسمت رو وارد کن!', 'err');
      nameInput.focus();
      return;
    }
    if (name.length > 20) {
      showMsg('❌ اسم حداکثر ۲۰ حرف!', 'err');
      return;
    }
    if (isNaN(score) || score < 1) {
      showMsg('❌ یه امتیاز معتبر وارد کن!', 'err');
      scoreInput.focus();
      return;
    }

    let records = loadRecords();
    records.push({
      name: name,
      score: score,
      time: Date.now()
    });

    // مرتب‌سازی نزولی بر اساس امتیاز
    records.sort((a, b) => b.score - a.score);
    // فقط ۱۰ نفر اول
    records = records.slice(0, MAX_RECORDS);
    saveRecords(records);

    // بررسی آیا این رکورد جزو ۱۰ نفر برتره
    const isInTop = records.some(r => r.name === name && r.score === score && r.time === records.find(x => x.name === name && x.score === score)?.time);
    const rank = records.findIndex(r => r.name === name && r.score === score);

    if (rank !== -1) {
      showMsg('🎉 رکوردت ثبت شد! رتبه ' + (rank + 1), 'ok');
    } else {
      showMsg('😕 امتیازت به ۱۰ نفر برتر نرسید!', 'err');
    }

    // پاک کردن فرم
    nameInput.value = '';
    scoreInput.value = '';

    // بعد از ۱.۵ ثانیه برو به لیدربورد
    setTimeout(() => {
      document.querySelector('.tab[data-tab="leaderboard"]').click();
    }, 1200);
  });

  // Enter برای ثبت
  scoreInput.addEventListener('keydown', e => {
    if (e.key === 'Enter') submitBtn.click();
  });

  // ===== نمایش لیدربورد =====
  const list = document.getElementById('list');

  function renderLeaderboard() {
    const records = loadRecords();

    if (records.length === 0) {
      list.innerHTML = '<div class="empty">هنوز هیچ رکوردی ثبت نشده<br>برو به تب گیم و اولین رکورد رو بزن! 🎮</div>';
      return;
    }

    let html = '';
    records.forEach((r, i) => {
      const rank = i + 1;
      const medal = rank === 1 ? '🥇' : rank === 2 ? '🥈' : rank === 3 ? '🥉' : rank;
      const cls = rank <= 3 ? 'rank-' + rank : '';
      html +=
        '<div class="record-item ' + cls + '">' +
          '<div class="rank">' + medal + '</div>' +
          '<div class="name">' + escapeHtml(r.name) + '</div>' +
          '<div class="points">' + r.score + '</div>' +
        '</div>';
    });
    list.innerHTML = html;
  }

  function escapeHtml(str) {
    return String(str)
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#39;');
  }

  // ===== پاک کردن همه =====
  document.getElementById('clearBtn').addEventListener('click', () => {
    if (confirm('مطمئنی می‌خوای همه رکوردها رو پاک کنی؟')) {
      try { localStorage.removeItem(STORAGE_KEY); } catch (e) {}
      renderLeaderboard();
    }
  });

  // ===== شروع =====
  renderLeaderboard();

})();
</script>
</body>
</html>

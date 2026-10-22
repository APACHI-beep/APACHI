<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>🚀 سفینه فضایی</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  html, body {
    width: 100%; height: 100%;
    background: #05050f;
    font-family: Tahoma, sans-serif;
    color: #fff;
    overflow: hidden;
    touch-action: none;
    user-select: none; -webkit-user-select: none;
  }
  body { display: flex; flex-direction: column; }

  /* ===== تب‌ها ===== */
  .tabs {
    display: flex;
    background: #0d0d1f;
    border-bottom: 2px solid #0ff;
    flex-shrink: 0;
    box-shadow: 0 0 20px #0ff4;
    z-index: 50;
    position: relative;
  }
  .tab {
    flex: 1;
    padding: 14px;
    text-align: center;
    font-size: 15px;
    font-weight: bold;
    color: #555;
    cursor: pointer;
    transition: all 0.2s;
    border-bottom: 3px solid transparent;
  }
  .tab.active {
    color: #0ff;
    border-bottom-color: #0ff;
    background: #16162e;
    text-shadow: 0 0 10px #0ff;
  }

  /* ===== محتوا ===== */
  .page {
    flex: 1;
    position: relative;
    overflow: hidden;
    display: none;
  }
  .page.active { display: block; }
  #page-game { position: relative; }

  /* ===== HUD بازی ===== */
  #hud {
    position: absolute;
    top: 10px; left: 0; right: 0;
    padding: 0 20px;
    display: flex; justify-content: space-between;
    color: #0ff; text-shadow: 0 0 10px #0ff;
    font-size: 15px; font-weight: bold;
    pointer-events: none;
    z-index: 20;
  }
  #hud .right { text-align: right; }

  /* ===== کنترل‌ها ===== */
  #controls {
    position: absolute;
    bottom: 20px; left: 0; right: 0;
    padding: 0 20px;
    display: flex; justify-content: space-between;
    align-items: flex-end;
    z-index: 20;
    pointer-events: none;
  }
  .pad { display: flex; gap: 12px; pointer-events: auto; }
  .btn-ctrl {
    width: 65px; height: 65px; border-radius: 50%;
    background: rgba(0,255,255,0.12);
    border: 2px solid #0ff;
    color: #0ff; font-size: 26px;
    display: flex; align-items: center; justify-content: center;
    box-shadow: 0 0 15px rgba(0,255,255,0.4), inset 0 0 15px rgba(0,255,255,0.15);
    cursor: pointer;
    transition: transform 0.08s, background 0.08s;
    backdrop-filter: blur(5px);
  }
  .btn-ctrl.active { background: rgba(0,255,255,0.45); transform: scale(0.92); }
  #fire {
    width: 80px; height: 80px;
    background: rgba(255,80,80,0.12);
    border-color: #f66; color: #f66;
    box-shadow: 0 0 15px rgba(255,80,80,0.4), inset 0 0 15px rgba(255,80,80,0.15);
    font-size: 30px;
  }
  #fire.active { background: rgba(255,80,80,0.45); }

  /* ===== Overlay ===== */
  .overlay {
    position: absolute; inset: 0;
    background: rgba(5,5,15,0.92);
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    text-align: center; z-index: 40; padding: 25px;
    backdrop-filter: blur(8px);
  }
  .overlay.hidden { display: none; }
  .overlay h1 {
    color: #0ff; font-size: 30px; margin-bottom: 12px;
    text-shadow: 0 0 25px #0ff;
  }
  .overlay p {
    color: #ccc; font-size: 14px; margin-bottom: 20px;
    line-height: 1.8;
  }
  .overlay input {
    width: 100%; max-width: 280px;
    padding: 13px;
    background: #0a0a1a;
    border: 2px solid #0ff4;
    border-radius: 10px;
    color: #fff; font-size: 16px;
    font-family: Tahoma, sans-serif;
    outline: none; text-align: center;
    margin-bottom: 15px;
  }
  .overlay input:focus { border-color: #0ff; box-shadow: 0 0 15px #0ff4; }
  .overlay input::placeholder { color: #555; }
  .overlay .btn {
    padding: 14px 40px; font-size: 17px; font-weight: bold;
    font-family: Tahoma, sans-serif;
    background: linear-gradient(135deg, #0ff, #0af);
    border: none; border-radius: 50px;
    color: #000; cursor: pointer;
    box-shadow: 0 0 25px #0ff8;
    transition: transform 0.1s;
  }
  .overlay .btn:active { transform: scale(0.95); }
  .finalScore { color: #ff0; font-size: 22px; margin: 10px 0; text-shadow: 0 0 15px #ff0; }
  .recordLine { color: #0ff; font-size: 16px; margin: 5px 0 15px; text-shadow: 0 0 10px #0ff; }
  .newRecord {
    color: #ff0; font-size: 18px; margin: 8px 0;
    animation: pulse 0.7s infinite alternate;
  }
  @keyframes pulse {
    from { transform: scale(1); text-shadow: 0 0 10px #ff0; }
    to { transform: scale(1.12); text-shadow: 0 0 30px #ff0; }
  }

  /* ===== لیدربورد ===== */
  #page-leaderboard {
    overflow-y: auto;
    padding: 20px;
  }
  .leaderboard { max-width: 500px; margin: 0 auto; }
  .leaderboard h2 {
    color: #ff0; text-align: center; margin-bottom: 18px;
    font-size: 22px; text-shadow: 0 0 15px #ff0;
  }
  .record-item {
    display: flex; align-items: center;
    background: #14142a;
    border: 1px solid #ff02;
    border-radius: 12px;
    padding: 12px 16px;
    margin-bottom: 9px;
  }
  .record-item.rank-1 { border-color: #ffd700; box-shadow: 0 0 15px #ffd70055; background: #1f1f10; }
  .record-item.rank-2 { border-color: #c0c0c0; background: #1f1f1f; }
  .record-item.rank-3 { border-color: #cd7f32; background: #1f150d; }
  .rank {
    font-size: 20px; font-weight: bold; width: 42px;
    text-align: center; color: #888; flex-shrink: 0;
  }
  .rank-1 .rank { color: #ffd700; text-shadow: 0 0 10px #ffd700; }
  .rank-2 .rank { color: #c0c0c0; }
  .rank-3 .rank { color: #cd7f32; }
  .name {
    flex: 1; font-size: 15px; padding: 0 10px;
    overflow: hidden; text-overflow: ellipsis; white-space: nowrap;
  }
  .points {
    font-size: 17px; font-weight: bold;
    color: #0ff; text-shadow: 0 0 10px #0ff;
    flex-shrink: 0;
  }
  .empty {
    text-align: center; color: #666;
    padding: 40px 20px; font-size: 15px; line-height: 1.8;
  }
  .clear-btn {
    display: block; margin: 20px auto 30px;
    padding: 10px 25px;
    background: transparent; border: 1px solid #f66;
    color: #f66; border-radius: 25px;
    font-size: 13px; font-family: Tahoma, sans-serif;
    cursor: pointer;
  }
  .clear-btn:active { background: #f662; }

  #gameCanvas { display: block; width: 100%; height: 100%; position: absolute; inset: 0; }
</style>
</head>
<body>

<!-- تب‌ها -->
<div class="tabs">
  <div class="tab active" data-tab="game">🎮 بازی</div>
  <div class="tab" data-tab="leaderboard">🏆 لیدربورد</div>
</div>

<!-- تب بازی -->
<div class="page active" id="page-game">
  <canvas id="gameCanvas"></canvas>

  <div id="hud">
    <div>امتیاز: <span id="score">0</span></div>
    <div class="right">رکورد: <span id="highScore">0</span><br>جان: <span id="lives">3</span></div>
  </div>

  <div id="controls">
    <div class="pad">
      <div class="btn-ctrl" id="btnLeft">◀</div>
      <div class="btn-ctrl" id="btnRight">▶</div>
    </div>
    <div class="pad">
      <div class="btn-ctrl" id="fire">🔥</div>
    </div>
  </div>

  <div class="overlay" id="startOverlay">
    <h1>🚀 سفینه فضایی</h1>
    <p>با دکمه‌ها حرکت کن و شلیک کن<br>از برخورد با دشمن‌ها دوری کن!</p>
    <div class="recordLine" id="startRecord">🏆 رکورد: 0</div>
    <button class="btn" id="startBtn">شروع بازی</button>
  </div>

  <div class="overlay hidden" id="gameOverOverlay">
    <h1>💥 Game Over</h1>
    <div class="finalScore" id="finalScore">امتیاز: 0</div>
    <div id="recordStatus"></div>
    <input type="text" id="playerName" placeholder="اسمت رو بنویس..." maxlength="20" autocomplete="off">
    <button class="btn" id="submitScoreBtn">ثبت رکورد</button>
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

  // ========== ذخیره‌سازی ==========
  const LEADERBOARD_KEY = 'spaceship_leaderboard_v1';
  const HIGHSCORE_KEY = 'spaceship_highscore_v1';

  function loadLeaderboard() {
    try {
      const raw = localStorage.getItem(LEADERBOARD_KEY);
      if (!raw) return [];
      const arr = JSON.parse(raw);
      return Array.isArray(arr) ? arr : [];
    } catch (e) { return []; }
  }
  function saveLeaderboard(arr) {
    try { localStorage.setItem(LEADERBOARD_KEY, JSON.stringify(arr)); } catch (e) {}
  }
  function getHighScore() {
    try {
      const v = localStorage.getItem(HIGHSCORE_KEY);
      return v ? parseInt(v, 10) || 0 : 0;
    } catch (e) { return 0; }
  }
  function setHighScore(v) {
    try { localStorage.setItem(HIGHSCORE_KEY, String(v)); } catch (e) {}
  }

  // ========== تب‌ها ==========
  const tabs = document.querySelectorAll('.tab');
  const pages = document.querySelectorAll('.page');

  tabs.forEach(tab => {
    tab.addEventListener('click', () => {
      const target = tab.dataset.tab;
      tabs.forEach(t => t.classList.toggle('active', t === tab));
      pages.forEach(p => p.classList.toggle('active', p.id === 'page-' + target));
      if (target === 'leaderboard') renderLeaderboard();
      if (target === 'game') setTimeout(resizeCanvas, 50);
    });
  });

  // ========== Canvas ==========
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');
  let W = 0, H = 0;

  function resizeCanvas() {
    const rect = canvas.getBoundingClientRect();
    W = canvas.width = rect.width || window.innerWidth;
    H = canvas.height = rect.height || (window.innerHeight - 60);
    if (ship) ship.y = H - 110;
    if (stars.length) initStars();
  }

  // ========== وضعیت بازی ==========
  const ship = { x: 0, y: 0, speed: 7 };
  const stars = [];
  const nebulaStars = [];
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
  let screenShake = 0;

  const input = { left: false, right: false, fire: false };

  document.getElementById('highScore').textContent = highScore;
  document.getElementById('startRecord').textContent = '🏆 رکورد: ' + highScore;

  // ========== ستاره‌ها ==========
  function initStars() {
    stars.length = 0;
    nebulaStars.length = 0;
    for (let i = 0; i < 130; i++) {
      stars.push({
        x: Math.random() * W,
        y: Math.random() * H,
        speed: Math.random() * 2.5 + 0.4,
        size: Math.random() * 1.8 + 0.4,
        twinkle: Math.random() * Math.PI * 2
      });
    }
    for (let i = 0; i < 8; i++) {
      nebulaStars.push({
        x: Math.random() * W,
        y: Math.random() * H,
        r: Math.random() * 150 + 80,
        hue: Math.random() * 360,
        speed: Math.random() * 0.3 + 0.1
      });
    }
  }

  // ========== ذرات ==========
  function explode(x, y, color, count) {
    count = count || 18;
    for (let i = 0; i < count; i++) {
      const angle = Math.random() * Math.PI * 2;
      const spd = Math.random() * 6 + 1;
      particles.push({
        x, y,
        vx: Math.cos(angle) * spd,
        vy: Math.sin(angle) * spd,
        life: 1,
        decay: 0.02 + Math.random() * 0.02,
        size: Math.random() * 3 + 1.5,
        color
      });
    }
  }

  // ========== دشمن‌ها ==========
  const ENEMY_TYPES = [
    { hue: 300, r: 18, hp: 1 },  // صورتی
    { hue: 200, r: 20, hp: 1 },  // آبی
    { hue: 50,  r: 16, hp: 1 },  // زرد
    { hue: 0,   r: 22, hp: 2 },  // قرمز (سخت‌تر)
    { hue: 140, r: 19, hp: 1 }   // سبز
  ];

  function spawnEnemy() {
    const type = ENEMY_TYPES[Math.floor(Math.random() * ENEMY_TYPES.length)];
    const r = type.r + Math.random() * 4;
    enemies.push({
      x: Math.random() * (W - r * 4) + r * 2,
      y: -r * 2,
      r,
      hp: type.hp,
      maxHp: type.hp,
      speed: 1.5 + Math.random() * 1.5 + score / 400,
      wobble: Math.random() * Math.PI * 2,
      hue: type.hue + Math.random() * 20 - 10,
      rotation: 0,
      rotSpeed: (Math.random() - 0.5) * 0.03,
      hit: 0
    });
  }

  // ========== ورودی ==========
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
  bindButton('btnLeft', 'left');
  bindButton('btnRight', 'right');
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

  // ========== به‌روزرسانی ==========
  function update(dt) {
    // حرکت سفینه
    if (input.left) ship.x -= ship.speed;
    if (input.right) ship.x += ship.speed;
    ship.x = Math.max(30, Math.min(W - 30, ship.x));
    ship.y = H - 110;

    // شلیک
    shootTimer += dt;
    if (input.fire && shootTimer > 200) {
      shootTimer = 0;
      lasers.push({ x: ship.x, y: ship.y - 30 });
    }

    // لیزرها
    for (let i = lasers.length - 1; i >= 0; i--) {
      lasers[i].y -= 14;
      if (lasers[i].y < -20) lasers.splice(i, 1);
    }

    // تولید دشمن
    enemyTimer += dt;
    const spawnRate = Math.max(400, 900 - score / 5);
    if (enemyTimer > spawnRate) {
      enemyTimer = 0;
      spawnEnemy();
    }

    // حرکت دشمن‌ها
    for (let i = enemies.length - 1; i >= 0; i--) {
      const e = enemies[i];
      e.y += e.speed;
      e.wobble += 0.04;
      e.x += Math.sin(e.wobble) * 0.6;
      e.rotation += e.rotSpeed;
      if (e.hit > 0) e.hit -= 0.05;

      // برخورد با سفینه
      const dx = e.x - ship.x;
      const dy = e.y - ship.y;
      if (Math.sqrt(dx*dx + dy*dy) < e.r + 16) {
        explode(e.x, e.y, 'hsl(' + e.hue + ',100%,60%)');
        enemies.splice(i, 1);
        screenShake = 12;
        loseLife();
        continue;
      }

      if (e.y > H + 60) {
        enemies.splice(i, 1);
        loseLife();
      }
    }

    // برخورد لیزر با دشمن
    for (let i = enemies.length - 1; i >= 0; i--) {
      const e = enemies[i];
      for (let j = lasers.length - 1; j >= 0; j--) {
        const l = lasers[j];
        const dx = e.x - l.x, dy = e.y - l.y;
        if (Math.sqrt(dx*dx + dy*dy) < e.r + 6) {
          lasers.splice(j, 1);
          e.hp--;
          e.hit = 1;
          if (e.hp <= 0) {
            explode(e.x, e.y, 'hsl(' + e.hue + ',100%,60%)', 22);
            enemies.splice(i, 1);
            score += 10 * e.maxHp;
            document.getElementById('score').textContent = score;
            if (score > highScore) {
              highScore = score;
              setHighScore(highScore);
              document.getElementById('highScore').textContent = highScore;
            }
          } else {
            explode(e.x, e.y, '#fff', 5);
          }
          break;
        }
      }
    }

    // ذرات
    for (let i = particles.length - 1; i >= 0; i--) {
      const p = particles[i];
      p.x += p.vx;
      p.y += p.vy;
      p.vx *= 0.96;
      p.vy *= 0.96;
      p.life -= p.decay;
      if (p.life <= 0) particles.splice(i, 1);
    }

    // ستاره‌ها
    for (const s of stars) {
      s.y += s.speed;
      s.twinkle += 0.05;
      if (s.y > H) { s.y = 0; s.x = Math.random() * W; }
    }
    for (const n of nebulaStars) {
      n.y += n.speed;
      if (n.y - n.r > H) { n.y = -n.r; n.x = Math.random() * W; }
    }

    if (screenShake > 0) screenShake *= 0.9;
  }

  function loseLife() {
    lives--;
    document.getElementById('lives').textContent = Math.max(0, lives);
    if (navigator.vibrate) navigator.vibrate(80);
    if (lives <= 0) gameOver();
  }

  // ========== رسم ==========
  function draw() {
    ctx.save();
    if (screenShake > 0.5) {
      ctx.translate((Math.random() - 0.5) * screenShake, (Math.random() - 0.5) * screenShake);
    }

    // پس‌زمینه گرادیانت
    const grad = ctx.createLinearGradient(0, 0, 0, H);
    grad.addColorStop(0, '#05050f');
    grad.addColorStop(0.5, '#0a0a1f');
    grad.addColorStop(1, '#050510');
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, W, H);

    // سحابی‌ها
    for (const n of nebulaStars) {
      const g = ctx.createRadialGradient(n.x, n.y, 0, n.x, n.y, n.r);
      g.addColorStop(0, 'hsla(' + n.hue + ',80%,50%,0.08)');
      g.addColorStop(1, 'hsla(' + n.hue + ',80%,50%,0)');
      ctx.fillStyle = g;
      ctx.fillRect(n.x - n.r, n.y - n.r, n.r * 2, n.r * 2);
    }

    // ستاره‌ها
    for (const s of stars) {
      const alpha = 0.3 + Math.sin(s.twinkle) * 0.3 + s.speed / 4;
      ctx.fillStyle = 'rgba(255,255,255,' + Math.max(0.1, alpha) + ')';
      ctx.fillRect(s.x, s.y, s.size, s.size);
    }

    // لیزرها
    ctx.shadowBlur = 15;
    ctx.shadowColor = '#0ff';
    for (const l of lasers) {
      const lg = ctx.createLinearGradient(l.x, l.y - 12, l.x, l.y + 10);
      lg.addColorStop(0, '#fff');
      lg.addColorStop(0.5, '#0ff');
      lg.addColorStop(1, 'transparent');
      ctx.fillStyle = lg;
      ctx.fillRect(l.x - 2.5, l.y - 12, 5, 22);
    }

    // دشمن‌ها
    for (const e of enemies) {
      ctx.save();
      ctx.translate(e.x, e.y);
      ctx.rotate(e.rotation);

      const color = e.hit > 0.5 ? '#fff' : 'hsl(' + e.hue + ',100%,60%)';
      ctx.shadowBlur = 18;
      ctx.shadowColor = 'hsl(' + e.hue + ',100%,60%)';

      // بدنه دشمن (شکل هندسی)
      ctx.fillStyle = color;
      ctx.beginPath();
      const sides = 6;
      for (let i = 0; i < sides; i++) {
        const a = (i / sides) * Math.PI * 2 - Math.PI / 2;
        const x = Math.cos(a) * e.r;
        const y = Math.sin(a) * e.r;
        if (i === 0) ctx.moveTo(x, y);
        else ctx.lineTo(x, y);
      }
      ctx.closePath();
      ctx.fill();

      // حلقه داخلی
      ctx.shadowBlur = 0;
      ctx.fillStyle = 'rgba(0,0,0,0.4)';
      ctx.beginPath();
      ctx.arc(0, 0, e.r * 0.55, 0, Math.PI * 2);
      ctx.fill();

      // چشم مرکزی
      ctx.fillStyle = color;
      ctx.shadowBlur = 10;
      ctx.shadowColor = color;
      ctx.beginPath();
      ctx.arc(0, 0, e.r * 0.3, 0, Math.PI * 2);
      ctx.fill();

      ctx.restore();

      // نوار جان (اگه بیشتر از ۱ بود)
      if (e.maxHp > 1 && e.hp < e.maxHp) {
        ctx.shadowBlur = 0;
        ctx.fillStyle = 'rgba(255,0,0,0.3)';
        ctx.fillRect(e.x - e.r, e.y - e.r - 8, e.r * 2, 4);
        ctx.fillStyle = '#0f0';
        ctx.fillRect(e.x - e.r, e.y - e.r - 8, e.r * 2 * (e.hp / e.maxHp), 4);
      }
    }

    // سفینه
    if (running || lives > 0) {
      ctx.save();
      ctx.translate(ship.x, ship.y);

      // شعله (سه لایه)
      ctx.shadowBlur = 20;
      ctx.shadowColor = '#f80';
      const flameLen = 28 + Math.random() * 15;
      // بیرونی
      ctx.fillStyle = '#f40';
      ctx.beginPath();
      ctx.moveTo(-9, 18);
      ctx.lineTo(0, 20 + flameLen);
      ctx.lineTo(9, 18);
      ctx.closePath();
      ctx.fill();
      // میانی
      ctx.fillStyle = '#f80';
      ctx.shadowColor = '#f80';
      ctx.beginPath();
      ctx.moveTo(-6, 18);
      ctx.lineTo(0, 15 + flameLen * 0.7);
      ctx.lineTo(6, 18);
      ctx.closePath();
      ctx.fill();
      // داخلی
      ctx.fillStyle = '#ff0';
      ctx.shadowColor = '#ff0';
      ctx.beginPath();
      ctx.moveTo(-3, 18);
      ctx.lineTo(0, 12 + flameLen * 0.4);
      ctx.lineTo(3, 18);
      ctx.closePath();
      ctx.fill();

      // بال‌های سفینه
      ctx.shadowBlur = 20;
      ctx.shadowColor = '#0ff';
      const bodyGrad = ctx.createLinearGradient(0, -30, 0, 20);
      bodyGrad.addColorStop(0, '#0ff');
      bodyGrad.addColorStop(1, '#06c');
      ctx.fillStyle = bodyGrad;
      ctx.beginPath();
      ctx.moveTo(0, -30);
      ctx.lineTo(22, 15);
      ctx.lineTo(10, 18);
      ctx.lineTo(0, 10);
      ctx.lineTo(-10, 18);
      ctx.lineTo(-22, 15);
      ctx.closePath();
      ctx.fill();

      // خط وسط
      ctx.strokeStyle = '#fff';
      ctx.lineWidth = 1.5;
      ctx.shadowBlur = 10;
      ctx.shadowColor = '#fff';
      ctx.beginPath();
      ctx.moveTo(0, -25);
      ctx.lineTo(0, 10);
      ctx.stroke();

      // کابین
      ctx.fillStyle = '#fff';
      ctx.shadowBlur = 15;
      ctx.shadowColor = '#fff';
      ctx.beginPath();
      ctx.arc(0, -6, 5, 0, Math.PI * 2);
      ctx.fill();
      // براقی کابین
      ctx.fillStyle = '#0ff';
      ctx.beginPath();
      ctx.arc(-1.5, -7.5, 2, 0, Math.PI * 2);
      ctx.fill();

      ctx.restore();
    }

    // ذرات
    ctx.shadowBlur = 0;
    for (const p of particles) {
      ctx.globalAlpha = Math.max(0, p.life);
      ctx.fillStyle = p.color;
      ctx.beginPath();
      ctx.arc(p.x, p.y, p.size * p.life, 0, Math.PI * 2);
      ctx.fill();
    }
    ctx.globalAlpha = 1;
    ctx.shadowBlur = 0;

    ctx.restore();
  }

  // ========== حلقه بازی ==========
  function loop(time) {
    if (!running) return;
    const dt = lastTime ? Math.min(50, time - lastTime) : 16;
    lastTime = time;
    update(dt);
    draw();
    requestAnimationFrame(loop);
  }

  // ========== شروع / پایان ==========
  function startGame() {
    score = 0;
    lives = 3;
    lasers = [];
    enemies = [];
    particles = [];
    enemyTimer = 0;
    shootTimer = 0;
    lastTime = 0;
    screenShake = 0;
    ship.x = W / 2;
    ship.y = H - 110;
    document.getElementById('score').textContent = '0';
    document.getElementById('lives').textContent = '3';
    document.getElementById('highScore').textContent = highScore;
    document.getElementById('startOverlay').classList.add('hidden');
    document.getElementById('gameOverOverlay').classList.add('hidden');
    running = true;
    initStars();
    requestAnimationFrame(loop);
  }

  function gameOver() {
    running = false;
    const isNew = score > highScore;

    document.getElementById('finalScore').textContent = 'امتیاز: ' + score;
    const statusEl = document.getElementById('recordStatus');
    if (isNew) {
      statusEl.innerHTML = '<div class="newRecord">🎉 رکورد جدید!</div>';
    } else {
      statusEl.innerHTML = '<div class="recordLine">🏆 رکورد: ' + highScore + '</div>';
    }

    document.getElementById('gameOverOverlay').classList.remove('hidden');
    document.getElementById('playerName').value = '';
    document.getElementById('playerName').focus();
  }

  // ثبت رکورد از صفحه Game Over
  document.getElementById('submitScoreBtn').addEventListener('click', () => {
    const name = document.getElementById('playerName').value.trim();
    if (!name) {
      alert('اسمت رو وارد کن!');
      return;
    }

    const board = loadLeaderboard();
    board.push({ name: name.substring(0, 20), score: score, time: Date.now() });
    board.sort((a, b) => b.score - a.score);
    const top = board.slice(0, 10);
    saveLeaderboard(top);

    // رفتن به لیدربورد
    document.getElementById('gameOverOverlay').classList.add('hidden');
    document.querySelector('.tab[data-tab="leaderboard"]').click();

    // نمایش پیام موفقیت اگه جزو ۱۰ نفر بود
    const rank = top.findIndex(r => r.name === name.substring(0, 20) && r.score === score);
    if (rank !== -1 && rank < 10) {
      setTimeout(() => alert('🎉 رکوردت ثبت شد! رتبه ' + (rank + 1)), 300);
    } else {
      setTimeout(() => alert('😕 امتیازت به ۱۰ نفر برتر نرسید!'), 300);
    }
  });

  // ========== لیدربورد ==========
  const list = document.getElementById('list');

  function renderLeaderboard() {
    const board = loadLeaderboard();
    if (board.length === 0) {
      list.innerHTML = '<div class="empty">هنوز هیچ رکوردی ثبت نشده<br>برو بازی کن و اولین رکورد رو بزن! 🎮</div>';
      return;
    }
    let html = '';
    board.forEach((r, i) => {
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
      .replace(/&/g, '&amp;').replace(/</g, '&lt;')
      .replace(/>/g, '&gt;').replace(/"/g, '&quot;')
      .replace(/'/g, '&#39;');
  }

  document.getElementById('clearBtn').addEventListener('click', () => {
    if (confirm('مطمئنی می‌خوای همه رکوردها رو پاک کنی؟')) {
      try { localStorage.removeItem(LEADERBOARD_KEY); } catch (e) {}
      renderLeaderboard();
    }
  });

  // ========== شروع ==========
  document.getElementById('startBtn').addEventListener('click', startGame);
  document.addEventListener('gesturestart', e => e.preventDefault());

  resizeCanvas();
  window.addEventListener('resize', resizeCanvas);
  window.addEventListener('orientationchange', () => setTimeout(resizeCanvas, 300));

  ship.x = W / 2;
  ship.y = H - 110;
  initStars();
  draw();
  renderLeaderboard();

})();
</script>
</body>
</html>

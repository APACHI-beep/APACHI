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

  /* تب‌ها */
  .tabs {
    display: flex;
    background: #0d0d1f;
    border-bottom: 2px solid #0ff;
    flex-shrink: 0;
    z-index: 50;
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
  }

  /* صفحه‌ها */
  .page {
    flex: 1;
    position: relative;
    overflow: hidden;
    display: none;
  }
  .page.active { display: block; }

  /* بازی */
  #gameCanvas {
    display: block;
    position: absolute;
    top: 0; left: 0;
    width: 100%;
    height: 100%;
  }

  /* HUD */
  #hud {
    position: absolute;
    top: 10px; left: 0; right: 0;
    padding: 0 20px;
    display: flex; justify-content: space-between;
    color: #0ff;
    text-shadow: 0 0 10px #0ff;
    font-size: 15px; font-weight: bold;
    pointer-events: none;
    z-index: 20;
  }
  #hud .right { text-align: right; }

  /* کنترل‌ها */
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
    cursor: pointer;
    transition: transform 0.08s, background 0.08s;
  }
  .btn-ctrl.active { background: rgba(0,255,255,0.45); transform: scale(0.92); }
  #fire {
    width: 80px; height: 80px;
    background: rgba(255,80,80,0.12);
    border-color: #f66; color: #f66;
    font-size: 30px;
  }
  #fire.active { background: rgba(255,80,80,0.45); }

  /* Overlay */
  .overlay {
    position: absolute; inset: 0;
    background: rgba(5,5,15,0.92);
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    text-align: center; z-index: 40; padding: 25px;
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
  .overlay input:focus { border-color: #0ff; }
  .overlay .btn {
    padding: 14px 40px; font-size: 17px; font-weight: bold;
    font-family: Tahoma, sans-serif;
    background: linear-gradient(135deg, #0ff, #0af);
    border: none; border-radius: 50px;
    color: #000; cursor: pointer;
  }
  .overlay .btn:active { transform: scale(0.95); }
  .finalScore { color: #ff0; font-size: 22px; margin: 10px 0; }
  .recordLine { color: #0ff; font-size: 16px; margin: 5px 0 15px; }
  .newRecord { color: #ff0; font-size: 18px; margin: 8px 0; }

  /* لیدربورد */
  #page-leaderboard {
    overflow-y: auto;
    padding: 20px;
  }
  .leaderboard { max-width: 500px; margin: 0 auto; }
  .leaderboard h2 {
    color: #ff0; text-align: center; margin-bottom: 18px;
    font-size: 22px;
  }
  .record-item {
    display: flex; align-items: center;
    background: #14142a;
    border: 1px solid #ff02;
    border-radius: 12px;
    padding: 12px 16px;
    margin-bottom: 9px;
  }
  .record-item.rank-1 { border-color: #ffd700; background: #1f1f10; }
  .record-item.rank-2 { border-color: #c0c0c0; background: #1f1f1f; }
  .record-item.rank-3 { border-color: #cd7f32; background: #1f150d; }
  .rank {
    font-size: 20px; font-weight: bold; width: 42px;
    text-align: center; color: #888; flex-shrink: 0;
  }
  .rank-1 .rank { color: #ffd700; }
  .rank-2 .rank { color: #c0c0c0; }
  .rank-3 .rank { color: #cd7f32; }
  .name {
    flex: 1; font-size: 15px; padding: 0 10px;
    overflow: hidden; text-overflow: ellipsis; white-space: nowrap;
  }
  .points {
    font-size: 17px; font-weight: bold;
    color: #0ff; flex-shrink: 0;
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
</style>
</head>
<body>

<div class="tabs">
  <div class="tab active" data-tab="game">🎮 بازی</div>
  <div class="tab" data-tab="leaderboard">🏆 لیدربورد</div>
</div>

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
  var LEADERBOARD_KEY = 'spaceship_leaderboard_v1';
  var HIGHSCORE_KEY = 'spaceship_highscore_v1';

  function loadLeaderboard() {
    try {
      var raw = localStorage.getItem(LEADERBOARD_KEY);
      if (!raw) return [];
      var arr = JSON.parse(raw);
      return Array.isArray(arr) ? arr : [];
    } catch (e) { return []; }
  }
  function saveLeaderboard(arr) {
    try { localStorage.setItem(LEADERBOARD_KEY, JSON.stringify(arr)); } catch (e) {}
  }
  function getHighScore() {
    try {
      var v = localStorage.getItem(HIGHSCORE_KEY);
      return v ? parseInt(v, 10) || 0 : 0;
    } catch (e) { return 0; }
  }
  function setHighScore(v) {
    try { localStorage.setItem(HIGHSCORE_KEY, String(v)); } catch (e) {}
  }

  // ========== Canvas ==========
  var canvas = document.getElementById('gameCanvas');
  var ctx = canvas.getContext('2d');
  var W = 800, H = 600;

  // 🔧 مهم: اندازه‌گیری از خود canvas بعد از نمایش
  function resizeCanvas() {
    var gamePage = document.getElementById('page-game');
    var rect = gamePage.getBoundingClientRect();
    var w = Math.floor(rect.width);
    var h = Math.floor(rect.height);

    if (w < 10) w = window.innerWidth;
    if (h < 10) h = window.innerHeight - 60;

    W = canvas.width = w;
    H = canvas.height = h;

    ship.y = H - 110;
    if (!ship.x || ship.x > W) ship.x = W / 2;
    initStars();
  }

  // ========== وضعیت ==========
  var ship = { x: 0, y: 0, speed: 7 };
  var stars = [];
  var nebulaStars = [];
  var lasers = [];
  var enemies = [];
  var particles = [];
  var score = 0;
  var lives = 3;
  var highScore = getHighScore();
  var running = false;
  var lastTime = 0;
  var enemyTimer = 0;
  var shootTimer = 0;
  var screenShake = 0;

  var input = { left: false, right: false, fire: false };

  document.getElementById('highScore').textContent = highScore;
  document.getElementById('startRecord').textContent = '🏆 رکورد: ' + highScore;

  // ========== ستاره‌ها ==========
  function initStars() {
    stars.length = 0;
    nebulaStars.length = 0;
    for (var i = 0; i < 130; i++) {
      stars.push({
        x: Math.random() * W,
        y: Math.random() * H,
        speed: Math.random() * 2.5 + 0.4,
        size: Math.random() * 1.8 + 0.4,
        twinkle: Math.random() * Math.PI * 2
      });
    }
    for (var j = 0; j < 8; j++) {
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
    for (var i = 0; i < count; i++) {
      var angle = Math.random() * Math.PI * 2;
      var spd = Math.random() * 6 + 1;
      particles.push({
        x: x, y: y,
        vx: Math.cos(angle) * spd,
        vy: Math.sin(angle) * spd,
        life: 1,
        decay: 0.02 + Math.random() * 0.02,
        size: Math.random() * 3 + 1.5,
        color: color
      });
    }
  }

  // ========== دشمن ==========
  var ENEMY_TYPES = [
    { hue: 300, r: 18, hp: 1 },
    { hue: 200, r: 20, hp: 1 },
    { hue: 50,  r: 16, hp: 1 },
    { hue: 0,   r: 22, hp: 2 },
    { hue: 140, r: 19, hp: 1 }
  ];

  function spawnEnemy() {
    var type = ENEMY_TYPES[Math.floor(Math.random() * ENEMY_TYPES.length)];
    var r = type.r + Math.random() * 4;
    enemies.push({
      x: Math.random() * (W - r * 4) + r * 2,
      y: -r * 2,
      r: r,
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
    var el = document.getElementById(id);
    function press(e) { e.preventDefault(); input[key] = true; el.classList.add('active'); }
    function release(e) { if (e) e.preventDefault(); input[key] = false; el.classList.remove('active'); }
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

  document.addEventListener('keydown', function(e) {
    if (e.key === 'ArrowLeft' || e.key === 'a') input.left = true;
    if (e.key === 'ArrowRight' || e.key === 'd') input.right = true;
    if (e.key === ' ' || e.key === 'ArrowUp') { input.fire = true; e.preventDefault(); }
  });
  document.addEventListener('keyup', function(e) {
    if (e.key === 'ArrowLeft' || e.key === 'a') input.left = false;
    if (e.key === 'ArrowRight' || e.key === 'd') input.right = false;
    if (e.key === ' ' || e.key === 'ArrowUp') input.fire = false;
  });

  // ========== به‌روزرسانی ==========
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

    var i, j, e, l, dx, dy;
    for (i = lasers.length - 1; i >= 0; i--) {
      lasers[i].y -= 14;
      if (lasers[i].y < -20) lasers.splice(i, 1);
    }

    enemyTimer += dt;
    var spawnRate = Math.max(400, 900 - score / 5);
    if (enemyTimer > spawnRate) {
      enemyTimer = 0;
      spawnEnemy();
    }

    for (i = enemies.length - 1; i >= 0; i--) {
      e = enemies[i];
      e.y += e.speed;
      e.wobble += 0.04;
      e.x += Math.sin(e.wobble) * 0.6;
      e.rotation += e.rotSpeed;
      if (e.hit > 0) e.hit -= 0.05;

      dx = e.x - ship.x;
      dy = e.y - ship.y;
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

    for (i = enemies.length - 1; i >= 0; i--) {
      e = enemies[i];
      for (j = lasers.length - 1; j >= 0; j--) {
        l = lasers[j];
        dx = e.x - l.x;
        dy = e.y - l.y;
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

    for (i = particles.length - 1; i >= 0; i--) {
      var p = particles[i];
      p.x += p.vx;
      p.y += p.vy;
      p.vx *= 0.96;
      p.vy *= 0.96;
      p.life -= p.decay;
      if (p.life <= 0) particles.splice(i, 1);
    }

    for (i = 0; i < stars.length; i++) {
      var s = stars[i];
      s.y += s.speed;
      s.twinkle += 0.05;
      if (s.y > H) { s.y = 0; s.x = Math.random() * W; }
    }
    for (i = 0; i < nebulaStars.length; i++) {
      var n = nebulaStars[i];
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

    var grad = ctx.createLinearGradient(0, 0, 0, H);
    grad.addColorStop(0, '#05050f');
    grad.addColorStop(0.5, '#0a0a1f');
    grad.addColorStop(1, '#050510');
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, W, H);

    var i;
    for (i = 0; i < nebulaStars.length; i++) {
      var n = nebulaStars[i];
      var g = ctx.createRadialGradient(n.x, n.y, 0, n.x, n.y, n.r);
      g.addColorStop(0, 'hsla(' + n.hue + ',80%,50%,0.08)');
      g.addColorStop(1, 'hsla(' + n.hue + ',80%,50%,0)');
      ctx.fillStyle = g;
      ctx.fillRect(n.x - n.r, n.y - n.r, n.r * 2, n.r * 2);
    }

    for (i = 0; i < stars.length; i++) {
      var s = stars[i];
      var alpha = 0.3 + Math.sin(s.twinkle) * 0.3 + s.speed / 4;
      ctx.fillStyle = 'rgba(255,255,255,' + Math.max(0.1, alpha) + ')';
      ctx.fillRect(s.x, s.y, s.size, s.size);
    }

    ctx.shadowBlur = 15;
    ctx.shadowColor = '#0ff';
    for (i = 0; i < lasers.length; i++) {
      var l = lasers[i];
      var lg = ctx.createLinearGradient(l.x, l.y - 12, l.x, l.y + 10);
      lg.addColorStop(0, '#fff');
      lg.addColorStop(0.5, '#0ff');
      lg.addColorStop(1, 'rgba(0,255,255,0)');
      ctx.fillStyle = lg;
      ctx.fillRect(l.x - 2.5, l.y - 12, 5, 22);
    }

    for (i = 0; i < enemies.length; i++) {
      var e = enemies[i];
      ctx.save();
      ctx.translate(e.x, e.y);
      ctx.rotate(e.rotation);

      var color = e.hit > 0.5 ? '#fff' : 'hsl(' + e.hue + ',100%,60%)';
      ctx.shadowBlur = 18;
      ctx.shadowColor = 'hsl(' + e.hue + ',100%,60%)';

      ctx.fillStyle = color;
      ctx.beginPath();
      var sides = 6;
      for (var k = 0; k < sides; k++) {
        var a = (k / sides) * Math.PI * 2 - Math.PI / 2;
        var ex = Math.cos(a) * e.r;
        var ey = Math.sin(a) * e.r;
        if (k === 0) ctx.moveTo(ex, ey);
        else ctx.lineTo(ex, ey);
      }
      ctx.closePath();
      ctx.fill();

      ctx.shadowBlur = 0;
      ctx.fillStyle = 'rgba(0,0,0,0.4)';
      ctx.beginPath();
      ctx.arc(0, 0, e.r * 0.55, 0, Math.PI * 2);
      ctx.fill();

      ctx.fillStyle = color;
      ctx.shadowBlur = 10;
      ctx.shadowColor = color;
      ctx.beginPath();
      ctx.arc(0, 0, e.r * 0.3, 0, Math.PI * 2);
      ctx.fill();

      ctx.restore();

      if (e.maxHp > 1 && e.hp < e.maxHp) {
        ctx.shadowBlur = 0;
        ctx.fillStyle = 'rgba(255,0,0,0.3)';
        ctx.fillRect(e.x - e.r, e.y - e.r - 8, e.r * 2, 4);
        ctx.fillStyle = '#0f0';
        ctx.fillRect(e.x - e.r, e.y - e.r - 8, e.r * 2 * (e.hp / e.maxHp), 4);
      }
    }

    if (running || lives > 0) {
      ctx.save();
      ctx.translate(ship.x, ship.y);

      ctx.shadowBlur = 20;
      ctx.shadowColor = '#f80';
      var flameLen = 28 + Math.random() * 15;
      ctx.fillStyle = '#f40';
      ctx.beginPath();
      ctx.moveTo(-9, 18);
      ctx.lineTo(0, 20 + flameLen);
      ctx.lineTo(9, 18);
      ctx.closePath();
      ctx.fill();
      ctx.fillStyle = '#f80';
      ctx.shadowColor = '#f80';
      ctx.beginPath();
      ctx.moveTo(-6, 18);
      ctx.lineTo(0, 15 + flameLen * 0.7);
      ctx.lineTo(6, 18);
      ctx.closePath();
      ctx.fill();
      ctx.fillStyle = '#ff0';
      ctx.shadowColor = '#ff0';
      ctx.beginPath();
      ctx.moveTo(-3, 18);
      ctx.lineTo(0, 12 + flameLen * 0.4);
      ctx.lineTo(3, 18);
      ctx.closePath();
      ctx.fill();

      ctx.shadowBlur = 20;
      ctx.shadowColor = '#0ff';
      var bodyGrad = ctx.createLinearGradient(0, -30, 0, 20);
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

      ctx.strokeStyle = '#fff';
      ctx.lineWidth = 1.5;
      ctx.shadowBlur = 10;
      ctx.shadowColor = '#fff';
      ctx.beginPath();
      ctx.moveTo(0, -25);
      ctx.lineTo(0, 10);
      ctx.stroke();

      ctx.fillStyle = '#fff';
      ctx.shadowBlur = 15;
      ctx.shadowColor = '#fff';
      ctx.beginPath();
      ctx.arc(0, -6, 5, 0, Math.PI * 2);
      ctx.fill();
      ctx.fillStyle = '#0ff';
      ctx.beginPath();
      ctx.arc(-1.5, -7.5, 2, 0, Math.PI * 2);
      ctx.fill();

      ctx.restore();
    }

    ctx.shadowBlur = 0;
    for (i = 0; i < particles.length; i++) {
      var pt = particles[i];
      ctx.globalAlpha = Math.max(0, pt.life);
      ctx.fillStyle = pt.color;
      ctx.beginPath();
      ctx.arc(pt.x, pt.y, pt.size * pt.life, 0, Math.PI * 2);
      ctx.fill();
    }
    ctx.globalAlpha = 1;

    ctx.restore();
  }

  // ========== حلقه ==========
  function loop(time) {
    if (!running) return;
    var dt = lastTime ? Math.min(50, time - lastTime) : 16;
    lastTime = time;
    update(dt);
    draw();
    requestAnimationFrame(loop);
  }

  // ========== شروع ==========
  function startGame() {
    resizeCanvas();  // 🔧 مهم: اندازه‌گیری قبل از شروع

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
    var isNew = score > highScore;

    document.getElementById('finalScore').textContent = 'امتیاز: ' + score;
    var statusEl = document.getElementById('recordStatus');
    if (isNew) {
      statusEl.innerHTML = '<div class="newRecord">🎉 رکورد جدید!</div>';
    } else {
      statusEl.innerHTML = '<div class="recordLine">🏆 رکورد: ' + highScore + '</div>';
    }

    document.getElementById('gameOverOverlay').classList.remove('hidden');
    document.getElementById('playerName').value = '';
  }

  document.getElementById('submitScoreBtn').addEventListener('click', function() {
    var name = document.getElementById('playerName').value.trim();
    if (!name) {
      alert('اسمت رو وارد کن!');
      return;
    }

    var board = loadLeaderboard();
    board.push({ name: name.substring(0, 20), score: score, time: Date.now() });
    board.sort(function(a, b) { return b.score - a.score; });
    var top = board.slice(0, 10);
    saveLeaderboard(top);

    document.getElementById('gameOverOverlay').classList.add('hidden');
    document.querySelector('.tab[data-tab="leaderboard"]').click();

    var rank = -1;
    for (var i = 0; i < top.length; i++) {
      if (top[i].score === score) { rank = i; break; }
    }
    if (rank !== -1) {
      setTimeout(function() { alert('🎉 رکوردت ثبت شد! رتبه ' + (rank + 1)); }, 300);
    } else {
      setTimeout(function() { alert('😕 امتیازت به ۱۰ نفر برتر نرسید!'); }, 300);
    }
  });

  // ========== لیدربورد ==========
  var list = document.getElementById('list');

  function renderLeaderboard() {
    var board = loadLeaderboard();
    if (board.length === 0) {
      list.innerHTML = '<div class="empty">هنوز هیچ رکوردی ثبت نشده<br>برو بازی کن و اولین رکورد رو بزن! 🎮</div>';
      return;
    }
    var html = '';
    for (var i = 0; i < board.length; i++) {
      var r = board[i];
      var rank = i + 1;
      var medal = rank === 1 ? '🥇' : rank === 2 ? '🥈' : rank === 3 ? '🥉' : rank;
      var cls = rank <= 3 ? 'rank-' + rank : '';
      html +=
        '<div class="record-item ' + cls + '">' +
          '<div class="rank">' + medal + '</div>' +
          '<div class="name">' + escapeHtml(r.name) + '</div>' +
          '<div class="points">' + r.score + '</div>' +
        '</div>';
    }
    list.innerHTML = html;
  }

  function escapeHtml(str) {
    return String(str)
      .replace(/&/g, '&amp;').replace(/</g, '&lt;')
      .replace(/>/g, '&gt;').replace(/"/g, '&quot;')
      .replace(/'/g, '&#39;');
  }

  document.getElementById('clearBtn').addEventListener('click', function() {
    if (confirm('مطمئنی می‌خوای همه رکوردها رو پاک کنی؟')) {
      try { localStorage.removeItem(LEADERBOARD_KEY); } catch (e) {}
      renderLeaderboard();
    }
  });

  // ========== تب‌ها ==========
  var tabs = document.querySelectorAll('.tab');
  var pages = document.querySelectorAll('.page');

  for (var t = 0; t < tabs.length; t++) {
    (function(tab) {
      tab.addEventListener('click', function() {
        var target = tab.getAttribute('data-tab');
        for (var i = 0; i < tabs.length; i++) {
          tabs[i].classList.toggle('active', tabs[i] === tab);
        }
        for (var i = 0; i < pages.length; i++) {
          pages[i].classList.toggle('active', pages[i].id === 'page-' + target);
        }
        if (target === 'leaderboard') {
          renderLeaderboard();
        }
        if (target === 'game') {
          // 🔧 مهم: بعد از نمایش تب، canvas رو دوباره اندازه بگیر
          setTimeout(function() {
            resizeCanvas();
            if (running) {
              // اگه بازی در جریانه، دوباره رسم کن
              draw();
            }
          }, 50);
        }
      });
    })(tabs[t]);
  }

  // ========== شروع ==========
  document.getElementById('startBtn').addEventListener('click', startGame);
  document.addEventListener('gesturestart', function(e) { e.preventDefault(); });

  window.addEventListener('resize', function() {
    if (running) resizeCanvas();
  });
  window.addEventListener('orientationchange', function() {
    setTimeout(function() {
      if (running) resizeCanvas();
    }, 300);
  });

  // 🔧 مقدار اولیه
  ship.x = 400;
  ship.y = 500;
  initStars();
  renderLeaderboard();

})();
</script>
</body>
</html>

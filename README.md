here<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>🚀 سفینه فضایی</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  html, body {
    background: #000;
    overflow: hidden;
    font-family: Tahoma, sans-serif;
    height: 100vh;
    height: 100dvh;
    touch-action: none;
    user-select: none;
    -webkit-user-select: none;
  }
  canvas { display: block; touch-action: none; }
  #ui {
    position: fixed;
    top: 15px;
    right: 15px;
    color: #0ff;
    text-shadow: 0 0 10px #0ff;
    font-size: 16px;
    z-index: 10;
    text-align: right;
    pointer-events: none;
  }
  /* دکمه‌های لمسی */
  .controls {
    position: fixed;
    bottom: 20px;
    left: 0;
    right: 0;
    display: flex;
    justify-content: space-between;
    padding: 0 20px;
    z-index: 20;
    pointer-events: none;
  }
  .btn-group {
    display: flex;
    gap: 15px;
    pointer-events: auto;
  }
  .btn {
    width: 70px;
    height: 70px;
    border-radius: 50%;
    background: rgba(0, 255, 255, 0.15);
    border: 2px solid #0ff;
    color: #0ff;
    font-size: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 0 20px #0ff5, inset 0 0 15px #0ff3;
    backdrop-filter: blur(5px);
    -webkit-backdrop-filter: blur(5px);
    transition: transform 0.1s, background 0.1s;
    cursor: pointer;
  }
  .btn:active, .btn.pressed {
    transform: scale(0.9);
    background: rgba(0, 255, 255, 0.4);
  }
  .btn-fire {
    width: 85px;
    height: 85px;
    background: rgba(255, 80, 80, 0.15);
    border-color: #f66;
    color: #f66;
    box-shadow: 0 0 20px #f665, inset 0 0 15px #f663;
    font-size: 35px;
  }
  .btn-fire:active, .btn-fire.pressed {
    background: rgba(255, 80, 80, 0.4);
  }
  @media (min-width: 800px) {
    .controls { display: none; }
  }
</style>
</head>
<body>
<canvas id="game"></canvas>
<div id="ui">
  <div>امتیاز: <span id="score">0</span></div>
  <div>جان: <span id="lives">3</span></div>
</div>

<!-- دکمه‌های لمسی -->
<div class="controls">
  <div class="btn-group">
    <div class="btn" id="btnLeft">◀</div>
    <div class="btn" id="btnRight">▶</div>
  </div>
  <div class="btn-group">
    <div class="btn btn-fire" id="btnFire">🔥</div>
  </div>
</div>

<script>
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
let W, H;
function resize() {
  W = canvas.width = window.innerWidth;
  H = canvas.height = window.innerHeight;
  // اگه سفینه بیرون صفحه بود، برگردونش
  if (ship) ship.y = H - 120;
}
resize();
window.addEventListener('resize', resize);
window.addEventListener('orientationchange', () => setTimeout(resize, 200));

// ستاره‌ها
const stars = Array.from({length: 150}, () => ({
  x: Math.random() * window.innerWidth,
  y: Math.random() * window.innerHeight,
  z: Math.random() * 3 + 0.5,
  size: Math.random() * 2
}));

// سفینه
const ship = {
  x: window.innerWidth / 2,
  y: window.innerHeight - 120,
  w: 50,
  h: 60,
  speed: 8
};

let lasers = [];
let enemies = [];
let particles = [];
let score = 0;
let lives = 3;
let gameOver = false;

// ========== کنترل‌ها ==========
const keys = {};

// کیبورد (برای دسکتاپ)
document.addEventListener('keydown', e => {
  keys[e.code] = true;
  if (e.code === 'Space' || e.code.startsWith('Arrow')) e.preventDefault();
});
document.addEventListener('keyup', e => { keys[e.code] = false; });

// ========== کنترل لمسی ==========
function bindHold(el, onDown, onUp) {
  const down = (e) => { e.preventDefault(); el.classList.add('pressed'); onDown(); };
  const up = (e) => { if(e) e.preventDefault(); el.classList.remove('pressed'); onUp(); };
  
  el.addEventListener('touchstart', down, {passive: false});
  el.addEventListener('touchend', up, {passive: false});
  el.addEventListener('touchcancel', up, {passive: false});
  el.addEventListener('mousedown', down);
  el.addEventListener('mouseup', up);
  el.addEventListener('mouseleave', up);
}

// دکمه چپ
bindHold(document.getElementById('btnLeft'),
  () => keys['ArrowLeft'] = true,
  () => keys['ArrowLeft'] = false
);
// دکمه راست
bindHold(document.getElementById('btnRight'),
  () => keys['ArrowRight'] = true,
  () => keys['ArrowRight'] = false
);
// دکمه شلیک
bindHold(document.getElementById('btnFire'),
  () => keys['Space'] = true,
  () => keys['Space'] = false
);

// ========== شلیک ==========
let lastShot = 0;
function shoot() {
  const now = Date.now();
  if (now - lastShot < 220) return;
  lastShot = now;
  lasers.push({ x: ship.x, y: ship.y - ship.h/2, speed: 13 });
}

// ========== دشمن ==========
function spawnEnemy() {
  if (gameOver) return;
  const size = 35 + Math.random() * 15;
  enemies.push({
    x: Math.random() * (W - 80) + 40,
    y: -50,
    r: size / 2,
    speed: 1.8 + Math.random() * 1.8 + (score / 200),
    wobble: Math.random() * Math.PI * 2,
    hue: Math.random() * 60 + 280 // بنفش-صورتی
  });
}
setInterval(spawnEnemy, 900);

// ========== انفجار ==========
function explode(x, y, color) {
  for (let i = 0; i < 18; i++) {
    particles.push({
      x, y,
      vx: (Math.random() - 0.5) * 9,
      vy: (Math.random() - 0.5) * 9,
      life: 1,
      color
    });
  }
}

// ========== رسم ==========
function drawShip(x, y) {
  ctx.save();
  ctx.translate(x, y);
  // شعله
  ctx.fillStyle = '#f80';
  ctx.shadowBlur = 15;
  ctx.shadowColor = '#f80';
  ctx.beginPath();
  ctx.moveTo(-8, 18);
  ctx.lineTo(0, 38 + Math.random() * 12);
  ctx.lineTo(8, 18);
  ctx.closePath();
  ctx.fill();
  // بدنه
  ctx.fillStyle = '#0ff';
  ctx.shadowBlur = 20;
  ctx.shadowColor = '#0ff';
  ctx.beginPath();
  ctx.moveTo(0, -30);
  ctx.lineTo(20, 20);
  ctx.lineTo(0, 10);
  ctx.lineTo(-20, 20);
  ctx.closePath();
  ctx.fill();
  // کابین
  ctx.fillStyle = '#fff';
  ctx.shadowBlur = 10;
  ctx.beginPath();
  ctx.arc(0, -5, 6, 0, Math.PI*2);
  ctx.fill();
  ctx.restore();
}

function drawEnemy(e) {
  ctx.save();
  ctx.translate(e.x, e.y);
  ctx.fillStyle = `hsl(${e.hue}, 100%, 60%)`;
  ctx.shadowBlur = 15;
  ctx.shadowColor = `hsl(${e.hue}, 100%, 60%)`;
  ctx.beginPath();
  ctx.arc(0, 0, e.r, 0, Math.PI*2);
  ctx.fill();
  ctx.shadowBlur = 0;
  ctx.fillStyle = '#000';
  ctx.beginPath();
  ctx.arc(-e.r*0.3, -e.r*0.15, e.r*0.15, 0, Math.PI*2);
  ctx.arc(e.r*0.3, -e.r*0.15, e.r*0.15, 0, Math.PI*2);
  ctx.fill();
  ctx.restore();
}

// ========== به‌روزرسانی ==========
function update() {
  if (gameOver) return;

  // حرکت
  if (keys['ArrowLeft'] || keys['KeyA']) ship.x -= ship.speed;
  if (keys['ArrowRight'] || keys['KeyD']) ship.x += ship.speed;
  ship.x = Math.max(30, Math.min(W - 30, ship.x));
  ship.y = H - 120;

  // شلیک
  if (keys['Space']) shoot();

  // لیزرها
  lasers.forEach(l => l.y -= l.speed);
  lasers = lasers.filter(l => l.y > -20);

  // دشمن‌ها
  enemies.forEach(e => {
    e.y += e.speed;
    e.wobble += 0.05;
    e.x += Math.sin(e.wobble) * 0.6;
  });

  // برخورد لیزر با دشمن
  for (let i = enemies.length - 1; i >= 0; i--) {
    for (let j = lasers.length - 1; j >= 0; j--) {
      const dx = enemies[i].x - lasers[j].x;
      const dy = enemies[i].y - lasers[j].y;
      if (Math.hypot(dx, dy) < enemies[i].r + 8) {
        explode(enemies[i].x, enemies[i].y, `hsl(${enemies[i].hue}, 100%, 60%)`);
        enemies.splice(i, 1);
        lasers.splice(j, 1);
        score += 10;
        document.getElementById('score').textContent = score;
        break;
      }
    }
  }

  // برخورد دشمن با سفینه
  for (let i = enemies.length - 1; i >= 0; i--) {
    const dx = enemies[i].x - ship.x;
    const dy = enemies[i].y - ship.y;
    if (Math.hypot(dx, dy) < enemies[i].r + 20) {
      explode(enemies[i].x, enemies[i].y, '#f44');
      enemies.splice(i, 1);
      loseLife();
    } else if (enemies[i].y > H + 60) {
      enemies.splice(i, 1);
      loseLife();
    }
  }

  // ذرات
  particles.forEach(p => {
    p.x += p.vx;
    p.y += p.vy;
    p.vy += 0.1;
    p.life -= 0.03;
  });
  particles = particles.filter(p => p.life > 0);
}

function loseLife() {
  lives--;
  document.getElementById('lives').textContent = lives;
  // لرزش گوشی
  if (navigator.vibrate) navigator.vibrate(100);
  if (lives <= 0) gameOver = true;
}

// ========== رندر ==========
function render() {
  ctx.fillStyle = '#000';
  ctx.fillRect(0, 0, W, H);

  stars.forEach(s => {
    s.y += s.z;
    if (s.y > H) { s.y = 0; s.x = Math.random() * W; }
    ctx.fillStyle = `rgba(255,255,255,${0.3 + s.z/5})`;
    ctx.fillRect(s.x, s.y, s.size, s.size);
  });

  lasers.forEach(l => {
    ctx.fillStyle = '#0ff';
    ctx.shadowBlur = 10;
    ctx.shadowColor = '#0ff';
    ctx.fillRect(l.x - 2, l.y - 10, 4, 20);
  });
  ctx.shadowBlur = 0;

  enemies.forEach(drawEnemy);

  if (!gameOver) drawShip(ship.x, ship.y);

  particles.forEach(p => {
    ctx.globalAlpha = p.life;
    ctx.fillStyle = p.color;
    ctx.fillRect(p.x, p.y, 4, 4);
  });
  ctx.globalAlpha = 1;

  if (gameOver) {
    ctx.fillStyle = 'rgba(0,0,0,0.7)';
    ctx.fillRect(0, 0, W, H);
    ctx.fillStyle = '#f44';
    ctx.font = 'bold 48px Tahoma';
    ctx.textAlign = 'center';
    ctx.shadowBlur = 30;
    ctx.shadowColor = '#f44';
    ctx.fillText('GAME OVER', W/2, H/2 - 20);
    ctx.fillStyle = '#0ff';
    ctx.font = '26px Tahoma';
    ctx.shadowColor = '#0ff';
    ctx.fillText('امتیاز: ' + score, W/2, H/2 + 40);
    ctx.fillStyle = '#fff';
    ctx.font = '18px Tahoma';
    ctx.shadowBlur = 0;
    ctx.fillText('برای شروع مجدد ضربه بزن', W/2, H/2 + 90);
  }
}

// ریست با لمس
canvas.addEventListener('touchstart', (e) => {
  if (gameOver) {
    e.preventDefault();
    resetGame();
  }
}, {passive: false});

canvas.addEventListener('click', () => {
  if (gameOver) resetGame();
});

function resetGame() {
  gameOver = false;
  score = 0;
  lives = 3;
  enemies = [];
  lasers = [];
  particles = [];
  ship.x = W / 2;
  document.getElementById('score').textContent = 0;
  document.getElementById('lives').textContent = 3;
}

// جلوگیری از اسکرول و زوم
document.addEventListener('gesturestart', e => e.preventDefault());
document.body.addEventListener('touchmove', e => e.preventDefault(), {passive: false});

// ========== حلقه بازی ==========
function loop() {
  update();
  render();
  requestAnimationFrame(loop);
}
loop();
</script>
</body>
</html>

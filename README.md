<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>سفینه فضایی</title>
<style>
  * { margin:0; padding:0; box-sizing:border-box; font-family:Tahoma, sans-serif; }
  body { background:#050914; color:#e0e6ff; overflow:hidden; user-select:none; }

  /* ===== نوار بالا ===== */
  #topbar {
    position:fixed; top:0; left:0; right:0; height:52px;
    background:linear-gradient(90deg,#0b1230,#1a2450,#0b1230);
    border-bottom:2px solid #3a5bd9;
    display:flex; align-items:center; justify-content:center; gap:10px;
    z-index:100;
  }
  #topbar button {
    background:transparent; border:2px solid #3a5bd9; color:#9fb4ff;
    padding:8px 30px; border-radius:8px; cursor:pointer; font-size:16px;
    transition:.2s;
  }
  #topbar button.active { background:#3a5bd9; color:#fff; box-shadow:0 0 15px #3a5bd9; }
  #topbar button:hover { background:#1c2a60; }

  /* ===== HUD ===== */
  #hud {
    position:fixed; top:52px; left:0; right:0; height:56px;
    display:flex; justify-content:space-between; align-items:center;
    padding:0 20px; z-index:99; pointer-events:none;
  }
  #hudLeft { display:flex; flex-direction:column; gap:4px; }
  #scoreLine { font-size:16px; color:#7fffd4; }
  #coinLine { font-size:16px; color:#ffd700; }
  #lives { font-size:22px; letter-spacing:4px; }

  /* ===== صحنه بازی ===== */
  #gameWrap { position:fixed; top:0; left:0; width:100%; height:100%; }
  canvas { display:block; width:100%; height:100%; }

  /* ===== فروشگاه ===== */
  #shop {
    position:fixed; top:108px; left:0; right:0; bottom:0;
    overflow-y:auto; padding:20px; display:none; z-index:98;
    background:#050914;
  }
  #shop h2 { text-align:center; color:#9fb4ff; margin-bottom:16px; font-size:20px; }
  #shopTabs { display:flex; justify-content:center; gap:10px; margin-bottom:18px; }
  #shopTabs button {
    background:#0e1633; border:2px solid #3a5bd9; color:#9fb4ff;
    padding:8px 24px; border-radius:8px; cursor:pointer; font-size:15px;
  }
  #shopTabs button.active { background:#3a5bd9; color:#fff; }
  .cards { display:flex; flex-wrap:wrap; gap:16px; justify-content:center; max-width:1000px; margin:0 auto; }
  .card {
    background:linear-gradient(160deg,#0e1633,#141f45);
    border:2px solid #2a3a70; border-radius:14px; padding:16px;
    width:220px; text-align:center; transition:.2s;
  }
  .card.equipped { border-color:#7fffd4; box-shadow:0 0 18px rgba(127,255,212,.35); }
  .card h3 { color:#c7d4ff; margin-bottom:8px; font-size:17px; }
  .card .spec { font-size:13px; color:#8fa3d6; line-height:1.7; margin-bottom:8px; }
  .card .buy { font-size:13px; color:#ffd700; margin-bottom:8px; }
  .card button {
    width:100%; padding:8px; margin-top:6px; border-radius:8px; cursor:pointer;
    border:1px solid #3a5bd9; background:#16224d; color:#9fb4ff; font-size:13px;
    transition:.2s;
  }
  .card button:hover:not(:disabled) { background:#24337a; }
  .card button:disabled { opacity:.4; cursor:not-allowed; }
  .card button.equipBtn { background:#1d5c46; border-color:#2ecc8f; color:#7fffd4; }
  .card button.upBtn { background:#5c4a1d; border-color:#d9b23a; color:#ffd966; }
  .card .badge { display:inline-block; background:#2ecc8f; color:#04231a; font-size:11px;
    padding:2px 10px; border-radius:10px; margin-bottom:6px; }

  /* ===== گیم اور ===== */
  #gameOver {
    position:fixed; inset:0; background:rgba(3,6,16,.92); display:none;
    flex-direction:column; align-items:center; justify-content:center; z-index:200; gap:18px;
  }
  #gameOver h1 { color:#ff5c7a; font-size:34px; }
  #gameOver p { color:#9fb4ff; font-size:18px; }
  #gameOver button {
    padding:12px 40px; font-size:17px; border-radius:10px; cursor:pointer;
    background:#3a5bd9; border:none; color:#fff;
  }
</style>
</head>
<body>

<!-- نوار بالا -->
<div id="topbar">
  <button id="btnGame" class="active">🎮 بازی</button>
  <button id="btnShop">🛒 فروشگاه</button>
</div>

<!-- HUD -->
<div id="hud">
  <div id="hudLeft">
    <div id="scoreLine">امتیاز این دست: <span id="scoreVal">0</span></div>
    <div id="coinLine">💰 <span id="coinVal">0</span></div>
  </div>
  <div id="lives">❤️❤️❤️</div>
</div>

<!-- صحنه بازی -->
<div id="gameWrap">
  <canvas id="game"></canvas>
</div>

<!-- فروشگاه -->
<div id="shop">
  <div id="shopTabs">
    <button id="tabWeapons" class="active">🔫 اسلحه‌ها</button>
    <button id="tabShips">🚀 سفینه‌ها</button>
  </div>
  <div id="shopContent" class="cards"></div>
</div>

<!-- گیم اور -->
<div id="gameOver">
  <h1>💥 بازی تمام شد!</h1>
  <p>امتیاز این دست: <span id="finalScore">0</span></p>
  <button id="restartBtn">شروع دوباره</button>
</div>

<script>
/* ================== داده‌ها ================== */
const WEAPONS = [
  { id:'laser', name:'لیزر ساده', dmg:1, shots:1, cost:0, upCost:200, upDmg:1 },
  { id:'twin', name:'شلیک دوقلو', dmg:1, shots:2, cost:100, upCost:200, upDmg:1 },
  { id:'red', name:'لیزر سرخ', dmg:2, shots:1, cost:300, upCost:200, upDmg:1 },
  { id:'triple', name:'سه گانه', dmg:1, shots:3, cost:500, upCost:200, upDmg:1 },
  { id:'plasma', name:'پلاسما', dmg:4, shots:1, cost:1000, upCost:200, upDmg:1 },
];

const SHIPS = [
  { id:'redwing', name:'بال قرمز', cost:0, color:'#ff4d5e', accent:'#ff9aa5' },
  { id:'falcon', name:'شاهین کهکشانی', cost:500, color:'#4da6ff', accent:'#a5d4ff' },
  { id:'purple', name:'شبح بنفش', cost:1000, color:'#a855f7', accent:'#d8b4fe' },
  { id:'golden', name:'سپر طلایی', cost:2000, color:'#ffd700', accent:'#fff3b0' },
  { id:'crystal', name:'ستاره کریستالی', cost:5000, color:'#7fffd4', accent:'#e0fff6' },
];

/* ================== وضعیت بازیکن ================== */
const state = {
  coins: 0,
  score: 0,
  lives: 3,
  ownedWeapons: ['laser'],
  weaponLevels: { laser:0 },
  equippedWeapon: 'laser',
  ownedShips: ['redwing'],
  equippedShip: 'redwing',
  running: true,
  gameOver: false,
};

/* ================== ذخیره‌سازی ================== */
function save(){
  localStorage.setItem('spaceGameSave', JSON.stringify({
    coins:state.coins, ownedWeapons:state.ownedWeapons,
    weaponLevels:state.weaponLevels, equippedWeapon:state.equippedWeapon,
    ownedShips:state.ownedShips, equippedShip:state.equippedShip
  }));
}
function load(){
  const raw = localStorage.getItem('spaceGameSave');
  if(!raw) return;
  try{
    const d = JSON.parse(raw);
    Object.assign(state, d);
    if(!state.ownedWeapons.includes('laser')) state.ownedWeapons.push('laser');
    if(!state.ownedShips.includes('redwing')) state.ownedShips.push('redwing');
  }catch(e){}
}
load();

/* ================== عناصر DOM ================== */
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const shopEl = document.getElementById('shop');
const gameWrap = document.getElementById('gameWrap');
const hudEl = document.getElementById('hud');
const topbarEl = document.getElementById('topbar');

/* ================== ابزار ================== */
function weaponStats(id){
  const w = WEAPONS.find(x=>x.id===id);
  const lvl = state.weaponLevels[id] || 0;
  return { dmg: w.dmg + lvl*w.upDmg, shots: w.shots };
}
function currentShip(){ return SHIPS.find(s=>s.id===state.equippedShip); }

/* ================== بازی ================== */
let W=0, H=0;
function resize(){
  W = canvas.width = window.innerWidth;
  H = canvas.height = window.innerHeight;
}
window.addEventListener('resize', resize);
resize();

const player = { x:0, y:0, w:70, h:40, speed:6, cooldown:0 };
let bullets = [];   // گلوله‌های بازیکن
let enemies = [];   // دشمن‌ها
let particles = [];
let keys = {};
let lastShot = 0;
let enemyTimer = 0;
let spawnInterval = 1100;

document.addEventListener('keydown', e=>{
  keys[e.key]=true;
  if(e.key===' ') e.preventDefault();
});
document.addEventListener('keyup', e=> keys[e.key]=false);

function resetGame(){
  state.score = 0;
  state.lives = 3;
  state.running = true;
  state.gameOver = false;
  bullets=[]; enemies=[]; particles=[];
  enemyTimer=0; spawnInterval=1100;
  player.x = W/2;
  player.y = H - 100;
  player.cooldown = 0;
  document.getElementById('gameOver').style.display='none';
  updateHUD();
}
resetGame();

function updateHUD(){
  document.getElementById('scoreVal').textContent = state.score;
  document.getElementById('coinVal').textContent = state.coins;
  let s='';
  for(let i=0;i<state.lives;i++) s+='❤️';
  for(let i=state.lives;i<3;i++) s+='🖤';
  document.getElementById('lives').textContent = s;
}

/* ------- شلیک ------- */
function shoot(){
  const st = weaponStats(state.equippedWeapon);
  const n = st.shots;
  for(let i=0;i<n;i++){
    let ox = 0;
    if(n===2) ox = (i===0? -12 : 12);
    if(n===3) ox = (i-1)*14;
    bullets.push({
      x: player.x + ox,
      y: player.y - player.h/2,
      vy: -13,
      dmg: st.dmg,
      r: 4
    });
  }
}

/* ------- دشمن (شش‌ضلعی) ------- */
function spawnEnemy(){
  const r = 16 + Math.random()*10;
  enemies.push({
    x: r + Math.random()*(W-2*r),
    y: -r,
    r,
    vy: 1.6 + Math.random()*1.2 + (state.score/1500), // کمی سریع‌تر با امتیاز
    passed: false,
    hitFlash: 0
  });
}

/* ------- ذرات انفجار ------- */
function explode(x,y,color){
  for(let i=0;i<10;i++){
    const a = Math.random()*Math.PI*2;
    const sp = 1+Math.random()*3;
    particles.push({x,y,vx:Math.cos(a)*sp,vy:Math.sin(a)*sp,life:1,color});
  }
}

/* ================== حلقه بازی ================== */
let last = performance.now();
function loop(now){
  const dt = Math.min(50, now-last); last = now;
  if(state.running) update(dt);
  render();
  requestAnimationFrame(loop);
}

function update(dt){
  const f = dt/16.67;

  // حرکت بازیکن
  if(keys['ArrowLeft']||keys['a']||keys['A']) player.x -= player.speed*f;
  if(keys['ArrowRight']||keys['d']||keys['D']) player.x += player.speed*f;
  if(keys['ArrowUp']||keys['w']||keys['W']) player.y -= player.speed*f;
  if(keys['ArrowDown']||keys['s']||keys['S']) player.y += player.speed*f;
  player.x = Math.max(player.w/2, Math.min(W-player.w/2, player.x));
  player.y = Math.max(H*0.35, Math.min(H-player.h/2-10, player.y));

  // شلیک
  player.cooldown -= dt;
  if((keys[' ']||keys['Spacebar']) && player.cooldown<=0){
    shoot();
    player.cooldown = 180;
  }

  // گلوله‌ها
  for(let i=bullets.length-1;i>=0;i--){
    const b = bullets[i];
    b.y += b.vy*f;
    if(b.y < -20) bullets.splice(i,1);
  }

  // دشمن‌ها
  enemyTimer -= dt;
  if(enemyTimer<=0){
    spawnEnemy();
    spawnInterval = Math.max(350, 1100 - state.score*0.6);
    enemyTimer = spawnInterval;
  }

  for(let i=enemies.length-1;i>=0;i--){
    const e = enemies[i];
    e.y += e.vy*f;
    if(e.hitFlash>0) e.hitFlash-=dt;

    // برخورد با بازیکن
    const dx = e.x - player.x, dy = e.y - player.y;
    if(Math.sqrt(dx*dx+dy*dy) < e.r + player.w*0.35){
      explode(e.x,e.y,'#ff4d5e');
      enemies.splice(i,1);
      loseLife();
      continue;
    }

    // رد شدن از پایین (از سفینه رد شد)
    if(e.y - e.r > H){
      enemies.splice(i,1);
      loseLife();
      continue;
    }

    // برخورد گلوله
    for(let j=bullets.length-1;j>=0;j--){
      const b = bullets[j];
      const bdx = b.x-e.x, bdy = b.y-e.y;
      if(Math.sqrt(bdx*bdx+bdy*bdy) < e.r + b.r){
        bullets.splice(j,1);
        e.hp = (e.hp??1) - b.dmg;
        if(e.hp<=0){
          explode(e.x,e.y,'#7fffd4');
          enemies.splice(i,1);
          state.score += 10;
          state.coins += 2;
          save(); updateHUD();
        } else {
          e.hitFlash = 100;
        }
        break;
      }
    }
  }

  // ذرات
  for(let i=particles.length-1;i>=0;i--){
    const p = particles[i];
    p.x+=p.vx*f; p.y+=p.vy*f; p.life-=0.03*f;
    if(p.life<=0) particles.splice(i,1);
  }
}

function loseLife(){
  if(!state.running) return;
  state.lives--;
  updateHUD();
  if(state.lives<=0){
    state.lives=0;
    gameOver();
  }
}

function gameOver(){
  state.running=false;
  state.gameOver=true;
  document.getElementById('finalScore').textContent = state.score;
  document.getElementById('gameOver').style.display='flex';
  save();
}

/* ================== رندر ================== */
function drawShip(x,y,ship,scale=1){
  ctx.save();
  ctx.translate(x,y);
  ctx.scale(scale,scale);

  // بدنه اصلی مثلثی/بالدار شبیه B-2
  ctx.beginPath();
  ctx.moveTo(0,-22);          // دماغه
  ctx.lineTo(34,14);          // بال راست
  ctx.lineTo(12,10);
  ctx.lineTo(6,20);
  ctx.lineTo(-6,20);
  ctx.lineTo(-12,10);
  ctx.lineTo(-34,14);         // بال چپ
  ctx.closePath();
  ctx.fillStyle = ship.color;
  ctx.shadowColor = ship.color;
  ctx.shadowBlur = 18;
  ctx.fill();
  ctx.shadowBlur = 0;

  // خطوط تزئینی
  ctx.beginPath();
  ctx.moveTo(0,-14);
  ctx.lineTo(18,10);
  ctx.moveTo(0,-14);
  ctx.lineTo(-18,10);
  ctx.strokeStyle = ship.accent;
  ctx.lineWidth = 2;
  ctx.stroke();

  // کابین
  ctx.beginPath();
  ctx.ellipse(0,-6,6,9,0,0,Math.PI*2);
  ctx.fillStyle = ship.accent;
  ctx.globalAlpha = .8;
  ctx.fill();
  ctx.globalAlpha = 1;

  // شعله موتور
  ctx.beginPath();
  ctx.moveTo(-6,20);
  ctx.lineTo(0,20+Math.random()*10);
  ctx.lineTo(6,20);
  ctx.fillStyle = '#ffb347';
  ctx.globalAlpha = .8;
  ctx.fill();
  ctx.globalAlpha = 1;

  ctx.restore();
}

function drawEnemy(e){
  ctx.save();
  ctx.translate(e.x,e.y);

  // شش‌ضلعی
  ctx.beginPath();
  for(let i=0;i<6;i++){
    const a = Math.PI/3*i - Math.PI/6;
    const px = Math.cos(a)*e.r;
    const py = Math.sin(a)*e.r;
    if(i===0) ctx.moveTo(px,py); else ctx.lineTo(px,py);
  }
  ctx.closePath();
  ctx.fillStyle = e.hitFlash>0 ? '#ffffff' : '#c0392b';
  ctx.strokeStyle = '#ff7b6b';
  ctx.lineWidth = 2;
  ctx.shadowColor = '#ff3b3b';
  ctx.shadowBlur = 12;
  ctx.fill();
  ctx.stroke();
  ctx.shadowBlur = 0;

  // هسته
  ctx.beginPath();
  ctx.arc(0,0,e.r*0.3,0,Math.PI*2);
  ctx.fillStyle = '#ffb3b3';
  ctx.fill();

  ctx.restore();
}

function render(){
  // پس‌زمینه ستاره‌ای
  ctx.fillStyle = '#050914';
  ctx.fillRect(0,0,W,H);

  // ستاره‌ها
  ctx.save();
  for(let i=0;i<80;i++){
    const sx = (i*137.5)%W;
    const sy = (i*89.3 + performance.now()*0.02*(1+i%3))%H;
    ctx.globalAlpha = 0.3 + (i%5)*0.12;
    ctx.fillStyle = '#bcd2ff';
    ctx.fillRect(sx, sy, 1.5, 1.5);
  }
  ctx.restore();

  if(state.gameOver){
    // همچنان سفینه و محیط را نشان بده
  }

  // ذرات
  particles.forEach(p=>{
    ctx.globalAlpha = p.life;
    ctx.fillStyle = p.color;
    ctx.beginPath();
    ctx.arc(p.x,p.y,3*p.life,0,Math.PI*2);
    ctx.fill();
  });
  ctx.globalAlpha = 1;

  // گلوله‌ها
  bullets.forEach(b=>{
    ctx.save();
    ctx.shadowColor = '#7fffd4';
    ctx.shadowBlur = 12;
    ctx.fillStyle = '#a5ffe9';
    ctx.beginPath();
    ctx.ellipse(b.x,b.y,b.r, b.r*2.4, 0, 0, Math.PI*2);
    ctx.fill();
    ctx.restore();
  });

  // دشمن‌ها
  enemies.forEach(drawEnemy);

  // بازیکن
  if(!state.gameOver || state.lives>0){
    drawShip(player.x, player.y, currentShip());
  }
}

requestAnimationFrame(loop);

/* ================== فروشگاه ================== */
let shopTab = 'weapons';

function renderShop(){
  const content = document.getElementById('shopContent');
  content.innerHTML = '';

  if(shopTab==='weapons'){
    WEAPONS.forEach(w=>{
      const owned = state.ownedWeapons.includes(w.id);
      const equipped = state.equippedWeapon===w.id;
      const lvl = state.weaponLevels[w.id] || 0;
      const st = weaponStats(w.id);

      const card = document.createElement('div');
      card.className = 'card' + (equipped? ' equipped':'');

      let html = `<h3>${w.name}</h3>`;
      if(equipped) html += `<div class="badge">✔ مجهز شده</div>`;
      html += `<div class="spec">دمیج: ${st.dmg}<br>تعداد تیر: ${st.shots}`;
      if(lvl>0) html += `<br><span style="color:#ffd966">سطح ارتقا: ${lvl}</span>`;
      html += `</div>`;

      if(!owned){
        html += `<div class="buy">هزینه خرید: ${w.cost} سکه</div>`;
        html += `<button class="buyBtn" data-w="${w.id}">خرید (${w.cost})</button>`;
      } else {
        if(!equipped){
          html += `<button class="equipBtn" data-w="${w.id}">مجهز کردن</button>`;
        }
        html += `<div class="buy">هزینه ارتقا: ${w.upCost} سکه</div>`;
        html += `<button class="upBtn" data-w="${w.id}">ارتقا (${w.upCost})</button>`;
      }

      card.innerHTML = html;
      content.appendChild(card);
    });
  } else {
    SHIPS.forEach(s=>{
      const owned = state.ownedShips.includes(s.id);
      const equipped = state.equippedShip===s.id;

      const card = document.createElement('div');
      card.className = 'card' + (equipped? ' equipped':'');

      // پیش‌نمایش کوچک سفینه روی کارت
      let html = `<canvas class="preview" width="120" height="70" data-ship="${s.id}"></canvas>`;
      html += `<h3>${s.name}</h3>`;
      if(equipped) html += `<div class="badge">✔ مجهز شده</div>`;

      if(!owned){
        html += `<div class="buy">هزینه خرید: ${s.cost} سکه</div>`;
        html += `<button class="buyShipBtn" data-s="${s.id}">خرید (${s.cost})</button>`;
      } else if(!equipped){
        html += `<button class="equipShipBtn" data-s="${s.id}">مجهز کردن</button>`;
      } else {
        html += `<button disabled>مجهز شده</button>`;
      }

      card.innerHTML = html;
      content.appendChild(card);

      // کشیدن پیش‌نمایش
      const cv = card.querySelector('canvas.preview');
      const c2 = cv.getContext('2d');
      c2.clearRect(0,0,120,70);
      // پس‌زمینه
      c2.fillStyle = '#0a1130';
      c2.fillRect(0,0,120,70);
      // سفینه با drawShip ولی روی بوم دیگر
      drawShipOn(c2, 60, 38, s, 1.1);
    });
  }
}

// نسخه‌ای از drawShip برای بوم‌های دیگر
function drawShipOn(c, x,y,ship,scale=1){
  c.save();
  c.translate(x,y);
  c.scale(scale,scale);
  c.beginPath();
  c.moveTo(0,-22);
  c.lineTo(34,14);
  c.lineTo(12,10);
  c.lineTo(6,20);
  c.lineTo(-6,20);
  c.lineTo(-12,10);
  c.lineTo(-34,14);
  c.closePath();
  c.fillStyle = ship.color;
  c.shadowColor = ship.color;
  c.shadowBlur = 15;
  c.fill();
  c.shadowBlur = 0;
  c.beginPath();
  c.moveTo(0,-14); c.lineTo(18,10);
  c.moveTo(0,-14); c.lineTo(-18,10);
  c.strokeStyle = ship.accent; c.lineWidth=2; c.stroke();
  c.beginPath();
  c.ellipse(0,-6,6,9,0,0,Math.PI*2);
  c.fillStyle = ship.accent; c.globalAlpha=.8; c.fill(); c.globalAlpha=1;
  c.restore();
}

// کلیک در فروشگاه
document.getElementById('shopContent').addEventListener('click', e=>{
  const t = e.target;
  if(t.classList.contains('buyBtn')){
    const id = t.dataset.w;
    const w = WEAPONS.find(x=>x.id===id);
    if(state.coins >= w.cost){
      state.coins -= w.cost;
      state.ownedWeapons.push(id);
      state.weaponLevels[id] = 0;
      state.equippedWeapon = id; // بعد از خرید مجهز شود
      save(); updateHUD(); renderShop();
    } else {
      alert('سکه کافی نیست!');
    }
  }
  else if(t.classList.contains('equipBtn')){
    state.equippedWeapon = t.dataset.w;
    save(); renderShop();
  }
  else if(t.classList.contains('upBtn')){
    const id = t.dataset.w;
    const w = WEAPONS.find(x=>x.id===id);
    if(state.coins >= w.upCost){
      state.coins -= w.upCost;
      state.weaponLevels[id] = (state.weaponLevels[id]||0)+1;
      save(); updateHUD(); renderShop();
    } else {
      alert('سکه کافی نیست!');
    }
  }
  else if(t.classList.contains('buyShipBtn')){
    const id = t.dataset.s;
    const s = SHIPS.find(x=>x.id===id);
    if(state.coins >= s.cost){
      state.coins -= s.cost;
      state.ownedShips.push(id);
      state.equippedShip = id;
      save(); updateHUD(); renderShop();
    } else {
      alert('سکه کافی نیست!');
    }
  }
  else if(t.classList.contains('equipShipBtn')){
    state.equippedShip = t.dataset.s;
    save(); renderShop();
  }
});

/* ================== تب‌ها ================== */
document.getElementById('btnGame').addEventListener('click', ()=>{
  document.getElementById('btnGame').classList.add('active');
  document.getElementById('btnShop').classList.remove('active');
  shopEl.style.display='none';
  gameWrap.style.display='block';
  hudEl.style.display='flex';
});
document.getElementById('btnShop').addEventListener('click', ()=>{
  document.getElementById('btnShop').classList.add('active');
  document.getElementById('btnGame').classList.remove('active');
  shopEl.style.display='block';
  gameWrap.style.display='none';
  hudEl.style.display='none';
  renderShop();
});
document.getElementById('tabWeapons').addEventListener('click', ()=>{
  shopTab='weapons';
  document.getElementById('tabWeapons').classList.add('active');
  document.getElementById('tabShips').classList.remove('active');
  renderShop();
});
document.getElementById('tabShips').addEventListener('click', ()=>{
  shopTab='ships';
  document.getElementById('tabShips').classList.add('active');
  document.getElementById('tabWeapons').classList.remove('active');
  renderShop();
});

/* ================== ریستارت ================== */
document.getElementById('restartBtn').addEventListener('click', ()=>{
  resetGame();
});

/* ================== شروع ================== */
updateHUD();
</script>
</body>
</html>

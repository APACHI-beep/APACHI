<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="mobile-web-app-capable" content="yes">
<title>سفینه فضایی</title>
<style>
  * { margin:0; padding:0; box-sizing:border-box; font-family:Tahoma, sans-serif;
      -webkit-tap-highlight-color:transparent; -webkit-touch-callout:none; }
  html, body { width:100%; height:100%; overflow:hidden; background:#050914; color:#e0e6ff;
      user-select:none; -webkit-user-select:none; touch-action:none; }

  #topbar {
    position:fixed; top:0; left:0; right:0; height:46px;
    background:linear-gradient(90deg,#0b1230,#1a2450,#0b1230);
    border-bottom:2px solid #3a5bd9;
    display:flex; align-items:center; justify-content:center; gap:8px;
    z-index:100; padding-top: env(safe-area-inset-top);
  }
  #topbar button {
    background:transparent; border:2px solid #3a5bd9; color:#9fb4ff;
    padding:6px 22px; border-radius:8px; cursor:pointer; font-size:14px;
    transition:.2s;
  }
  #topbar button.active { background:#3a5bd9; color:#fff; box-shadow:0 0 12px #3a5bd9; }

  #hud {
    position:fixed; top:calc(46px + env(safe-area-inset-top)); left:0; right:0; height:46px;
    display:flex; justify-content:space-between; align-items:center;
    padding:0 12px; z-index:99; pointer-events:none;
  }
  #hudLeft { display:flex; flex-direction:column; gap:2px; }
  #scoreLine { font-size:13px; color:#7fffd4; }
  #coinLine { font-size:13px; color:#ffd700; }
  #lives { font-size:18px; letter-spacing:2px; }

  #gameWrap { position:fixed; inset:0; }
  canvas#game { display:block; width:100%; height:100%; touch-action:none; }

  #shop {
    position:fixed; top:calc(92px + env(safe-area-inset-top)); left:0; right:0; bottom:0;
    overflow-y:auto; -webkit-overflow-scrolling:touch; padding:12px;
    display:none; z-index:98; background:#050914; touch-action:pan-y;
  }
  #shopTabs { display:flex; justify-content:center; gap:8px; margin-bottom:12px; }
  #shopTabs button {
    background:#0e1633; border:2px solid #3a5bd9; color:#9fb4ff;
    padding:8px 20px; border-radius:8px; cursor:pointer; font-size:13px;
  }
  #shopTabs button.active { background:#3a5bd9; color:#fff; }
  .cards { display:flex; flex-direction:column; gap:12px; max-width:560px; margin:0 auto; }

  .wcard {
    background:linear-gradient(160deg,#0e1633,#141f45);
    border:2px solid #2a3a70; border-radius:12px; padding:12px 14px; transition:.2s;
  }
  .wcard.equipped { border-color:#7fffd4; box-shadow:0 0 15px rgba(127,255,212,.3); }
  .wcard .wtitle { color:#c7d4ff; font-size:15px; margin-bottom:8px; display:flex; align-items:center; gap:8px; }
  .wcard .wbody { display:flex; justify-content:space-between; align-items:center; gap:10px; }
  .wcard .wstats { flex:1; font-size:12px; color:#8fa3d6; line-height:1.8; }
  .wcard .wstats b { color:#7fffd4; }
  .wcard .wstats .lvl { color:#ffd966; }
  .wcard .wactions { display:flex; flex-direction:column; gap:6px; min-width:120px; }
  .wcard .wactions button {
    padding:8px 10px; border-radius:8px; cursor:pointer; font-size:12px;
    border:1px solid #3a5bd9; background:#16224d; color:#9fb4ff; transition:.2s; white-space:nowrap;
  }
  .wcard .wactions button.equipBtn { background:#1d5c46; border-color:#2ecc8f; color:#7fffd4; }
  .wcard .wactions button.upBtn { background:#5c4a1d; border-color:#d9b23a; color:#ffd966; }
  .wcard .wactions button.buyBtn { background:#3a1d5c; border-color:#a855f7; color:#d8b4fe; }
  .wcard .wactions button:disabled { opacity:.45; }
  .badge { display:inline-block; background:#2ecc8f; color:#04231a; font-size:11px;
    padding:2px 10px; border-radius:10px; }

  .scard {
    background:linear-gradient(160deg,#0e1633,#141f45);
    border:2px solid #2a3a70; border-radius:12px; padding:12px 14px;
    display:flex; align-items:center; gap:12px; transition:.2s;
  }
  .scard.equipped { border-color:#7fffd4; box-shadow:0 0 15px rgba(127,255,212,.3); }
  .scard .preview { flex:0 0 110px; display:block; }
  .scard .sinfo { flex:1; display:flex; flex-direction:column; gap:6px; text-align:right; }
  .scard .sname { color:#c7d4ff; font-size:15px; display:flex; align-items:center; gap:6px; flex-wrap:wrap; }
  .scard .sprice { color:#ffd700; font-size:12px; }
  .scard .sactions { display:flex; flex-direction:column; gap:6px; min-width:110px; }
  .scard .sactions button {
    padding:8px 10px; border-radius:8px; cursor:pointer; font-size:12px;
    border:1px solid #3a5bd9; background:#16224d; color:#9fb4ff; transition:.2s; white-space:nowrap;
  }
  .scard .sactions button.equipShipBtn { background:#1d5c46; border-color:#2ecc8f; color:#7fffd4; }
  .scard .sactions button.buyShipBtn { background:#3a1d5c; border-color:#a855f7; color:#d8b4fe; }
  .scard .sactions button:disabled { opacity:.45; }

  #gameOver {
    position:fixed; inset:0; background:rgba(3,6,16,.94); display:none;
    flex-direction:column; align-items:center; justify-content:center; z-index:200; gap:16px;
    padding:20px; text-align:center;
  }
  #gameOver h1 { color:#ff5c7a; font-size:26px; }
  #gameOver p { color:#9fb4ff; font-size:16px; }
  #gameOver button {
    padding:14px 44px; font-size:16px; border-radius:10px; cursor:pointer;
    background:#3a5bd9; border:none; color:#fff;
  }

  #hint {
    position:fixed; bottom:16px; left:0; right:0; text-align:center;
    font-size:12px; color:#5a6da3; z-index:50; pointer-events:none;
    animation: fadeHint 6s forwards;
  }
  @keyframes fadeHint { 0%,60%{opacity:1} 100%{opacity:0} }
</style>
</head>
<body>

<div id="topbar">
  <button id="btnGame" class="active">🎮 بازی</button>
  <button id="btnShop">🛒 فروشگاه</button>
</div>

<div id="hud">
  <div id="hudLeft">
    <div id="scoreLine">امتیاز این دست: <span id="scoreVal">0</span></div>
    <div id="coinLine">💰 <span id="coinVal">0</span></div>
  </div>
  <div id="lives">❤️❤️❤️</div>
</div>

<div id="gameWrap"><canvas id="game"></canvas></div>
<div id="hint">👆 انگشتت رو روی صفحه بکش تا حرکت کنی و شلیک کنی</div>

<div id="shop">
  <div id="shopTabs">
    <button id="tabWeapons" class="active">🔫 اسلحه‌ها</button>
    <button id="tabShips">🚀 سفینه‌ها</button>
  </div>
  <div id="shopContent" class="cards"></div>
</div>

<div id="gameOver">
  <h1>💥 بازی تمام شد!</h1>
  <p>امتیاز این دست: <span id="finalScore">0</span></p>
  <button id="restartBtn">شروع دوباره</button>
</div>

<script>
/* ================== داده‌ها ================== */
const WEAPONS = [
  { id:'laser',  name:'لیزر ساده',  dmg:1, shots:1, cost:0,    upCost:200,  upDmg:1 },
  { id:'twin',   name:'شلیک دوقلو', dmg:1, shots:2, cost:100,  upCost:200,  upDmg:1 },
  { id:'red',    name:'لیزر سرخ',   dmg:2, shots:1, cost:300,  upCost:200,  upDmg:1 },
  { id:'triple', name:'سه گانه',    dmg:1, shots:3, cost:500,  upCost:200,  upDmg:1 },
  { id:'plasma', name:'پلاسما',     dmg:4, shots:1, cost:1000, upCost:200,  upDmg:2 },
];

/* هر سفینه یک نوع شکل کاملاً متفاوت دارد */
const SHIPS = [
  { id:'redwing', name:'بال قرمز',       cost:0,    color:'#ff4d5e', accent:'#ff9aa5', type:'classic',  scale:1.0 },
  { id:'falcon',  name:'شاهین کهکشانی',  cost:500,  color:'#4da6ff', accent:'#a5d4ff', type:'arrow',    scale:1.1 },
  { id:'purple',  name:'شبح بنفش',       cost:1000, color:'#a855f7', accent:'#d8b4fe', type:'phantom',  scale:0.95 },
  { id:'golden',  name:'سپر طلایی',      cost:2000, color:'#ffd700', accent:'#fff3b0', type:'shield',   scale:1.15 },
  { id:'crystal', name:'ستاره کریستالی', cost:5000, color:'#7fffd4', accent:'#e0fff6', type:'crystal',  scale:1.2 },
];

/* ================== وضعیت ================== */
const state = {
  coins: Infinity,
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

function save(){
  try{
    localStorage.setItem('spaceGameSave', JSON.stringify({
      ownedWeapons:state.ownedWeapons,
      weaponLevels:state.weaponLevels, equippedWeapon:state.equippedWeapon,
      ownedShips:state.ownedShips, equippedShip:state.equippedShip
    }));
  }catch(e){}
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
state.coins = Infinity;

/* ================== DOM ================== */
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const shopEl = document.getElementById('shop');
const gameWrap = document.getElementById('gameWrap');
const hudEl = document.getElementById('hud');
const hintEl = document.getElementById('hint');

function weaponStats(id){
  const w = WEAPONS.find(x=>x.id===id);
  const lvl = state.weaponLevels[id] || 0;
  return { dmg: w.dmg + lvl*w.upDmg, shots: w.shots, level: lvl };
}
function currentShip(){ return SHIPS.find(s=>s.id===state.equippedShip); }

/* ================== بوم ================== */
let W=0, H=0, DPR = 1;
function resize(){
  DPR = Math.min(window.devicePixelRatio||1, 2);
  W = window.innerWidth;
  H = window.innerHeight;
  canvas.width = Math.floor(W*DPR);
  canvas.height = Math.floor(H*DPR);
  canvas.style.width = W+'px';
  canvas.style.height = H+'px';
  ctx.setTransform(DPR,0,0,DPR,0,0);
}
window.addEventListener('resize', resize);
window.addEventListener('orientationchange', ()=> setTimeout(resize, 200));
resize();

/* ================== بازی ================== */
const player = { x:0, y:0, w:64, h:36 };
let bullets = [];
let enemies = [];
let particles = [];
let lastShot = 0;
let enemyTimer = 0;
let spawnInterval = 1100;

const touchState = { active:false, targetX:0, targetY:0 };

function resetGame(){
  state.score = 0;
  state.lives = 3;
  state.running = true;
  state.gameOver = false;
  bullets=[]; enemies=[]; particles=[];
  enemyTimer=0; spawnInterval=1100;
  player.x = W/2;
  player.y = H - 110;
  touchState.active = false;
  touchState.targetX = player.x;
  touchState.targetY = player.y;
  document.getElementById('gameOver').style.display='none';
  hintEl.style.display='block';
  hintEl.style.animation='none';
  void hintEl.offsetWidth;
  hintEl.style.animation='fadeHint 6s forwards';
  updateHUD();
}
resetGame();

function updateHUD(){
  document.getElementById('scoreVal').textContent = state.score;
  document.getElementById('coinVal').textContent = '∞';
  let s='';
  for(let i=0;i<state.lives;i++) s+='❤️';
  for(let i=state.lives;i<3;i++) s+='🖤';
  document.getElementById('lives').textContent = s;
}

function shoot(){
  const st = weaponStats(state.equippedWeapon);
  const n = st.shots;
  const spread = 12;
  for(let i=0;i<n;i++){
    let ox = 0;
    if(n===2) ox = (i===0? -spread/2 : spread/2);
    if(n===3) ox = (i-1)*spread;
    bullets.push({ x: player.x + ox, y: player.y - 22, vy: -13, dmg: st.dmg, r: 3.5 });
  }
}

function spawnEnemy(){
  const r = 14 + Math.random()*8;
  enemies.push({
    x: r + Math.random()*(W-2*r),
    y: -r, r,
    vy: 1.5 + Math.random()*1.1 + Math.min(2.5, state.score/1800),
    hitFlash: 0, hp: 1
  });
}
function explode(x,y,color){
  for(let i=0;i<9;i++){
    const a = Math.random()*Math.PI*2;
    const sp = 1+Math.random()*2.6;
    particles.push({x,y,vx:Math.cos(a)*sp,vy:Math.sin(a)*sp,life:1,color});
  }
}

/* ================== حلقه ================== */
let last = performance.now();
function loop(now){
  const dt = Math.min(50, now-last); last = now;
  if(state.running) update(dt);
  render();
  requestAnimationFrame(loop);
}

function update(dt){
  const f = dt/16.67;

  if(touchState.active){
    const dx = touchState.targetX - player.x;
    const dy = touchState.targetY - player.y;
    const dist = Math.hypot(dx,dy);
    if(dist > 1){
      const step = Math.min(dist, 14*f);
      player.x += (dx/dist)*step;
      player.y += (dy/dist)*step;
    }
  }
  player.x = Math.max(player.w/2, Math.min(W-player.w/2, player.x));
  player.y = Math.max(H*0.32, Math.min(H-player.h/2-10, player.y));

  if(touchState.active && performance.now() - lastShot > 175){
    shoot();
    lastShot = performance.now();
  }

  for(let i=bullets.length-1;i>=0;i--){
    const b = bullets[i];
    b.y += b.vy*f;
    if(b.y < -20) bullets.splice(i,1);
  }

  enemyTimer -= dt;
  if(enemyTimer<=0){
    spawnEnemy();
    spawnInterval = Math.max(340, 1100 - state.score*0.55);
    enemyTimer = spawnInterval;
  }

  for(let i=enemies.length-1;i>=0;i--){
    const e = enemies[i];
    e.y += e.vy*f;
    if(e.hitFlash>0) e.hitFlash-=dt;

    const ddx = e.x - player.x, ddy = e.y - player.y;
    if(Math.hypot(ddx,ddy) < e.r + player.w*0.32){
      explode(e.x,e.y,'#ff4d5e');
      enemies.splice(i,1);
      loseLife();
      continue;
    }
    if(e.y - e.r > H){
      enemies.splice(i,1);
      loseLife();
      continue;
    }

    let killed = false;
    for(let j=bullets.length-1;j>=0;j--){
      const b = bullets[j];
      if(Math.hypot(b.x-e.x, b.y-e.y) < e.r + b.r){
        bullets.splice(j,1);
        e.hp -= b.dmg;
        if(e.hp<=0){
          explode(e.x,e.y,'#7fffd4');
          enemies.splice(i,1);
          state.score += 10;
          save(); updateHUD();
          killed = true;
        } else {
          e.hitFlash = 100;
        }
        break;
      }
    }
    if(killed) continue;
  }

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
  if(state.lives<=0){ state.lives=0; gameOver(); }
}

function gameOver(){
  state.running=false;
  state.gameOver=true;
  touchState.active=false;
  document.getElementById('finalScore').textContent = state.score;
  document.getElementById('gameOver').style.display='flex';
  save();
}

/* ================== کنترل لمسی - اصلاح‌شده ================== */
/* نکته مهم: preventDefault حذف شد و فقط touch-action:none روی canvas کار می‌کنه */
function setTarget(clientX, clientY){
  touchState.targetX = clientX;
  touchState.targetY = clientY - 55;
}

canvas.addEventListener('touchstart', e=>{
  if(!state.running) return;
  touchState.active = true;
  const t = e.touches[0];
  setTarget(t.clientX, t.clientY);
  lastShot = 0;
}, {passive:true});

canvas.addEventListener('touchmove', e=>{
  if(!state.running) return;
  const t = e.touches[0];
  setTarget(t.clientX, t.clientY);
}, {passive:true});

canvas.addEventListener('touchend', e=>{
  if(e.touches.length===0) touchState.active = false;
}, {passive:true});

canvas.addEventListener('touchcancel', ()=>{ touchState.active = false; }, {passive:true});

canvas.addEventListener('mousedown', e=>{
  if(!state.running) return;
  touchState.active = true;
  setTarget(e.clientX, e.clientY);
  lastShot = 0;
});
canvas.addEventListener('mousemove', e=>{
  if(touchState.active && state.running) setTarget(e.clientX, e.clientY);
});
window.addEventListener('mouseup', ()=> touchState.active=false);

/* ================== رندر سفینه‌ها - هر کدام شکل متفاوت ================== */
function drawShipType(c, ship, withFlame){
  c.save();
  c.scale(ship.scale, ship.scale);

  if(ship.type === 'classic'){
    /* بال قرمز: بدنه مثلثی با بال‌های تیز و کابین گرد */
    c.beginPath();
    c.moveTo(0,-20);
    c.lineTo(30,14);
    c.lineTo(11,9);
    c.lineTo(5,18);
    c.lineTo(-5,18);
    c.lineTo(-11,9);
    c.lineTo(-30,14);
    c.closePath();
    c.fillStyle = ship.color;
    c.shadowColor = ship.color; c.shadowBlur = 16; c.fill(); c.shadowBlur = 0;
    c.beginPath();
    c.moveTo(0,-13); c.lineTo(16,9);
    c.moveTo(0,-13); c.lineTo(-16,9);
    c.strokeStyle = ship.accent; c.lineWidth = 2; c.stroke();
    c.beginPath();
    c.ellipse(0,-5,5,8,0,0,Math.PI*2);
    c.fillStyle = ship.accent; c.globalAlpha=.85; c.fill(); c.globalAlpha=1;
  }
  else if(ship.type === 'arrow'){
    /* شاهین کهکشانی: بدنه باریک و کشیده با بال‌های خیلی پهن و دماغه نوک‌تیز */
    c.beginPath();
    c.moveTo(0,-26);
    c.lineTo(8,-10);
    c.lineTo(38,16);
    c.lineTo(14,12);
    c.lineTo(4,22);
    c.lineTo(-4,22);
    c.lineTo(-14,12);
    c.lineTo(-38,16);
    c.lineTo(-8,-10);
    c.closePath();
    c.fillStyle = ship.color;
    c.shadowColor = ship.color; c.shadowBlur = 16; c.fill(); c.shadowBlur = 0;
    /* خط بال وسط */
    c.beginPath();
    c.moveTo(0,-18); c.lineTo(0,20);
    c.strokeStyle = ship.accent; c.lineWidth = 2; c.stroke();
    /* کابین باریک */
    c.beginPath();
    c.ellipse(0,-8,3.5,6,0,0,Math.PI*2);
    c.fillStyle = '#fff'; c.globalAlpha=.7; c.fill(); c.globalAlpha=1;
  }
  else if(ship.type === 'phantom'){
    /* شبح بنفش: بدنه دایره‌ای و کشیده با دو بال منحنی و بدون کابین */
    c.beginPath();
    c.ellipse(0,0,26,18,0,0,Math.PI*2);
    c.fillStyle = ship.color;
    c.shadowColor = ship.color; c.shadowBlur = 20; c.fill(); c.shadowBlur = 0;
    /* بال‌های منحنی */
    c.beginPath();
    c.moveTo(-26,0);
    c.quadraticCurveTo(-40,-10,-30,14);
    c.quadraticCurveTo(-18,8,-14,6);
    c.closePath();
    c.fillStyle = ship.accent; c.globalAlpha=.9; c.fill(); c.globalAlpha=1;
    c.beginPath();
    c.moveTo(26,0);
    c.quadraticCurveTo(40,-10,30,14);
    c.quadraticCurveTo(18,8,14,6);
    c.closePath();
    c.fillStyle = ship.accent; c.globalAlpha=.9; c.fill(); c.globalAlpha=1;
    /* چشم‌های شبح */
    c.beginPath(); c.arc(-6,-2,3,0,Math.PI*2); c.fillStyle='#fff'; c.globalAlpha=.9; c.fill();
    c.beginPath(); c.arc(6,-2,3,0,Math.PI*2); c.fill();
    c.globalAlpha=1;
  }
  else if(ship.type === 'shield'){
    /* سپر طلایی: بدنه شش‌ضلعی با سپر بزرگ در جلو و بال‌های کوتاه */
    /* سپر جلو */
    c.beginPath();
    c.moveTo(0,-28);
    c.lineTo(18,-14);
    c.lineTo(14,10);
    c.lineTo(0,20);
    c.lineTo(-14,10);
    c.lineTo(-18,-14);
    c.closePath();
    c.fillStyle = ship.accent; c.globalAlpha=.85;
    c.shadowColor = ship.accent; c.shadowBlur = 18; c.fill(); c.shadowBlur = 0;
    c.globalAlpha=1;
    /* بدنه اصلی */
    c.beginPath();
    c.moveTo(0,-18);
    c.lineTo(34,14);
    c.lineTo(12,10);
    c.lineTo(0,20);
    c.lineTo(-12,10);
    c.lineTo(-34,14);
    c.closePath();
    c.fillStyle = ship.color; c.fill();
    /* خطوط سپر */
    c.beginPath();
    c.moveTo(0,-24); c.lineTo(0,16);
    c.moveTo(-12,-6); c.lineTo(12,-6);
    c.strokeStyle='#fff8d0'; c.lineWidth=1.5; c.globalAlpha=.7; c.stroke(); c.globalAlpha=1;
  }
  else if(ship.type === 'crystal'){
    /* ستاره کریستالی: بدنه ستاره‌ای چندضلعی با کریستال‌های جانبی */
    /* کریستال‌های جانبی */
    c.beginPath();
    c.moveTo(-30,10); c.lineTo(-22,-8); c.lineTo(-14,12); c.closePath();
    c.fillStyle = ship.accent; c.globalAlpha=.85; c.fill(); c.globalAlpha=1;
    c.beginPath();
    c.moveTo(30,10); c.lineTo(22,-8); c.lineTo(14,12); c.closePath();
    c.fillStyle = ship.accent; c.globalAlpha=.85; c.fill(); c.globalAlpha=1;
    /* بدنه اصلی چندضلعی */
    c.beginPath();
    c.moveTo(0,-26);
    c.lineTo(10,-8);
    c.lineTo(26,4);
    c.lineTo(14,16);
    c.lineTo(0,22);
    c.lineTo(-14,16);
    c.lineTo(-26,4);
    c.lineTo(-10,-8);
    c.closePath();
    c.fillStyle = ship.color;
    c.shadowColor = ship.color; c.shadowBlur = 22; c.fill(); c.shadowBlur = 0;
    /* خطوط کریستالی */
    c.beginPath();
    c.moveTo(0,-20); c.lineTo(0,16);
    c.moveTo(-18,6); c.lineTo(18,6);
    c.strokeStyle = '#fff'; c.lineWidth = 1.2; c.globalAlpha = .6; c.stroke(); c.globalAlpha=1;
    /* هسته درخشان */
    c.beginPath(); c.arc(0,-2,4,0,Math.PI*2);
    c.fillStyle = '#fff'; c.globalAlpha = .9; c.fill(); c.globalAlpha=1;
  }

  /* شعله موتور - فقط وقتی در حال حرکت است */
  if(withFlame){
    c.beginPath();
    c.moveTo(-5,18);
    c.lineTo(0, 18 + Math.random()*10);
    c.lineTo(5,18);
    c.fillStyle = '#ffb347'; c.globalAlpha = .85; c.fill(); c.globalAlpha = 1;
  }

  c.restore();
}

function drawShip(x,y,ship){
  ctx.save();
  ctx.translate(x,y);
  drawShipType(ctx, ship, true);
  ctx.restore();
}

function drawEnemy(e){
  ctx.save();
  ctx.translate(e.x,e.y);
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
  ctx.shadowColor = '#ff3b3b'; ctx.shadowBlur = 10;
  ctx.fill(); ctx.stroke();
  ctx.shadowBlur = 0;
  ctx.beginPath(); ctx.arc(0,0,e.r*0.3,0,Math.PI*2);
  ctx.fillStyle = '#ffb3b3'; ctx.fill();
  ctx.restore();
}

let starSeed = [];
for(let i=0;i<70;i++) starSeed.push({x:Math.random(), y:Math.random(), s:Math.random()*1.6+0.6, sp:Math.random()*0.02+0.005});

function render(){
  ctx.fillStyle = '#050914';
  ctx.fillRect(0,0,W,H);

  const t = performance.now();
  for(const s of starSeed){
    let y = (s.y*H + t*s.sp) % H;
    ctx.globalAlpha = 0.3 + s.s*0.25;
    ctx.fillStyle = '#bcd2ff';
    ctx.fillRect(s.x*W, y, s.s, s.s);
  }
  ctx.globalAlpha = 1;

  for(const p of particles){
    ctx.globalAlpha = p.life;
    ctx.fillStyle = p.color;
    ctx.beginPath();
    ctx.arc(p.x,p.y,3*p.life,0,Math.PI*2);
    ctx.fill();
  }
  ctx.globalAlpha = 1;

  for(const b of bullets){
    ctx.save();
    ctx.shadowColor = '#7fffd4'; ctx.shadowBlur = 10;
    ctx.fillStyle = '#a5ffe9';
    ctx.beginPath();
    ctx.ellipse(b.x,b.y,b.r, b.r*2.2, 0, 0, Math.PI*2);
    ctx.fill();
    ctx.restore();
  }

  for(const e of enemies) drawEnemy(e);

  if(!state.gameOver || state.lives>0){
    drawShip(player.x, player.y, currentShip());
  }

  if(touchState.active){
    ctx.save();
    ctx.globalAlpha = .35;
    ctx.strokeStyle = '#7fffd4';
    ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.arc(touchState.targetX, touchState.targetY+55, 18, 0, Math.PI*2);
    ctx.stroke();
    ctx.restore();
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
      const st = weaponStats(w.id);

      const card = document.createElement('div');
      card.className = 'wcard' + (equipped? ' equipped':'');

      let actions = '';
      if(!owned){
        actions += `<button class="buyBtn" data-w="${w.id}">خرید (${w.cost})</button>`;
      } else {
        if(!equipped){
          actions += `<button class="equipBtn" data-w="${w.id}">مجهز کردن</button>`;
        }
        actions += `<button class="upBtn" data-w="${w.id}">ارتقا (${w.upCost})</button>`;
      }

      card.innerHTML = `
        <div class="wtitle">
          ${w.name}
          ${equipped? '<span class="badge">✔ مجهز</span>':''}
        </div>
        <div class="wbody">
          <div class="wstats">
            دمیج: <b>${st.dmg}</b><br>
            تعداد تیر: <b>${st.shots}</b>
            ${st.level>0? `<br>سطح ارتقا: <span class="lvl">${st.level}</span>`:''}
          </div>
          <div class="wactions">${actions}</div>
        </div>
      `;
      content.appendChild(card);
    });
  } else {
    SHIPS.forEach

<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>سفینه فضایی</title>
<style>
  * { margin:0; padding:0; box-sizing:border-box; -webkit-tap-highlight-color:transparent; }
  html,body { height:100%; overflow:hidden; font-family:tahoma, sans-serif; background:#050510; color:#fff; user-select:none; -webkit-user-select:none; }
  #app { display:flex; flex-direction:column; height:100vh; height:100dvh; width:100%; }

  /* نوار بالا */
  #topbar { display:flex; height:46px; flex:0 0 46px; background:#101028; border-bottom:2px solid #2a2a5a; z-index:10; }
  #topbar button { flex:1; background:transparent; border:none; color:#8899ff; font-size:15px; font-weight:bold; cursor:pointer; }
  #topbar button.active { color:#fff; background:#1e1e4a; box-shadow:inset 0 -3px 0 #5566ff; }

  /* HUD */
  #hud { display:flex; justify-content:space-between; align-items:flex-start; padding:5px 10px; flex:0 0 auto; background:#0a0a1e; font-size:12px; border-bottom:1px solid #1a1a3a; }
  #hud .left div { margin-bottom:3px; }
  #hud .right { color:#ff5566; font-size:18px; letter-spacing:1px; white-space:nowrap; }

  /* بازی */
  #gameWrap { flex:1 1 auto; position:relative; overflow:hidden; min-height:0; }
  canvas { display:block; width:100%; height:100%; background:#000; touch-action:none; }

  /* فروشگاه */
  #shop { display:none; flex:1 1 auto; overflow-y:auto; overflow-x:hidden; padding:10px; padding-bottom:40px; -webkit-overflow-scrolling:touch; min-height:0; }
  .shopSection { margin-bottom:18px; }
  .shopTitle { font-size:15px; color:#8899ff; margin-bottom:8px; border-bottom:1px solid #2a2a5a; padding-bottom:5px; }
  .card { background:#12122a; border:1px solid #2a2a5a; border-radius:10px; padding:11px; margin-bottom:9px; }
  .card.equipped { border-color:#55ff88; box-shadow:0 0 10px #55ff8855; }
  .card .name { font-size:14px; font-weight:bold; color:#fff; margin-bottom:5px; }
  .card .stats { font-size:11px; color:#aab; margin-bottom:7px; line-height:1.6; }
  .card .badge { color:#55ff88; font-size:11px; margin-bottom:6px; display:inline-block; }
  .card button { background:#2a2a6a; color:#fff; border:none; border-radius:6px; padding:7px 12px; font-size:12px; margin-left:5px; margin-top:4px; cursor:pointer; }
  .card button.buy { background:#2a6a3a; }
  .card button.equip { background:#5a2a6a; }
  .card button:disabled { opacity:0.4; }

  #toast { position:fixed; bottom:25px; left:50%; transform:translateX(-50%); background:#1e1e4a; color:#fff; padding:9px 18px; border-radius:20px; font-size:12px; opacity:0; transition:opacity .3s; pointer-events:none; z-index:99; white-space:nowrap; }
  #toast.show { opacity:1; }
</style>
</head>
<body>
<div id="app">

  <div id="topbar">
    <button id="btnGame" class="active">بازی</button>
    <button id="btnShop">فروشگاه</button>
  </div>

  <div id="hud">
    <div class="left">
      <div>امتیاز این دست: <b id="score">0</b></div>
      <div>💰 <b id="coins">0</b></div>
      <div>🏆 رکورد: <b id="record">0</b></div>
    </div>
    <div class="right" id="lives">❤️❤️❤️</div>
  </div>

  <div id="gameWrap">
    <canvas id="canvas"></canvas>
  </div>

  <div id="shop"></div>

</div>
<div id="toast"></div>

<script>
/* ============ ذخیره‌سازی ============ */
const DB = {
  coins: 0, record: 0,
  ownedWeapons: ['simple'],
  equippedWeapon: 'simple',
  weaponLevel: { simple:1, twin:1, red:1, triple:1, plasma:1 },
  ownedShips: ['redwing'],
  equippedShip: 'redwing',
  load() {
    try {
      const s = JSON.parse(localStorage.getItem('spacegame') || '{}');
      if(s.coins!==undefined) this.coins = s.coins;
      if(s.record!==undefined) this.record = s.record;
      if(Array.isArray(s.ownedWeapons) && s.ownedWeapons.length) this.ownedWeapons = s.ownedWeapons;
      if(s.equippedWeapon) this.equippedWeapon = s.equippedWeapon;
      if(s.weaponLevel) this.weaponLevel = s.weaponLevel;
      if(Array.isArray(s.ownedShips) && s.ownedShips.length) this.ownedShips = s.ownedShips;
      if(s.equippedShip) this.equippedShip = s.equippedShip;
      // اطمینان از وجود همه کلیدها
      for(const k of ['simple','twin','red','triple','plasma']){
        if(!this.weaponLevel[k]) this.weaponLevel[k] = 1;
      }
    } catch(e){ console.error(e); }
  },
  save() {
    try {
      localStorage.setItem('spacegame', JSON.stringify({
        coins:this.coins, record:this.record,
        ownedWeapons:this.ownedWeapons, equippedWeapon:this.equippedWeapon,
        weaponLevel:this.weaponLevel,
        ownedShips:this.ownedShips, equippedShip:this.equippedShip
      }));
    } catch(e){}
  }
};
DB.load();

/* ============ داده‌ها ============ */
const WEAPONS = {
  simple: { name:'لیزر ساده', dmg:1, bullets:1, buyCost:0,   upCost:200 },
  twin:   { name:'شلیک دوقلو', dmg:1, bullets:2, buyCost:100, upCost:200 },
  red:    { name:'لیزر سرخ',  dmg:2, bullets:1, buyCost:300, upCost:200 },
  triple: { name:'سه گانه',   dmg:1, bullets:3, buyCost:500, upCost:200 },
  plasma: { name:'پلاسما',    dmg:4, bullets:1, buyCost:1000,upCost:200 },
};
const WEAPON_ORDER = ['simple','twin','red','triple','plasma'];

const SHIPS = {
  redwing:    { name:'بال قرمز',       cost:0,    color:'#ff4466', style:'arrow' },
  hawk:       { name:'شاهین کهکشانی',  cost:500,  color:'#44aaff', style:'hawk' },
  purple:     { name:'شبح بنفش',       cost:1000, color:'#aa44ff', style:'ghost' },
  goldshield: { name:'سپر طلایی',      cost:2000, color:'#ffcc33', style:'shield' },
  crystal:    { name:'ستاره کریستالی', cost:5000, color:'#66ffdd', style:'star' },
};
const SHIP_ORDER = ['redwing','hawk','purple','goldshield','crystal'];

function weaponStats(key){
  const base = WEAPONS[key];
  const lvl = DB.weaponLevel[key] || 1;
  return { dmg: base.dmg * lvl, bullets: base.bullets, name: base.name, lvl };
}

/* ============ کانواس ============ */
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
let W=0,H=0;

function resizeCanvas(){
  const r = canvas.parentElement.getBoundingClientRect();
  const dpr = Math.min(2, window.devicePixelRatio || 1);
  W = r.width;
  H = r.height;
  canvas.width = Math.floor(W * dpr);
  canvas.height = Math.floor(H * dpr);
  ctx.setTransform(dpr,0,0,dpr,0,0);
}
window.addEventListener('resize', ()=>{ resizeCanvas(); if(game) clampShip(); });

/* ============ وضعیت بازی ============ */
let game = null;
let running = false;
let lastTime = 0;
let rafId = null;

function clampShip(){
  if(!game) return;
  game.ship.x = Math.max(20, Math.min(W-20, game.ship.x));
  game.ship.y = Math.max(20, Math.min(H-20, game.ship.y));
  game.ship.targetX = Math.max(20, Math.min(W-20, game.ship.targetX));
  game.ship.targetY = Math.max(20, Math.min(H-20, game.ship.targetY));
}

function initGame(){
  resizeCanvas();
  game = {
    ship: { x: W/2, y: H-70, r: 18, targetX: W/2, targetY: H-70 },
    bullets: [], enemies: [], particles: [],
    score: 0, lives: 3, coinsThisRun: 0,
    spawnTimer: 0, spawnInterval: 1000,
    shootTimer: 0, shootCooldown: 240,
    maxLives: 3,
  };
  document.getElementById('score').textContent = '0';
  updateLives();
}

function updateLives(){
  const el = document.getElementById('lives');
  let s='';
  for(let i=0;i<game.maxLives;i++) s += i < game.lives ? '❤️' : '🖤';
  el.textContent = s;
}

/* ============ حلقه ============ */
function loop(t){
  if(!running) return;
  const dt = Math.min(50, t - lastTime || 16);
  lastTime = t;
  update(dt);
  draw();
  rafId = requestAnimationFrame(loop);
}

function update(dt){
  const g = game;
  g.ship.x += (g.ship.targetX - g.ship.x) * 0.28;
  g.ship.y += (g.ship.targetY - g.ship.y) * 0.28;
  clampShip();

  g.shootTimer -= dt;
  if(g.shootTimer <= 0){ shoot(); g.shootTimer = g.shootCooldown; }

  g.spawnTimer -= dt;
  if(g.spawnTimer <= 0){
    spawnEnemy();
    g.spawnTimer = Math.max(300, g.spawnInterval - g.score*0.5);
  }

  for(let i=g.bullets.length-1;i>=0;i--){
    const b = g.bullets[i];
    b.x += b.vx*dt; b.y += b.vy*dt;
    if(b.x<-20||b.x>W+20||b.y<-20||b.y>H+20) g.bullets.splice(i,1);
  }

  for(let i=g.enemies.length-1;i>=0;i--){
    const e = g.enemies[i];
    e.x += e.vx*dt; e.y += e.vy*dt;
    e.rot += dt*0.002;

    const dx = e.x-g.ship.x, dy = e.y-g.ship.y;
    if(Math.sqrt(dx*dx+dy*dy) < e.r + g.ship.r){
      g.enemies.splice(i,1);
      explode(e.x,e.y,e.color);
      loseLife();
      continue;
    }
    if(e.y > H + 30){
      g.enemies.splice(i,1);
      loseLife();
    }
  }

  for(let i=g.bullets.length-1;i>=0;i--){
    const b = g.bullets[i];
    let hit = false;
    for(let j=g.enemies.length-1;j>=0;j--){
      const e = g.enemies[j];
      const dx=b.x-e.x, dy=b.y-e.y;
      if(Math.sqrt(dx*dx+dy*dy) < e.r + 5){
        e.hp -= b.dmg;
        hit = true;
        if(e.hp <= 0){
          g.enemies.splice(j,1);
          g.score += 10;
          g.coinsThisRun += 2;
          DB.coins += 2;
          explode(e.x,e.y,e.color);
          document.getElementById('score').textContent = g.score;
          document.getElementById('coins').textContent = DB.coins;
          DB.save();
        }
        break;
      }
    }
    if(hit) g.bullets.splice(i,1);
  }

  for(let i=g.particles.length-1;i>=0;i--){
    const p=g.particles[i];
    p.x+=p.vx*dt; p.y+=p.vy*dt; p.life-=dt;
    if(p.life<=0) g.particles.splice(i,1);
  }
}

function spawnEnemy(){
  const r = 12 + Math.random()*8;
  const x = r + Math.random()*(W-2*r);
  game.enemies.push({
    x, y: -r-10, r,
    vx: (Math.random()-0.5)*0.04,
    vy: 0.055 + Math.random()*0.05 + game.score*0.000015,
    hp: 1, rot: Math.random()*Math.PI,
    color: ['#ff5566','#ffaa44','#aa66ff','#44ddff'][Math.floor(Math.random()*4)]
  });
}

function shoot(){
  const g = game;
  const st = weaponStats(DB.equippedWeapon);
  const n = st.bullets;
  for(let i=0;i<n;i++){
    const off = n===1?0:(i-(n-1)/2)*14;
    g.bullets.push({
      x: g.ship.x + off, y: g.ship.y-18,
      vx: n===1?0:((i-(n-1)/2))*0.05,
      vy: -0.8,
      dmg: st.dmg
    });
  }
}

function loseLife(){
  const g = game;
  g.lives--;
  updateLives();
  if(g.lives <= 0) gameOver();
}

function gameOver(){
  running = false;
  cancelAnimationFrame(rafId);
  const g = game;
  if(g.score > DB.record){
    DB.record = g.score;
    document.getElementById('record').textContent = DB.record;
  }
  DB.save();
  setTimeout(()=>{
    alert('بازی تمام شد!\nامتیاز: '+g.score+'\nسکه‌های این دست: '+g.coinsThisRun);
    initGame();
    running = true;
    lastTime = performance.now();
    rafId = requestAnimationFrame(loop);
  }, 80);
}

/* ============ رسم ============ */
let starSeed = [];
function initStars(){
  starSeed = [];
  for(let i=0;i<50;i++) starSeed.push({x:Math.random(),y:Math.random(),s:Math.random()*1.5+0.5});
}
initStars();

function draw(){
  ctx.clearRect(0,0,W,H);
  // ستاره‌ها
  const now = Date.now();
  ctx.fillStyle='#ffffff55';
  for(const st of starSeed){
    const y = (st.y*H + now*0.03) % H;
    ctx.fillRect(st.x*W, y, st.s, st.s);
  }

  const g = game;

  // گلوله‌ها
  ctx.shadowBlur = 8;
  ctx.shadowColor = '#66ddff';
  ctx.fillStyle='#66ddff';
  for(const b of g.bullets){
    ctx.beginPath();
    ctx.arc(b.x,b.y,3,0,Math.PI*2);
    ctx.fill();
  }
  ctx.shadowBlur = 0;

  // دشمن‌ها
  for(const e of g.enemies){
    ctx.save();
    ctx.translate(e.x,e.y);
    ctx.rotate(e.rot);
    ctx.beginPath();
    for(let i=0;i<6;i++){
      const a = Math.PI/3*i;
      const px = Math.cos(a)*e.r, py = Math.sin(a)*e.r;
      if(i===0) ctx.moveTo(px,py); else ctx.lineTo(px,py);
    }
    ctx.closePath();
    ctx.fillStyle = e.color;
    ctx.shadowColor = e.color;
    ctx.shadowBlur = 10;
    ctx.fill();
    ctx.restore();
  }
  ctx.shadowBlur = 0;

  // ذرات
  for(const p of g.particles){
    ctx.globalAlpha = Math.max(0, p.life/400);
    ctx.fillStyle = p.color;
    ctx.beginPath();
    ctx.arc(p.x,p.y,p.r,0,Math.PI*2);
    ctx.fill();
  }
  ctx.globalAlpha = 1;

  // سفینه
  const shipDef = SHIPS[DB.equippedShip] || SHIPS.redwing;
  drawShip(g.ship.x, g.ship.y, shipDef.color, shipDef.style);
}

function drawShip(x,y,color,style){
  ctx.save();
  ctx.translate(x,y);
  ctx.shadowColor = color;
  ctx.shadowBlur = 14;
  ctx.fillStyle = color;

  if(style === 'arrow'){
    // بال قرمز - B2 مثلثی
    ctx.beginPath();
    ctx.moveTo(0,-22); ctx.lineTo(8,-6); ctx.lineTo(26,6);
    ctx.lineTo(10,4); ctx.lineTo(6,10); ctx.lineTo(0,6);
    ctx.lineTo(-6,10); ctx.lineTo(-10,4); ctx.lineTo(-26,6);
    ctx.lineTo(-8,-6); ctx.closePath(); ctx.fill();
  } else if(style === 'hawk'){
    // شاهین - بال‌های بلند
    ctx.beginPath();
    ctx.moveTo(0,-24); ctx.lineTo(6,-4); ctx.lineTo(30,2);
    ctx.lineTo(12,6); ctx.lineTo(8,12); ctx.lineTo(0,8);
    ctx.lineTo(-8,12); ctx.lineTo(-12,6); ctx.lineTo(-30,2);
    ctx.lineTo(-6,-4); ctx.closePath(); ctx.fill();
  } else if(style === 'ghost'){
    // شبح - بدنه باریک
    ctx.beginPath();
    ctx.moveTo(0,-26); ctx.lineTo(5,-8); ctx.lineTo(20,0);
    ctx.lineTo(8,3); ctx.lineTo(5,8); ctx.lineTo(0,5);
    ctx.lineTo(-5,8); ctx.lineTo(-8,3); ctx.lineTo(-20,0);
    ctx.lineTo(-5,-8); ctx.closePath(); ctx.fill();
  } else if(style === 'shield'){
    // سپر - پهن و گرد
    ctx.beginPath();
    ctx.moveTo(0,-20); ctx.lineTo(10,-8); ctx.lineTo(28,4);
    ctx.lineTo(14,8); ctx.lineTo(8,14); ctx.lineTo(0,10);
    ctx.lineTo(-8,14); ctx.lineTo(-14,8); ctx.lineTo(-28,4);
    ctx.lineTo(-10,-8); ctx.closePath(); ctx.fill();
    // خط سپر
    ctx.shadowBlur = 0;
    ctx.strokeStyle = '#ffffffaa'; ctx.lineWidth = 1.5;
    ctx.beginPath();
    ctx.moveTo(-18,2); ctx.lineTo(0,-14); ctx.lineTo(18,2);
    ctx.stroke();
  } else if(style === 'star'){
    // ستاره کریستالی
    ctx.beginPath();
    ctx.moveTo(0,-26); ctx.lineTo(7,-10); ctx.lineTo(24,-6);
    ctx.lineTo(12,3); ctx.lineTo(16,16); ctx.lineTo(0,9);
    ctx.lineTo(-16,16); ctx.lineTo(-12,3); ctx.lineTo(-24,-6);
    ctx.lineTo(-7,-10); ctx.closePath(); ctx.fill();
    ctx.shadowBlur = 0;
    ctx.fillStyle = '#ffffff88';
    ctx.beginPath();
    ctx.moveTo(0,-16); ctx.lineTo(4,0); ctx.lineTo(0,10); ctx.lineTo(-4,0);
    ctx.closePath(); ctx.fill();
  }

  // نور موتور
  ctx.shadowBlur = 0;
  ctx.fillStyle = '#ffffffaa';
  ctx.beginPath();
  ctx.arc(0,4,2.5,0,Math.PI*2);
  ctx.fill();
  ctx.restore();
}

function explode(x,y,color){
  for(let i=0;i<10;i++){
    game.particles.push({
      x,y,
      vx:(Math.random()-0.5)*0.4,
      vy:(Math.random()-0.5)*0.4,
      r:2+Math.random()*3,
      life:300+Math.random()*200,
      color
    });
  }
}

/* ============ کنترل لمسی ============ */
let touchActive = false;
function setTarget(clientX, clientY){
  const r = canvas.getBoundingClientRect();
  game.ship.targetX = Math.max(20, Math.min(W-20, clientX - r.left));
  game.ship.targetY = Math.max(20, Math.min(H-20, clientY - r.top - 40));
}

canvas.addEventListener('touchstart', e=>{
  e.preventDefault();
  if(!game) return;
  const t = e.touches[0];
  setTarget(t.clientX, t.clientY);
  touchActive = true;
}, {passive:false});

canvas.addEventListener('touchmove', e=>{
  e.preventDefault();
  if(!touchActive || !game) return;
  const t = e.touches[0];
  setTarget(t.clientX, t.clientY);
}, {passive:false});

canvas.addEventListener('touchend', e=>{
  e.preventDefault();
  touchActive = false;
}, {passive:false});

canvas.addEventListener('touchcancel', e=>{ touchActive = false; }, {passive:false});

canvas.addEventListener('mousedown', e=>{
  if(!game) return;
  setTarget(e.clientX, e.clientY);
  touchActive = true;
});
canvas.addEventListener('mousemove', e=>{
  if(!touchActive || !game) return;
  setTarget(e.clientX, e.clientY);
});
window.addEventListener('mouseup', ()=>{ touchActive = false; });

/* ============ تب‌ها ============ */
const btnGame = document.getElementById('btnGame');
const btnShop = document.getElementById('btnShop');
const shopEl = document.getElementById('shop');
const gameWrap = document.getElementById('gameWrap');
const hud = document.getElementById('hud');

function showGame(){
  btnGame.classList.add('active');
  btnShop.classList.remove('active');
  gameWrap.style.display='block';
  hud.style.display='flex';
  shopEl.style.display='none';
  // بعد از تغییر دیسپلی، کانواس را سایز بده
  requestAnimationFrame(()=>{
    resizeCanvas();
    if(!game){
      initGame();
    } else {
      game.ship.x = W/2;
      game.ship.y = H-70;
      game.ship.targetX = W/2;
      game.ship.targetY = H-70;
    }
    if(!running){
      running = true;
      lastTime = performance.now();
      rafId = requestAnimationFrame(loop);
    }
  });
}

function showShop(){
  btnShop.classList.add('active');
  btnGame.classList.remove('active');
  gameWrap.style.display='none';
  hud.style.display='flex';
  shopEl.style.display='block';
  shopEl.scrollTop = 0;
  running = false;
  cancelAnimationFrame(rafId);
  renderShop();
}

btnGame.onclick = showGame;
btnShop.onclick = showShop;

/* ============ فروشگاه ============ */
function renderShop(){
  document.getElementById('coins').textContent = DB.coins;
  document.getElementById('record').textContent = DB.record;

  let html = '';

  // بخش اسلحه
  html += '<div class="shopSection"><div class="shopTitle">🔫 اسلحه‌ها</div>';
  for(const key of WEAPON_ORDER){
    const w = WEAPONS[key];
    const owned = DB.ownedWeapons.includes(key);
    const equipped = DB.equippedWeapon === key;
    const lvl = DB.weaponLevel[key] || 1;
    const st = weaponStats(key);

    html += `<div class="card ${equipped?'equipped':''}">
      <div class="name">${w.name}</div>
      <div class="stats">دمیج: ${st.dmg} &nbsp;|&nbsp; تعداد تیر: ${w.bullets} &nbsp;|&nbsp; سطح: ${lvl}</div>`;

    if(owned){
      if(equipped) html += `<div class="badge">✔ مجهز شده</div>`;
      else html += `<button class="equip" onclick="equipWeapon('${key}')">مجهز کردن</button>`;
      const upCost = w.upCost;
      html += `<button class="buy" onclick="upgradeWeapon('${key}')" ${DB.coins<upCost?'disabled':''}>ارتقا (${upCost} سکه)</button>`;
    } else {
      html += `<button class="buy" onclick="buyWeapon('${key}')" ${DB.coins<w.buyCost?'disabled':''}>خرید (${w.buyCost} سکه)</button>`;
    }
    html += '</div>';
  }
  html += '</div>';

  // بخش سفینه
  html += '<div class="shopSection"><div class="shopTitle">🚀 سفینه‌ها</div>';
  for(const key of SHIP_ORDER){
    const s = SHIPS[key];
    const owned = DB.ownedShips.includes(key);
    const equipped = DB.equippedShip === key;

    html += `<div class="card ${equipped?'equipped':''}">
      <div class="name">${s.name}</div>`;

    if(owned){
      if(equipped) html += `<div class="badge">✔ مجهز شده</div>`;
      else html += `<button class="equip" onclick="equipShip('${key}')">مجهز کردن</button>`;
    } else {
      html += `<button class="buy" onclick="buyShip('${key}')" ${DB.coins<s.cost?'disabled':''}>خرید (${s.cost} سکه)</button>`;
    }
    html += '</div>';
  }
  html += '</div>';

  shopEl.innerHTML = html;
}

window.buyWeapon = function(key){
  const w = WEAPONS[key];
  if(DB.coins < w.buyCost) return toast('سکه کافی نیست');
  DB.coins -= w.buyCost;
  if(!DB.ownedWeapons.includes(key)) DB.ownedWeapons.push(key);
  if(!DB.weaponLevel[key]) DB.weaponLevel[key] = 1;
  DB.save();
  renderShop();
  document.getElementById('coins').textContent = DB.coins;
  toast('خرید موفق!');
};

window.upgradeWeapon = function(key){
  const w = WEAPONS[key];
  if(!DB.ownedWeapons.includes(key)) return;
  if(DB.coins < w.upCost) return toast('سکه کافی نیست');
  DB.coins -= w.upCost;
  DB.weaponLevel[key] = (DB.weaponLevel[key]||1) + 1;
  DB.save();
  renderShop();
  document.getElementById('coins').textContent = DB.coins;
  toast('ارتقا یافت! سطح ' + DB.weaponLevel[key]);
};

window.equipWeapon = function(key){
  if(!DB.ownedWeapons.includes(key)) return;
  DB.equippedWeapon = key;
  DB.save();
  renderShop();
  toast('مجهز شد: ' + WEAPONS[key].name);
};

window.buyShip = function(key){
  const s = SHIPS[key];
  if(DB.coins < s.cost) return toast('سکه کافی نیست');
  DB.coins -= s.cost;
  if(!DB.ownedShips.includes(key)) DB.ownedShips.push(key);
  DB.save();
  renderShop();
  document.getElementById('coins').textContent = DB.coins;
  toast('سفینه خریداری شد!');
};

window.equipShip = function(key){
  if(!DB.ownedShips.includes(key)) return;
  DB.equippedShip = key;
  DB.save();
  renderShop();
  toast('مجهز شد: ' + SHIPS[key].name);
};

/* ============ Toast ============ */
let toastTimer=null;
function toast(msg){
  const el=document.getElementById('toast');
  el.textContent=msg;
  el.classList.add('show');
  clearTimeout(toastTimer);
  toastTimer=setTimeout(()=>el.classList.remove('show'),1500);
}

/* ============ شروع ============ */
document.getElementById('coins').textContent = DB.coins;
document.getElementById('record').textContent = DB.record;

// شروع با بازی
showGame();
</script>
</body>
</html>

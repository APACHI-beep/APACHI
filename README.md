<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>سفینه فضایی</title>
<style>
  * { margin:0; padding:0; box-sizing:border-box; -webkit-tap-highlight-color:transparent; }
  html,body { height:100%; overflow:hidden; font-family:tahoma, sans-serif; background:#050510; color:#fff; user-select:none; }
  #app { display:flex; flex-direction:column; height:100%; }

  /* نوار بالا */
  #topbar { display:flex; height:50px; flex:0 0 50px; background:#101028; border-bottom:2px solid #2a2a5a; }
  #topbar button { flex:1; background:transparent; border:none; color:#8899ff; font-size:16px; font-weight:bold; cursor:pointer; }
  #topbar button.active { color:#fff; background:#1e1e4a; box-shadow:inset 0 -3px 0 #5566ff; }

  /* HUD */
  #hud { display:flex; justify-content:space-between; align-items:flex-start; padding:6px 10px; flex:0 0 auto; background:#0a0a1e; font-size:13px; }
  #hud .left div { margin-bottom:4px; }
  #hud .right { color:#ff5566; font-size:20px; letter-spacing:2px; }

  /* بازی */
  #gameWrap { flex:1; position:relative; overflow:hidden; }
  canvas { display:block; width:100%; height:100%; background:#000; touch-action:none; }

  /* فروشگاه */
  #shop { display:none; flex:1; overflow-y:auto; padding:12px; }
  .shopSection { margin-bottom:20px; }
  .shopTitle { font-size:16px; color:#8899ff; margin-bottom:10px; border-bottom:1px solid #2a2a5a; padding-bottom:6px; }
  .card { background:#12122a; border:1px solid #2a2a5a; border-radius:10px; padding:12px; margin-bottom:10px; }
  .card.equipped { border-color:#55ff88; box-shadow:0 0 10px #55ff8855; }
  .card .name { font-size:15px; font-weight:bold; color:#fff; margin-bottom:6px; }
  .card .stats { font-size:12px; color:#aab; margin-bottom:8px; line-height:1.7; }
  .card .badge { color:#55ff88; font-size:12px; margin-bottom:6px; display:inline-block; }
  .card button { background:#2a2a6a; color:#fff; border:none; border-radius:6px; padding:7px 14px; font-size:13px; margin-left:6px; cursor:pointer; }
  .card button.buy { background:#2a6a3a; }
  .card button.equip { background:#5a2a6a; }
  .card button:disabled { opacity:0.4; }

  /* پیام */
  #toast { position:fixed; bottom:30px; left:50%; transform:translateX(-50%); background:#1e1e4a; color:#fff; padding:10px 20px; border-radius:20px; font-size:13px; opacity:0; transition:opacity .3s; pointer-events:none; z-index:99; }
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
  ownedWeapons: ['simple'],        // مالکیت
  equippedWeapon: 'simple',
  weaponLevel: { simple:1, twin:0, red:0, triple:0, plasma:0 }, // سطح ارتقا (1 = بدون ارتقا)
  ownedShips: ['redwing'],
  equippedShip: 'redwing',
  load() {
    try {
      const s = JSON.parse(localStorage.getItem('spacegame') || '{}');
      Object.assign(this, s);
      if(!this.weaponLevel) this.weaponLevel = { simple:1, twin:0, red:0, triple:0, plasma:0 };
    } catch(e){}
  },
  save() {
    localStorage.setItem('spacegame', JSON.stringify({
      coins:this.coins, record:this.record,
      ownedWeapons:this.ownedWeapons, equippedWeapon:this.equippedWeapon,
      weaponLevel:this.weaponLevel,
      ownedShips:this.ownedShips, equippedShip:this.equippedShip
    }));
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
const SHIPS = {
  redwing:   { name:'بال قرمز',       cost:0,    color:'#ff4466' },
  hawk:      { name:'شاهین کهکشانی',  cost:500,  color:'#44aaff' },
  purple:    { name:'شبح بنفش',       cost:1000, color:'#aa44ff' },
  goldshield:{ name:'سپر طلایی',      cost:2000, color:'#ffcc33' },
  crystal:   { name:'ستاره کریستالی', cost:5000, color:'#66ffdd' },
};

/* سطح ارتقا => دمیج بیشتر. هر سطح +50% دمیج و +0 تیر */
function weaponStats(key){
  const base = WEAPONS[key];
  const lvl = DB.weaponLevel[key] || 1;
  return { dmg: base.dmg * lvl, bullets: base.bullets, name: base.name };
}

/* ============ وضعیت بازی ============ */
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
let W=0,H=0;
function resize(){
  const r = canvas.getBoundingClientRect();
  W = canvas.width = r.width * devicePixelRatio;
  H = canvas.height = r.height * devicePixelRatio;
  ctx.setTransform(devicePixelRatio,0,0,devicePixelRatio,0,0);
  W = r.width; H = r.height;
}
window.addEventListener('resize', resize);

let game = null;
let running = false;
let lastTime = 0;
let rafId = null;

function initGame(){
  resize();
  game = {
    ship: { x: W/2, y: H-70, r: 18, color: SHIPS[DB.equippedShip].color, targetX: W/2, targetY: H-70 },
    bullets: [], enemies: [], particles: [],
    score: 0, lives: 3, coinsThisRun: 0,
    spawnTimer: 0, spawnInterval: 1000,
    shootTimer: 0, shootCooldown: 260,
    maxLives: 3,
  };
  document.getElementById('score').textContent = '0';
  updateLives();
}

/* ============ حلقه بازی ============ */
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
  // حرکت سفینه به سمت هدف
  g.ship.x += (g.ship.targetX - g.ship.x) * 0.25;
  g.ship.y += (g.ship.targetY - g.ship.y) * 0.25;
  g.ship.x = Math.max(20, Math.min(W-20, g.ship.x));
  g.ship.y = Math.max(20, Math.min(H-20, g.ship.y));

  // شلیک خودکار
  g.shootTimer -= dt;
  if(g.shootTimer <= 0){
    shoot();
    g.shootTimer = g.shootCooldown;
  }

  // تولید دشمن
  g.spawnTimer -= dt;
  if(g.spawnTimer <= 0){
    spawnEnemy();
    g.spawnTimer = Math.max(320, g.spawnInterval - g.score*0.4);
  }

  // گلوله‌ها
  for(let i=g.bullets.length-1;i>=0;i--){
    const b = g.bullets[i];
    b.x += b.vx*dt; b.y += b.vy*dt;
    if(b.x<-20||b.x>W+20||b.y<-20||b.y>H+20) g.bullets.splice(i,1);
  }

  // دشمن‌ها
  for(let i=g.enemies.length-1;i>=0;i--){
    const e = g.enemies[i];
    e.x += e.vx*dt; e.y += e.vy*dt;
    e.rot += dt*0.002;

    // برخورد با سفینه
    const dx = e.x-g.ship.x, dy = e.y-g.ship.y;
    if(Math.sqrt(dx*dx+dy*dy) < e.r + g.ship.r){
      g.enemies.splice(i,1);
      loseLife();
      continue;
    }
    // رد شدن از پایین
    if(e.y > H + 30){
      g.enemies.splice(i,1);
      loseLife();
    }
  }

  // برخورد گلوله با دشمن
  for(let i=g.bullets.length-1;i>=0;i--){
    const b = g.bullets[i];
    for(let j=g.enemies.length-1;j>=0;j--){
      const e = g.enemies[j];
      const dx=b.x-e.x, dy=b.y-e.y;
      if(Math.sqrt(dx*dx+dy*dy) < e.r + 5){
        e.hp -= b.dmg;
        g.bullets.splice(i,1);
        if(e.hp <= 0){
          // کشته شد
          g.enemies.splice(j,1);
          g.score += 10;
          g.coinsThisRun += 2;
          DB.coins += 2;
          explode(e.x,e.y,e.color);
          document.getElementById('score').textContent = g.score;
          document.getElementById('coins').textContent = DB.coins;
        }
        break;
      }
    }
  }

  // ذرات
  for(let i=g.particles.length-1;i>=0;i--){
    const p=g.particles[i];
    p.x+=p.vx*dt; p.y+=p.vy*dt; p.life-=dt;
    if(p.life<=0) g.particles.splice(i,1);
  }
}

function spawnEnemy(){
  const r = 12 + Math.random()*10;
  const x = r + Math.random()*(W-2*r);
  game.enemies.push({
    x, y: -r-10, r,
    vx: (Math.random()-0.5)*0.05,
    vy: 0.06 + Math.random()*0.06 + game.score*0.00002,
    hp: 1, rot: Math.random()*Math.PI,
    color: ['#ff5566','#ffaa44','#aa66ff','#44ddff'][Math.floor(Math.random()*4)]
  });
}

function shoot(){
  const g = game;
  const st = weaponStats(DB.equippedWeapon);
  const n = st.bullets;
  const spread = n===1?0:0.12;
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
  if(g.lives <= 0){
    gameOver();
  }
}

function updateLives(){
  const el = document.getElementById('lives');
  let s='';
  for(let i=0;i<game.maxLives;i++){
    s += i < game.lives ? '❤️' : '🖤';
  }
  el.textContent = s;
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
    alert('بازی تمام شد!\nامتیاز: '+g.score+'\nسکه‌های کسب شده: '+g.coinsThisRun);
    initGame();
    running = true;
    lastTime = performance.now();
    rafId = requestAnimationFrame(loop);
  }, 100);
}

/* ============ رسم ============ */
function draw(){
  ctx.clearRect(0,0,W,H);
  // ستاره‌ها
  ctx.fillStyle='#ffffff22';
  for(let i=0;i<40;i++){
    const x=(i*97+Date.now()*0.01)%W;
    const y=(i*53+Date.now()*0.03)%H;
    ctx.fillRect(x,y,1.5,1.5);
  }

  const g = game;
  // گلوله‌ها
  ctx.fillStyle='#66ddff';
  for(const b of g.bullets){
    ctx.beginPath();
    ctx.arc(b.x,b.y,3,0,Math.PI*2);
    ctx.fill();
  }

  // دشمن‌ها (شش ضلعی)
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

  // ذرات
  for(const p of g.particles){
    ctx.globalAlpha = Math.max(0, p.life/400);
    ctx.fillStyle = p.color;
    ctx.beginPath();
    ctx.arc(p.x,p.y,p.r,0,Math.PI*2);
    ctx.fill();
  }
  ctx.globalAlpha = 1;

  // سفینه (شبیه B-2)
  drawShip(g.ship.x, g.ship.y, g.ship.color);
}

function drawShip(x,y,color){
  ctx.save();
  ctx.translate(x,y);
  ctx.shadowColor = color;
  ctx.shadowBlur = 15;
  ctx.fillStyle = color;
  // بدنه بال مثلثی/خمیده B-2
  ctx.beginPath();
  ctx.moveTo(0,-22);          // نوک
  ctx.lineTo(8,-6);
  ctx.lineTo(26,6);           // بال راست
  ctx.lineTo(10,4);
  ctx.lineTo(6,10);
  ctx.lineTo(0,6);
  ctx.lineTo(-6,10);
  ctx.lineTo(-10,4);
  ctx.lineTo(-26,6);          // بال چپ
  ctx.lineTo(-8,-6);
  ctx.closePath();
  ctx.fill();
  // نور مرکزی
  ctx.shadowBlur = 0;
  ctx.fillStyle = '#ffffffaa';
  ctx.beginPath();
  ctx.arc(0,0,3,0,Math.PI*2);
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
canvas.addEventListener('touchstart', e=>{
  e.preventDefault();
  const t = e.touches[0];
  const r = canvas.getBoundingClientRect();
  const x = t.clientX - r.left;
  const y = t.clientY - r.top;
  // حرکت فوری + شروع دنبال کردن
  game.ship.targetX = x;
  game.ship.targetY = y - 30;
  touchActive = true;
}, {passive:false});

canvas.addEventListener('touchmove', e=>{
  e.preventDefault();
  if(!touchActive) return;
  const t = e.touches[0];
  const r = canvas.getBoundingClientRect();
  game.ship.targetX = t.clientX - r.left;
  game.ship.targetY = t.clientY - r.top - 30;
}, {passive:false});

canvas.addEventListener('touchend', e=>{
  e.preventDefault();
  touchActive = false;
}, {passive:false});

// پشتیبانی از ماوس برای تست دسکتاپ
canvas.addEventListener('mousemove', e=>{
  if(e.buttons !== 1) return;
  const r = canvas.getBoundingClientRect();
  game.ship.targetX = e.clientX - r.left;
  game.ship.targetY = e.clientY - r.top - 30;
});

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
  if(!running){
    initGame();
    running = true;
    lastTime = performance.now();
    rafId = requestAnimationFrame(loop);
  }
}
function showShop(){
  btnShop.classList.add('active');
  btnGame.classList.remove('active');
  gameWrap.style.display='none';
  hud.style.display='flex';
  shopEl.style.display='block';
  running = false;
  cancelAnimationFrame(rafId);
  renderShop();
}

btnGame.onclick = showGame;
btnShop.onclick = showShop;

/* ============ رندر فروشگاه ============ */
function renderShop(){
  document.getElementById('coins').textContent = DB.coins;
  document.getElementById('record').textContent = DB.record;

  let html = '';
  // بخش اسلحه
  html += '<div class="shopSection"><div class="shopTitle">🔫 اسلحه‌ها</div>';
  for(const key in WEAPONS){
    const w = WEAPONS[key];
    const owned = DB.ownedWeapons.includes(key);
    const equipped = DB.equippedWeapon === key;
    const lvl = DB.weaponLevel[key] || 1;
    const st = weaponStats(key);
    const dmgShow = st.dmg;
    html += `<div class="card ${equipped?'equipped':''}">
      <div class="name">${w.name}</div>
      <div class="stats">دمیج: ${dmgShow} &nbsp;|&nbsp; تعداد تیر: ${w.bullets}</div>`;
    if(owned){
      if(equipped) html += `<div class="badge">✔ مجهز شده</div>`;
      // دکمه مجهز کردن اگر مجهز نیست
      if(!equipped) html += `<button class="equip" onclick="equipWeapon('${key}')">مجهز کردن</button>`;
      // ارتقا (برای همه، حتی مجهز)
      const upCost = w.upCost;
      html += `<button class="buy" onclick="upgradeWeapon('${key}')" ${DB.coins<upCost?'disabled':''}>ارتقا (${upCost} سکه)</button>`;
      html += `<div class="stats" style="margin-top:6px;color:#778">سطح: ${lvl}</div>`;
    } else {
      html += `<button class="buy" onclick="buyWeapon('${key}')" ${DB.coins<w.buyCost?'disabled':''}>خرید (${w.buyCost} سکه)</button>`;
    }
    html += '</div>';
  }
  html += '</div>';

  // بخش سفینه
  html += '<div class="shopSection"><div class="shopTitle">🚀 سفینه‌ها</div>';
  for(const key in SHIPS){
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
  DB.ownedWeapons.push(key);
  DB.weaponLevel[key] = 1;
  if(DB.equippedWeapon === 'simple') DB.equippedWeapon = key; // اگر هیچی مجهز نبود
  DB.save();
  renderShop();
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
  DB.ownedShips.push(key);
  if(DB.ownedShips.length===1) DB.equippedShip = key;
  DB.save();
  renderShop();
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
showGame();
</script>
</body>
</html>

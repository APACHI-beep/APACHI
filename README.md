<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
<title>Star Hunter</title>

<style>
*{box-sizing:border-box}
body{
 margin:0;background:#050817;color:white;
 font-family:Tahoma,Arial,sans-serif
}
header{
 position:sticky;top:0;z-index:10;
 background:#0b1025;border-bottom:1px solid #30416d;
 padding:12px;display:flex;align-items:center;
 justify-content:space-between;gap:10px;flex-wrap:wrap
}
.logo{font-weight:bold;color:#75f4ff;font-size:19px}
nav{display:flex;gap:6px}
button{
 font-family:inherit;color:white;cursor:pointer;
 background:#172447;border:1px solid #3b548e;
 padding:10px 13px;border-radius:9px
}
button:hover{background:#2c447b}
nav button.active{background:#3159a7}
.stats{font-size:12px;color:#c5d4ff}
main{max-width:1000px;margin:auto;padding:15px}
.page{display:none}
.page.active{display:block}
.card{
 background:linear-gradient(145deg,#121a35,#090e20);
 border:1px solid #293963;border-radius:15px;
 padding:16px;margin-bottom:14px
}
h2{color:#8cf4ff;margin-top:0}
.muted{color:#a8b8dd;font-size:13px;line-height:1.8}
.row{display:flex;align-items:center;gap:12px;flex-wrap:wrap}
.row>*{flex:1}
canvas{
 display:block;width:100%;height:440px;
 border-radius:12px;background:#030611;
 touch-action:none
}
#hud{
 display:flex;justify-content:space-between;
 gap:8px;margin-bottom:10px;font-size:13px
}
#touchControls{
 display:flex;justify-content:space-between;
 align-items:center;gap:20px;margin-top:14px;
 direction:ltr
}
.movePad{
 display:flex;gap:9px;align-items:center
}
.control{
 width:65px;height:65px;border-radius:50%;
 font-size:25px;touch-action:none;
 background:#182c57;border:2px solid #5e91e9;
 box-shadow:0 0 18px #397dff33
}
.fireButton{
 width:85px;height:85px;border-radius:50%;
 font-size:28px;background:#5c1839;
 border:2px solid #ff69a8;
 box-shadow:0 0 22px #ff3b8333
}
.control:active,.fireButton:active{transform:scale(.94)}
#shopGrid{
 display:grid;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));
 gap:12px
}
.skin{
 text-align:center;padding:16px;background:#0e1630;
 border:1px solid #344777;border-radius:14px
}
.preview{font-size:45px;margin:12px}
.skin button{width:100%;margin-top:10px}
.danger{background:#601c35;border-color:#e05b82}
@media(max-width:600px){
 header{justify-content:center}
 main{padding:9px}
 canvas{height:55vh;min-height:300px;max-height:440px}
 .control{width:58px;height:58px}
 .fireButton{width:76px;height:76px}
}
</style>
</head>

<body>

<header>
 <div class="logo">🚀 STAR HUNTER</div>

 <nav>
  <button class="active" data-tab="game">🎮 گیم</button>
  <button data-tab="leader">🏆 لیدربرد</button>
  <button data-tab="shop">🛒 فروشگاه</button>
 </nav>

 <div class="stats">
  👤 <b id="username">بازیکن</b> |
  🪙 <b id="coins">0</b>
 </div>
</header>

<main>

<!-- GAME -->
<section id="game" class="page active">

 <div class="card">
  <div class="row">
   <div>
    <h2>نبرد کهکشانی</h2>
    <p class="muted">دشمن‌ها را نابود کن، امتیاز بگیر و سکه جمع کن!</p>
   </div>
   <button id="startBtn">شروع بازی</button>
  </div>
 </div>

 <div class="card">
  <div id="hud">
   <span>امتیاز: <b id="score">0</b></span>
   <span>رکورد: <b id="record">0</b></span>
   <span>جان: <b id="lives">3</b></span>
  </div>

  <canvas id="gameCanvas"></canvas>

  <!-- حرکت در سمت چپ، شلیک در سمت راست -->
  <div id="touchControls">
   <div class="movePad">
    <button class="control" id="moveLeft">◀</button>
    <button class="control" id="moveRight">▶</button>
   </div>
   <button class="fireButton" id="fireButton">🔥</button>
  </div>

  <p id="status" class="muted">
   برای شروع بازی، دکمه شروع را بزن.
  </p>
 </div>

</section>

<!-- LEADERBOARD -->
<section id="leader" class="page">
 <div class="card">
  <h2>🏆 لیدربرد</h2>
  <p class="muted">بازیکنان بر اساس بالاترین رکورد مرتب می‌شوند.</p>
  <div id="leaderList"></div>
 </div>

 <div class="card">
  <h2>⚙️ پنل سازنده</h2>
  <p class="muted">
   ریست، اطلاعات ذخیره‌شده در این مرورگر را پاک می‌کند.
   برای ریست جهانی باید از سرور و پنل مدیریت امن استفاده شود.
  </p>
  <button class="danger" id="resetBtn">🗑 ریست همه اطلاعات محلی</button>
 </div>
</section>

<!-- SHOP -->
<section id="shop" class="page">
 <div class="card">
  <h2>🛒 فروشگاه سفینه‌ها</h2>
  <p class="muted">با سکه‌هایی که در نبرد به دست می‌آوری اسکین بخر.</p>
  موجودی: 🪙 <b id="shopCoins">0</b>
 </div>
 <div id="shopGrid"></div>
</section>

</main>

<script>
// ========================
// اطلاعات بازیکن و فروشگاه
// ========================

const $=id=>document.getElementById(id);

const skins=[
 {id:"blue",name:"سفینه آبی",price:0,color:"#53eaff",emoji:"🚀"},
 {id:"pink",name:"پینک فانتوم",price:100,color:"#ff55d9",emoji:"🛸"},
 {id:"green",name:"نوا سبز",price:250,color:"#8cff67",emoji:"🚀"},
 {id:"gold",name:"طلایی خورشیدی",price:600,color:"#ffd15b",emoji:"🌟"},
 {id:"purple",name:"سایه بنفش",price:1200,color:"#b28aff",emoji:"💜"},
 {id:"cosmic",name:"کیهانی افسانه‌ای",price:2500,color:"#ffffff",emoji:"🌌"}
];

function readDB(){
 try{return JSON.parse(localStorage.getItem("starHunterDB"))||{}}
 catch{return {}}
}
let db=readDB();

let username=localStorage.getItem("starHunterName");
if(!username){
 username=(prompt("اسم بازیکنت رو وارد کن:")||"بازیکن").trim().slice(0,18);
 if(!username)username="بازیکن";
 localStorage.setItem("starHunterName",username);
}

if(!db[username]){
 db[username]={
  coins:0,record:0,owned:["blue"],equipped:"blue"
 };
}
let profile=db[username];

function save(){
 db[username]=profile;
 localStorage.setItem("starHunterDB",JSON.stringify(db));
}

function refreshUI(){
 $("username").textContent=username;
 $("coins").textContent=profile.coins;
 $("shopCoins").textContent=profile.coins;
 $("record").textContent=profile.record;
 renderLeaderboard();
 renderShop();
}

function renderLeaderboard(){
 let list=Object.entries(readDB())
  .sort((a,b)=>b[1].record-a[1].record)
  .slice(0,10);

 $("leaderList").innerHTML=list.length?
 list.map(([name,p],i)=>
 `<div class="card">🏅 ${i+1}. ${escapeHTML(name)}
 <span style="float:left">⭐ ${p.record}</span></div>`
 ).join(""):"هنوز رکوردی ثبت نشده.";
}

function escapeHTML(text){
 return text.replace(/[&<>"']/g,c=>({
 "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
 }[c]));
}

function renderShop(){
 $("shopGrid").innerHTML=skins.map(s=>{
 let owned=profile.owned.includes(s.id);
 let equipped=profile.equipped===s.id;

 return `<div class="skin">
 <div class="preview" style="filter:drop-shadow(0 0 12px ${s.color})">${s.emoji}</div>
 <b>${s.name}</b>
 <p style="color:${s.color}">🪙 ${s.price} سکه</p>
 ${equipped?
 '<button disabled>اسکین فعلی</button>':
 owned?
 `<button onclick="equipSkin('${s.id}')">انتخاب اسکین</button>`:
 `<button onclick="buySkin('${s.id}')">خرید اسکین</button>`}
 </div>`;
 }).join("");
}

function buySkin(id){
 const s=skins.find(x=>x.id===id);
 if(!s||profile.owned.includes(id))return;

 if(profile.coins<s.price){
  alert("سکه کافی نداری!");
  return;
 }

 profile.coins-=s.price;
 profile.owned.push(id);
 save();
 refreshUI();
}

function equipSkin(id){
 if(!profile.owned.includes(id))return;
 profile.equipped=id;
 save();
 refreshUI();
}

window.buySkin=buySkin;
window.equipSkin=equipSkin;

// تب‌ها
document.querySelectorAll("nav button").forEach(btn=>{
 btn.onclick=()=>{
  document.querySelectorAll("nav button").forEach(b=>b.classList.remove("active"));
  document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
  btn.classList.add("active");
  $(btn.dataset.tab).classList.add("active");
  if(btn.dataset.tab==="game")resizeCanvas();
 };
});

// ریست اطلاعات محلی
$("resetBtn").onclick=()=>{
 if(!confirm("رکورد، سکه و اسکین‌های تمام بازیکنان ذخیره‌شده در این مرورگر پاک شوند؟"))return;

 localStorage.removeItem("starHunterDB");
 db={};
 profile={coins:0,record:0,owned:["blue"],equipped:"blue"};
 db[username]=profile;
 save();
 refreshUI();
 alert("اطلاعات محلی ریست شد.");
};

// ========================
// موتور بازی فضایی
// ========================

const canvas=$("gameCanvas");
const ctx=canvas.getContext("2d");

let W=canvas.clientWidth,H=canvas.clientHeight;
let stars=[],bullets=[],enemies=[],particles=[];
let player,score=0,lives=3,frame=0;
let running=false,lastShot=0;
let keys={};
let touch={left:false,right:false,fire:false};

function resizeCanvas(){
 const dpr=window.devicePixelRatio||1;
 W=canvas.clientWidth;
 H=canvas.clientHeight;
 canvas.width=W*dpr;
 canvas.height=H*dpr;
 ctx.setTransform(dpr,0,0,dpr,0,0);

 stars=Array.from({length:130},()=>({
  x:Math.random()*W,
  y:Math.random()*H,
  speed:Math.random()*2+0.4,
  size:Math.random()*2+0.5
 }));
}
window.addEventListener("resize",resizeCanvas);
resizeCanvas();

// رسم سفینه بازیکن
function drawPlayer(){
 const s=skins.find(x=>x.id===profile.equipped)||skins[0];
 const color=s.color;

 ctx.save();
 ctx.translate(player.x,player.y);
 ctx.shadowBlur=22;
 ctx.shadowColor=color;

 // شعله موتور
 ctx.fillStyle="#ff9a45";
 ctx.beginPath();
 ctx.moveTo(-7,12);
 ctx.lineTo(0,29+Math.random()*7);
 ctx.lineTo(7,12);
 ctx.fill();

 // بدنه سفینه
 ctx.fillStyle=color;
 ctx.beginPath();
 ctx.moveTo(0,-25);
 ctx.lineTo(19,17);
 ctx.lineTo(5,11);
 ctx.lineTo(0,17);
 ctx.lineTo(-5,11);
 ctx.lineTo(-19,17);
 ctx.closePath();
 ctx.fill();

 // شیشه کابین
 ctx.fillStyle="#eaffff";
 ctx.beginPath();
 ctx.ellipse(0,-5,5,9,0,0,Math.PI*2);
 ctx.fill();

 ctx.restore();
}

// رسم دشمن‌ها
function drawEnemy(e){
 ctx.save();
 ctx.translate(e.x,e.y);
 ctx.shadowBlur=18;
 ctx.shadowColor=e.color;
 ctx.fillStyle=e.color;

 ctx.beginPath();
 ctx.moveTo(-23,-8);
 ctx.lineTo(-9,-16);
 ctx.lineTo(0,-10);
 ctx.lineTo(9,-16);
 ctx.lineTo(23,-8);
 ctx.lineTo(15,11);
 ctx.lineTo(0,17);
 ctx.lineTo(-15,11);
 ctx.closePath();
 ctx.fill();

 ctx.fillStyle="#fff";
 ctx.fillRect(-8,-5,5,4);
 ctx.fillRect(4,-5,5,4);

 ctx.restore();
}

// افکت انفجار
function explosion(x,y,color){
 for(let i=0;i<22;i++){
  const a=Math.random()*Math.PI*2;
  const speed=Math.random()*4+1;

  particles.push({
   x,y,
   vx:Math.cos(a)*speed,
   vy:Math.sin(a)*speed,
   life:30,
   color
  });
 }
}

// شروع دست
function startGame(){
 score=0;
 lives=3;
 frame=0;
 bullets=[];
 enemies=[];
 particles=[];

 player={x:W/2,y:H-65};
 running=true;

 $("score").textContent=score;
 $("lives").textContent=lives;
 $("status").textContent="دشمن‌ها را نابود کن!";
 $("startBtn").textContent="شروع دوباره";
}
$("startBtn").onclick=startGame;

// پایان دست
function gameOver(){
 running=false;

 const reward=Math.floor(score/10);
 profile.coins+=reward;
 profile.record=Math.max(profile.record,score);
 save();
 refreshUI();

 $("status").textContent=
 `پایان بازی! امتیاز: ${score} | جایزه: ${reward} سکه`;

 $("startBtn").textContent="دوباره بازی کن";
}

// شلیک
function shoot(){
 if(frame-lastShot<10)return;

 lastShot=frame;
 bullets.push({
  x:player.x,
  y:player.y-25,
  speed:9
 });

 // درخشش لیزر
 particles.push({
  x:player.x,y:player.y-20,
  vx:0,vy:-2,life:8,color:"#a8ffff"
 });
}

// ساخت دشمن
function spawnEnemy(){
 const boss=score>0&&score%500<15&&Math.random()<0.25;

 enemies.push({
  x:30+Math.random()*(W-60),
  y:-30,
  speed:boss?1:1.7+Math.min(score/1000,3),
  hp:boss?5:1,
  boss,
  color:boss?"#ff32a0":
   ["#ff4c72","#ffad42","#b56aff"][Math.floor(Math.random()*3)]
 });
}

// حلقه بازی
function loop(){
 requestAnimationFrame(loop);

 ctx.clearRect(0,0,W,H);

 // پس‌زمینه
 const bg=ctx.createLinearGradient(0,0,0,H);
 bg.addColorStop(0,"#080c25");
 bg.addColorStop(1,"#02040b");
 ctx.fillStyle=bg;
 ctx.fillRect(0,0,W,H);

 // ستاره‌ها
 for(const s of stars){
  s.y+=s.speed;
  if(s.y>H){
   s.y=0;
   s.x=Math.random()*W;
  }

  ctx.fillStyle="#d7edff";
  ctx.globalAlpha=0.3+s.speed/4;
  ctx.fillRect(s.x,s.y,s.size,s.size);
 }
 ctx.globalAlpha=1;

 if(!running)return;

 frame++;

 // حرکت سفینه
 if(keys.ArrowLeft||keys.a||touch.left)player.x-=5;
 if(keys.ArrowRight||keys.d||touch.right)player.x+=5;
 if(keys.ArrowUp||keys.w)player.y-=4;
 if(keys.ArrowDown||keys.s)player.y+=4;

 player.x=Math.max(22,Math.min(W-22,player.x));
 player.y=Math.max(H*0.45,Math.min(H-35,player.y));

 drawPlayer();

 // شلیک با Space یا دکمه لمسی
 if(keys.Space||touch.fire)shoot();

 bullets=bullets.filter(b=>{
  b.y-=b.speed;

  ctx.shadowBlur=15;
  ctx.shadowColor="#63ffff";
  ctx.fillStyle="#d6ffff";
  ctx.fillRect(b.x-2,b.y,4,17);
  ctx.shadowBlur=0;

  return b.y>-20;
 });

 // موج دشمن
 if(frame%42===0)spawnEnemy();

 enemies=enemies.filter(e=>{
  e.y+=e.speed;
  drawEnemy(e);

  // برخورد تیر با دشمن
  for(const b of bullets){
   if(Math.abs(b.x-e.x)<24&&Math.abs(b.y-e.y)<23){
    b.y=-100;
    e.hp--;
    explosion(e.x,e.y,e.color);

    if(e.hp<=0){
     score+=e.boss?100:10;
     return false;
    }
   }
  }

  // برخورد دشمن با سفینه
  if(Math.abs(player.x-e.x)<28&&Math.abs(player.y-e.y)<28){
   explosion(player.x,player.y,"#ff5387");
   lives--;
   $("lives").textContent=lives;

   if(lives<=0){
    gameOver();
   }else{
    player.x=W/2;
    player.y=H-65;
   }
   return false;
  }

  // دشمن رد شده
  if(e.y>H+40){
   lives--;
   $("lives").textContent=lives;
   if(lives<=0)gameOver();
   return false;
  }

  return true;
 });

 // ذرات انفجار
 particles=particles.filter(p=>{
  p.x+=p.vx;
  p.y+=p.vy;
  p.life--;

  ctx.globalAlpha=Math.max(0,p.life/30);
  ctx.fillStyle=p.color;
  ctx.fillRect(p.x,p.y,4,4);
  ctx.globalAlpha=1;

  return p.life>0;
 });

 $("score").textContent=score;
}
loop();

// کنترل کیبورد
window.addEventListener("keydown",e=>{
 keys[e.key]=true;
 if(e.code==="Space"){
  keys.Space=true;
  e.preventDefault();
 }
});
window.addEventListener("keyup",e=>{
 keys[e.key]=false;
 if(e.code==="Space")keys.Space=false;
});

// کنترل لمسی
function bindTouch(id,property){
 const el=$(id);

 el.addEventListener("pointerdown",e=>{
  e.preventDefault();
  touch[property]=true;
  el.setPointerCapture(e.pointerId);
 });

 function release(){
  touch[property]=false;
 }

 el.addEventListener("pointerup",release);
 el.addEventListener("pointercancel",release);
 el.addEventListener("lostpointercapture",release);
}

bindTouch("moveLeft","left");
bindTouch("moveRight","right");
bindTouch("fireButton","fire");

refreshUI();
</script>

</body>
</html>

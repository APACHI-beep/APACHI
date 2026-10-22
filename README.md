<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Star Hunter Online</title>

<style>
*{box-sizing:border-box}
body{
 margin:0;background:#050817;color:white;
 font-family:Tahoma,Arial,sans-serif
}
header{
 position:sticky;top:0;z-index:5;
 background:#0c1229;border-bottom:1px solid #344575;
 padding:12px;display:flex;justify-content:space-between;
 align-items:center;gap:8px;flex-wrap:wrap
}
.logo{color:#78f4ff;font-weight:bold;font-size:19px}
nav{display:flex;gap:5px;flex-wrap:wrap}
button,input{
 background:#182548;color:white;border:1px solid #40598d;
 border-radius:9px;padding:10px;font-family:inherit
}
button{cursor:pointer}
button:hover{background:#2a4176}
nav button.active{background:#3159a7}
main{max-width:1050px;margin:auto;padding:14px}
.page{display:none}
.page.active{display:block}
.card{
 background:linear-gradient(145deg,#121a35,#090e20);
 border:1px solid #293963;border-radius:14px;
 padding:15px;margin-bottom:13px
}
h2{color:#8cf4ff;margin-top:0}
.muted{color:#a8b8dd;font-size:13px;line-height:1.8}
.row{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
.row>*{flex:1}
canvas{
 width:100%;height:430px;display:block;
 background:#030611;border-radius:12px;
 touch-action:none
}
#hud{display:flex;justify-content:space-between;margin-bottom:10px}
#controls{display:flex;justify-content:space-between;
 align-items:center;direction:ltr;margin-top:12px}
.move{display:flex;gap:8px}
.control{width:62px;height:62px;border-radius:50%;font-size:23px}
.fire{width:82px;height:82px;border-radius:50%;
 background:#5c1839;border-color:#ff65a8;font-size:27px}
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(175px,1fr));gap:12px}
.item{background:#101a36;border:1px solid #344777;border-radius:12px;padding:14px;text-align:center}
.item button{width:100%;margin-top:9px}
#chatMessages{
 height:300px;overflow-y:auto;background:#070c1c;
 border:1px solid #293963;border-radius:10px;padding:10px;
 overflow-wrap:anywhere
}
.msg{padding:7px;border-bottom:1px solid #1c2948;font-size:13px}
#chatForm{display:flex;gap:7px;margin-top:10px}
#chatInput{flex:1;min-width:0}
.danger{background:#601c35;border-color:#e05b82}
@media(max-width:600px){
 header{justify-content:center}
 canvas{height:55vh;min-height:300px;max-height:430px}
 .control{width:55px;height:55px}
 .fire{width:75px;height:75px}
}
</style>
</head>

<body>

<header>
 <div class="logo">🚀 STAR HUNTER</div>
 <nav>
  <button class="active" data-page="game">🎮 گیم</button>
  <button data-page="leader">🏆 لیدربرد</button>
  <button data-page="shop">🛒 فروشگاه</button>
  <button data-page="chat">💬 چت جهانی</button>
 </nav>
 <div style="font-size:12px">🪙 <b id="coins">0</b></div>
</header>

<main>

<section id="game" class="page active">
 <div class="card row">
  <div>
   <h2>نبرد کهکشانی</h2>
   <p class="muted">دشمن‌ها رو نابود کن و رکورد بزن!</p>
  </div>
  <button id="start">شروع بازی</button>
 </div>

 <div class="card">
  <div id="hud">
   <span>امتیاز: <b id="score">0</b></span>
   <span>رکورد: <b id="record">0</b></span>
   <span>جان: <b id="lives">3</b></span>
  </div>

  <canvas id="canvas"></canvas>

  <div id="controls">
   <div class="move">
    <button class="control" id="left">◀</button>
    <button class="control" id="right">▶</button>
   </div>
   <button class="fire" id="shoot">🔥</button>
  </div>

  <p class="muted" id="status">برای شروع، دکمه شروع بازی رو بزن.</p>
  <p class="muted">کامپیوتر: WASD یا جهت‌ها + Space</p>
 </div>
</section>

<section id="leader" class="page">
 <div class="card">
  <h2>🏆 لیدربرد آنلاین</h2>
  <p class="muted">رکورد بازیکنان از دیتابیس آنلاین دریافت می‌شود.</p>
  <div id="leaderList">در حال دریافت رکوردها...</div>
 </div>
</section>

<section id="shop" class="page">
 <div class="card">
  <h2>🛒 فروشگاه</h2>
  <p class="muted">با سکه‌های بازی، اسکین و سلاح بخر.</p>
  <p>موجودی: 🪙 <b id="shopCoins">0</b></p>
 </div>
 <div id="shopGrid" class="grid"></div>
</section>

<section id="chat" class="page">
 <div class="card">
  <h2>💬 چت جهانی</h2>
  <p class="muted">پیام‌ها برای بازیکنان آنلاین قابل مشاهده‌اند. اطلاعات شخصی نفرست.</p>
  <div id="chatMessages"></div>
  <form id="chatForm">
   <input id="chatInput" maxlength="250" placeholder="پیامت رو بنویس..." autocomplete="off">
   <button>ارسال</button>
  </form>
  <p class="muted" id="chatStatus">در حال اتصال به چت...</p>
 </div>
</section>

</main>

<script type="module">
import {initializeApp} from
 "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";

import {getDatabase,ref,onValue,push,set,update,query,orderByChild,limitToLast}
 from "https://www.gstatic.com/firebasejs/10.12.2/firebase-database.js";

import {getAuth,signInAnonymously,onAuthStateChanged}
 from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";

/* ========================================
   1. تنظیم Firebase
   اطلاعات پروژه خودت را اینجا وارد کن.
======================================== */

const firebaseConfig={
 apiKey:"YOUR_API_KEY",
 authDomain:"YOUR_PROJECT.firebaseapp.com",
 databaseURL:"https://YOUR_PROJECT-default-rtdb.firebaseio.com",
 projectId:"YOUR_PROJECT",
 appId:"YOUR_APP_ID"
};

let app,db,auth,user=null,online=false;

try{
 app=initializeApp(firebaseConfig);
 db=getDatabase(app);
 auth=getAuth(app);

 signInAnonymously(auth).catch(err=>{
  console.error(err);
  $("chatStatus").textContent="اتصال به حساب آنلاین ناموفق بود.";
 });

 onAuthStateChanged(auth,u=>{
  if(!u)return;
  user=u;
  online=true;
  $("chatStatus").textContent="به چت جهانی وصل شدی.";
  loadChat();
  loadLeaderboard();
 });
}catch(e){
 console.warn("Firebase config is not set",e);
 $("chatStatus").textContent="برای فعال‌شدن چت و لیدربرد، Firebase را تنظیم کن.";
}

/* ========================================
   2. اطلاعات بازیکن و فروشگاه
======================================== */

const $=id=>document.getElementById(id);

const equipment=[
 {id:"blue",name:"سفینه آبی",type:"skin",price:0,color:"#53eaff",emoji:"🚀"},
 {id:"pink",name:"پینک فانتوم",type:"skin",price:100,color:"#ff55d9",emoji:"🛸"},
 {id:"gold",name:"سفینه طلایی",type:"skin",price:600,color:"#ffd15b",emoji:"🌟"},
 {id:"purple",name:"سایه بنفش",type:"skin",price:1200,color:"#b28aff",emoji:"💜"},

 {id:"machine",name:"مسلسل لیزری",type:"weapon",price:250,emoji:"🔫"},
 {id:"triple",name:"تیر سه‌تایی",type:"weapon",price:600,emoji:"💥"},
 {id:"rapid",name:"تیرانداز سریع",type:"weapon",price:1000,emoji:"⚡"},
 {id:"spread",name:"پخش‌کننده پنج‌تایی",type:"weapon",price:1800,emoji:"🌠"}
];

let saved=JSON.parse(localStorage.starHunterProfile||"null");

let profile=saved||{
 coins:0,record:0,
 owned:["blue"],
 skin:"blue",
 weapon:"normal"
};

function saveLocal(){
 localStorage.starHunterProfile=JSON.stringify(profile);
}

function refresh(){
 $("coins").textContent=profile.coins;
 $("shopCoins").textContent=profile.coins;
 $("record").textContent=profile.record;
 renderShop();
}

function renderShop(){
 $("shopGrid").innerHTML=equipment.map(it=>{
  const owned=profile.owned.includes(it.id);
  const selected=it.type==="skin"?
    profile.skin===it.id:profile.weapon===it.id;

  let label=selected?"انتخاب شده":
    owned?"انتخاب":"خرید";

  return `<div class="item">
   <div style="font-size:40px">${it.emoji}</div>
   <b>${it.name}</b>
   <p>🪙 ${it.price}</p>
   <button data-item="${it.id}">${label}</button>
  </div>`;
 }).join("");

 $("shopGrid").querySelectorAll("button").forEach(btn=>{
  btn.onclick=()=>purchase(btn.dataset.item);
 });
}

function purchase(id){
 const it=equipment.find(x=>x.id===id);
 if(!it)return;

 if(profile.owned.includes(id)){
  if(it.type==="skin")profile.skin=id;
  else profile.weapon=id;
  saveLocal();
  refresh();
  return;
 }

 if(profile.coins<it.price){
  alert("سکه کافی نداری!");
  return;
 }

 profile.coins-=it.price;
 profile.owned.push(id);

 if(it.type==="skin")profile.skin=id;
 else profile.weapon=id;

 saveLocal();
 refresh();
}

document.querySelectorAll("nav button").forEach(btn=>{
 btn.onclick=()=>{
  document.querySelectorAll("nav button").forEach(x=>x.classList.remove("active"));
  document.querySelectorAll(".page").forEach(x=>x.classList.remove("active"));
  btn.classList.add("active");
  $(btn.dataset.page).classList.add("active");
 };
});

/* ========================================
   3. چت جهانی Firebase
======================================== */

function loadChat(){
 if(!db)return;

 const chatRef=query(
  ref(db,"globalChat"),
  orderByChild("time"),
  limitToLast(50)
 );

 onValue(chatRef,snapshot=>{
  const messages=[];
  snapshot.forEach(child=>messages.push(child.val()));

  $("chatMessages").innerHTML=messages.map(m=>
   `<div class="msg"><b>${escapeHTML(m.name||"بازیکن")}:</b>
   ${escapeHTML(m.text||"")}</div>`
  ).join("");

  $("chatMessages").scrollTop=$("chatMessages").scrollHeight;
 });
}

function escapeHTML(s){
 return String(s).replace(/[&<>"']/g,c=>({
  "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
 }[c]));
}

$("chatForm").addEventListener("submit",async e=>{
 e.preventDefault();

 const text=$("chatInput").value.trim();
 if(!text||!db||!user)return;

 $("chatInput").value="";

 try{
  await push(ref(db,"globalChat"),{
   uid:user.uid,
   name:localStorage.starHunterName||"بازیکن",
   text,
   time:Date.now()
  });
 }catch(err){
  $("chatStatus").textContent="پیام ارسال نشد.";
 }
});

/* ========================================
   4. لیدربرد آنلاین
======================================== */

function loadLeaderboard(){
 if(!db)return;

 onValue(query(
  ref(db,"players"),
  orderByChild("record"),
  limitToLast(20)
 ),snapshot=>{
  let rows=[];
  snapshot.forEach(child=>{
   rows.push(child.val());
  });

  rows.sort((a,b)=>b.record-a.record);

  $("leaderList").innerHTML=rows.map((p,i=>
   `<div class="card">🏅 ${i+1}. ${escapeHTML(p.name||"بازیکن")}
   <span style="float:left">⭐ ${p.record||0}</span></div>`
  ).join("")||"هنوز رکوردی ثبت نشده.");
 });
}

async function publishRecord(){
 if(!db||!user)return;

 const playerRef=ref(db,"players/"+user.uid);

 // رکورد فقط وقتی بهتر شد به‌روزرسانی می‌شود.
 const current=profile.record;

 try{
  await update(playerRef,{
   name:localStorage.starHunterName||"بازیکن",
   record:current,
   uid:user.uid
  });
 }catch(e){
  console.error("Record upload failed",e);
 }
}

/* ========================================
   5. موتور بازی
======================================== */

const canvas=$("canvas");
const ctx=canvas.getContext("2d");

let W,H,stars=[],bullets=[],enemies=[],particles=[];
let player,score=0,lives=3,frame=0;
let running=false,lastShot=0;
let keys={},touch={left:false,right:false,fire:false};

function resize(){
 W=canvas.clientWidth;
 H=canvas.clientHeight;

 const dpr=devicePixelRatio||1;
 canvas.width=W*dpr;
 canvas.height=H*dpr;
 ctx.setTransform(dpr,0,0,dpr,0,0);

 stars=Array.from({length:110},()=>({
  x:Math.random()*W,y:Math.random()*H,
  speed:Math.random()*2+.4
 }));
}
window.addEventListener("resize",resize);
resize();

function drawShip(){
 const skin=equipment.find(x=>x.id===profile.skin)||equipment[0];

 ctx.save();
 ctx.translate(player.x,player.y);
 ctx.shadowBlur=20;
 ctx.shadowColor=skin.color;

 ctx.fillStyle="#ff9c49";
 ctx.beginPath();
 ctx.moveTo(-6,12);
 ctx.lineTo(0,29+Math.random()*6);
 ctx.lineTo(6,12);
 ctx.fill();

 ctx.fillStyle=skin.color;
 ctx.beginPath();
 ctx.moveTo(0,-24);
 ctx.lineTo(18,16);
 ctx.lineTo(0,10);
 ctx.lineTo(-18,16);
 ctx.closePath();
 ctx.fill();

 ctx.fillStyle="#efffff";
 ctx.fillRect(-4,-8,8,10);

 ctx.restore();
}

function drawEnemy(e){
 ctx.save();
 ctx.translate(e.x,e.y);
 ctx.shadowBlur=16;
 ctx.shadowColor=e.color;
 ctx.fillStyle=e.color;

 ctx.beginPath();
 ctx.moveTo(-20,-8);
 ctx.lineTo(-8,-16);
 ctx.lineTo(0,-10);
 ctx.lineTo(8,-16);
 ctx.lineTo(20,-8);
 ctx.lineTo(13,11);
 ctx.lineTo(0,16);
 ctx.lineTo(-13,11);
 ctx.closePath();
 ctx.fill();

 ctx.fillStyle="#fff";
 ctx.fillRect(-7,-4,4,4);
 ctx.fillRect(4,-4,4,4);

 ctx.restore();
}

function burst(x,y,color){
 for(let i=0;i<18;i++){
  const a=Math.random()*Math.PI*2;
  const s=Math.random()*4+1;

  particles.push({
   x,y,vx:Math.cos(a)*s,vy:Math.sin(a)*s,
   life:28,color
  });
 }
}

function startGame(){
 score=0;lives=3;frame=0;
 bullets=[];enemies=[];particles=[];
 player={x:W/2,y:H-60};
 running=true;

 $("score").textContent=0;
 $("lives").textContent=3;
 $("status").textContent="دشمن‌ها را نابود کن!";
}
$("start").onclick=startGame;

// سلاح‌ها تیرهای متفاوت دارند.
function fireWeapon(){
 const weapon=profile.weapon;

 if(weapon==="normal"){
  bullets.push({x:player.x,y:player.y-25,vx:0});
 }
 else if(weapon==="machine"){
  // مسلسل: شلیک سریع‌تر
  bullets.push({x:player.x,y:player.y-25,vx:0});
 }
 else if(weapon==="triple"){
  [-1,0,1].forEach(v=>
   bullets.push({x:player.x+v*12,y:player.y-25,vx:v*1.3})
  );
 }
 else if(weapon==="rapid"){
  bullets.push({x:player.x,y:player.y-25,vx:0});
 }
 else if(weapon==="spread"){
  [-2,-1,0,1,2].forEach(v=>
   bullets.push({x:player.x+v*8,y:player.y-25,vx:v*1.5})
  );
 }
}

function spawnEnemy(){
 enemies.push({
  x:25+Math.random()*(W-50),
  y:-25,
  speed:1.6+Math.min(score/1000,3),
  hp:1,
  color:["#ff4b75","#ffad42","#b16bff"][Math.floor(Math.random()*3)]
 });
}

function finish(){
 running=false;

 const reward=Math.floor(score/10);
 profile.coins+=reward;

 if(score>profile.record){
  profile.record=score;
 }

 saveLocal();
 refresh();
 publishRecord();

 $("status").textContent=
  `پایان بازی! امتیاز ${score} و جایزه ${reward} سکه.`;
}

function loop(){
 requestAnimationFrame(loop);

 ctx.clearRect(0,0,W,H);
 ctx.fillStyle="#030611";
 ctx.fillRect(0,0,W,H);

 // ستاره‌های پس‌زمینه
 for(const s of stars){
  s.y+=s.speed;
  if(s.y>H){s.y=0;s.x=Math.random()*W;}
  ctx.fillStyle="#d8edff";
  ctx.globalAlpha=.4;
  ctx.fillRect(s.x,s.y,2,2);
 }
 ctx.globalAlpha=1;

 if(!running)return;

 frame++;

 if(keys.ArrowLeft||keys.a||touch.left)player.x-=5;
 if(keys.ArrowRight||keys.d||touch.right)player.x+=5;
 if(keys.ArrowUp||keys.w)player.y-=4;
 if(keys.ArrowDown||keys.s)player.y+=4;

 player.x=Math.max(20,Math.min(W-20,player.x));
 player.y=Math.max(H*.4,Math.min(H-30,player.y));

 drawShip();

 const cooldown=profile.weapon==="machine"?4:
                profile.weapon==="rapid"?2:10;

 if((keys.Space||touch.fire)&&frame-lastShot>=cooldown){
  lastShot=frame;
  fireWeapon();
 }

 bullets=bullets.filter(b=>{
  b.y-=9;
  b.x+=b.vx||0;

  ctx.shadowBlur=13;
  ctx.shadowColor="#6fffff";
  ctx.fillStyle="#d7ffff";
  ctx.fillRect(b.x-2,b.y,4,15);
  ctx.shadowBlur=0;

  return b.y>-20;
 });

 if(frame%42===0)spawnEnemy();

 enemies=enemies.filter(e=>{
  e.y+=e.speed;
  drawEnemy(e);

  for(const b of bullets){
   if(Math.abs(b.x-e.x)<22&&Math.abs(b.y-e.y)<22){
    b.y=-100;
    e.hp--;
    burst(e.x,e.y,e.color);

    if(e.hp<=0){
     score+=10;
     return false;
    }
   }
  }

  if(Math.abs(player.x-e.x)<27&&Math.abs(player.y-e.y)<27){
   burst(player.x,player.y,"#ff4c8c");
   lives--;
   $("lives").textContent=lives;

   if(lives<=0)finish();
   else player.x=W/2;

   return false;
  }

  if(e.y>H+20){
   lives--;
   $("lives").textContent=lives;
   if(lives<=0)finish();
   return false;
  }

  return true;
 });

 particles=particles.filter(p=>{
  p.x+=p.vx;p.y+=p.vy;p.life--;

  ctx.globalAlpha=p.life/28;
  ctx.fillStyle=p.color;
  ctx.fillRect(p.x,p.y,4,4);
  ctx.globalAlpha=1;

  return p.life>0;
 });

 $("score").textContent=score;
}

loop();

// کیبورد
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

// دکمه‌های لمسی
function bindTouch(id,prop){
 const el=$(id);

 el.addEventListener("pointerdown",e=>{
  e.preventDefault();
  touch[prop]=true;
  el.setPointerCapture(e.pointerId);
 });

 const release=()=>touch[prop]=false;
 el.addEventListener("pointerup",release);
 el.addEventListener("pointercancel",release);
 el.addEventListener("lostpointercapture",release);
}

bindTouch("left","left");
bindTouch("right","right");
bindTouch("shoot","fire");

refresh();
</script>
</body>
</html>

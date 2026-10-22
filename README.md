<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Star Hunter | بازی فضایی</title>
<style>
*{box-sizing:border-box}
body{
  margin:0;background:#050713;color:#fff;
  font-family:Tahoma,Arial,sans-serif;overflow:hidden
}
canvas{display:block;width:100vw;height:100vh}
#top{
 position:fixed;top:12px;left:12px;right:12px;
 display:flex;justify-content:space-between;align-items:center;
 gap:10px;pointer-events:none
}
.panel{
 background:#10172bd9;border:1px solid #5368a8;
 border-radius:12px;padding:10px 15px;
 box-shadow:0 0 20px #436cff22;font-size:13px
}
#leader{min-width:155px}
#leader div{margin-top:5px;color:#b9c9ff}
#score{font-size:18px;color:#70faff}
#menu{
 position:fixed;inset:0;display:flex;align-items:center;
 justify-content:center;background:#030615bb;backdrop-filter:blur(5px)
}
.box{
 width:min(420px,90vw);padding:28px;text-align:center;
 border:1px solid #5269c9;border-radius:20px;
 background:linear-gradient(145deg,#111a39,#080b1b);
 box-shadow:0 0 50px #395eff33
}
h1{color:#8bf8ff;text-shadow:0 0 20px #16bfff}
button,select{
 background:#18264b;color:white;border:1px solid #6388ff;
 border-radius:9px;padding:11px 16px;cursor:pointer;font-size:14px
}
button:hover{background:#304c91}
#skins{margin:14px 0}
.small{font-size:12px;color:#aab9df;line-height:2}
#touch{position:fixed;bottom:22px;left:0;right:0;display:none;
 justify-content:space-between;padding:0 25px;pointer-events:none}
#touch button{pointer-events:auto;font-size:22px;width:65px;height:60px;
 background:#14244bc9}
@media (pointer:coarse){#touch{display:flex}}
</style>
</head>
<body>
<canvas id="game"></canvas>

<div id="top">
 <div class="panel">⭐ امتیاز: <b id="score">0</b><br>
  🏆 رکورد: <b id="best">0</b></div>
 <div class="panel" id="leader"><b>🏆 لیدربرد محلی</b>
  <div id="leaders">هنوز رکوردی نیست</div>
 </div>
</div>

<div id="menu">
 <div class="box">
  <h1>🚀 STAR HUNTER</h1>
  <p>به جنگ کهکشان برو!</p>
  <div id="skins">
   <label>اسکین سفینه:</label><br><br>
   <select id="skin">
    <option value="#55f6ff">نیلی — کلاسیک</option>
    <option value="#ff62df">پینک فانتوم</option>
    <option value="#a5ff63">سبز نوا</option>
    <option value="#ffb454">طلایی خورشیدی</option>
    <option value="#a98bff">بنفش سایه</option>
   </select>
  </div>
  <button id="start">شروع بازی</button>
  <p class="small">
   حرکت: کلیدهای جهت‌دار یا WASD<br>
   شلیک: Space — روی موبایل از دکمه‌ها استفاده کن<br>
   هرچه بیشتر زنده بمانی، دشمن‌ها قوی‌تر می‌شوند!
  </p>
 </div>
</div>

<div id="touch">
 <button id="left">◀</button>
 <button id="fire">🔥</button>
 <button id="right">▶</button>
</div>

<script>
const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");
let W,H,stars=[],bullets=[],enemies=[],particles=[];
let player,score=0,best=+(localStorage.best||0);
let running=false,frame=0,keys={},touch={left:false,right:false,fire:false};
let lastShot=0,enemyTimer=0;
const $=id=>document.getElementById(id);

function resize(){
 W=canvas.width=innerWidth*devicePixelRatio;
 H=canvas.height=innerHeight*devicePixelRatio;
 ctx.setTransform(devicePixelRatio,0,0,devicePixelRatio,0,0);
 W=innerWidth;H=innerHeight;
 stars=Array.from({length:150},()=>({
  x:Math.random()*W,y:Math.random()*H,
  z:Math.random()*2+0.5
 }));
}
addEventListener("resize",resize);resize();

function updateBoard(){
 $("best").textContent=best;
 let list=JSON.parse(localStorage.records||"[]");
 $("leaders").innerHTML=list.length?
 list.slice(0,5).map((s,i)=>`${i+1}. ${s} امتیاز`).join("<br>"):
 "هنوز رکوردی نیست";
}
updateBoard();

function newGame(){
 score=0;frame=0;bullets=[];enemies=[];particles=[];
 player={x:W/2,y:H-100,hp:3,color:$("skin").value};
 running=true;
 $("score").textContent=0;
 $("menu").style.display="none";
}

$("start").onclick=newGame;

function ship(x,y,color,size=1){
 ctx.save();ctx.translate(x,y);ctx.scale(size,size);
 ctx.shadowBlur=22;ctx.shadowColor=color;ctx.fillStyle=color;
 ctx.beginPath();ctx.moveTo(0,-24);ctx.lineTo(18,18);
 ctx.lineTo(0,10);ctx.lineTo(-18,18);ctx.closePath();ctx.fill();
 ctx.fillStyle="#e9ffff";
 ctx.beginPath();ctx.moveTo(0,-12);ctx.lineTo(5,5);
 ctx.lineTo(-5,5);ctx.fill();
 ctx.restore();
}

function explosion(x,y,color){
 for(let i=0;i<15;i++){
  let a=Math.random()*Math.PI*2,s=Math.random()*4+1;
  particles.push({x,y,vx:Math.cos(a)*s,vy:Math.sin(a)*s,
   life:30,color});
 }
}

function spawnEnemy(){
 const boss=frame>0&&frame%1000<35;
 enemies.push({
  x:Math.random()*(W-70)+35,y:-40,
  hp:boss?12:1,boss,
  speed:boss?1.2+score/3000:1.8+score/1500,
  color:boss?"#ff3b92":["#ff4b75","#ffac42","#b26bff"][Math.floor(Math.random()*3)]
 });
}

function drawEnemy(e){
 ctx.save();ctx.translate(e.x,e.y);
 ctx.shadowBlur=18;ctx.shadowColor=e.color;ctx.fillStyle=e.color;
 ctx.beginPath();ctx.moveTo(0,20);ctx.lineTo(25,-15);
 ctx.lineTo(8,-8);ctx.lineTo(0,-22);ctx.lineTo(-8,-8);
 ctx.lineTo(-25,-15);ctx.closePath();ctx.fill();
 if(e.boss){
  ctx.fillStyle="#fff";ctx.font="12px Arial";
  ctx.fillText("BOSS", -15,-30);
  ctx.fillStyle="#ff4b75";ctx.fillRect(-25,-38,50,4);
  ctx.fillStyle="#4dffbc";ctx.fillRect(-25,-38,50*e.hp/12,4);
 }
 ctx.restore();
}

function endGame(){
 running=false;
 best=Math.max(best,score);
 localStorage.best=best;
 let list=JSON.parse(localStorage.records||"[]");
 list.push(score);list.sort((a,b)=>b-a);
 localStorage.records=JSON.stringify(list.slice(0,5));
 updateBoard();
 $("menu").style.display="flex";
 $("menu").querySelector("h1").textContent="پایان مأموریت!";
 $("menu").querySelector("p").textContent="امتیاز تو: "+score+" | رکورد: "+best;
 $("start").textContent="دوباره بازی کن";
}

function loop(){
 requestAnimationFrame(loop);
 ctx.clearRect(0,0,W,H);

 // کهکشان و ستاره‌های متحرک
 let bg=ctx.createRadialGradient(W/2,H/2,10,W/2,H/2,W);
 bg.addColorStop(0,"#101b42");bg.addColorStop(1,"#03040c");
 ctx.fillStyle=bg;ctx.fillRect(0,0,W,H);
 for(let s of stars){
  s.y+=s.z;
  if(s.y>H){s.y=0;s.x=Math.random()*W}
  ctx.fillStyle=`rgba(170,210,255,${s.z/3})`;
  ctx.fillRect(s.x,s.y,s.z,s.z);
 }

 if(!running)return;
 frame++;
 $("score").textContent=score;

 let speed=5;
 if(keys.ArrowLeft||keys.a||touch.left)player.x-=speed;
 if(keys.ArrowRight||keys.d||touch.right)player.x+=speed;
 if(keys.ArrowUp||keys.w)player.y-=speed;
 if(keys.ArrowDown||keys.s)player.y+=speed;
 player.x=Math.max(25,Math.min(W-25,player.x));
 player.y=Math.max(H*.45,Math.min(H-40,player.y));

 ship(player.x,player.y,player.color,1.1);

 // شلیک
 if((keys.Space||touch.fire)&&frame-lastShot>10){
  lastShot=frame;
  bullets.push({x:player.x,y:player.y-25,vy:-10});
  particles.push({x:player.x,y:player.y+20,vx:0,vy:3,life:10,color:"#65faff"});
 }
 bullets=bullets.filter(b=>{
  b.y+=b.vy;
  ctx.shadowBlur=14;ctx.shadowColor="#66ffff";
  ctx.fillStyle="#aaffff";ctx.fillRect(b.x-2,b.y,4,16);
  ctx.shadowBlur=0;
  return b.y>-20;
 });

 if(frame-enemyTimer>Math.max(18,55-score/100)){
  enemyTimer=frame;spawnEnemy();
 }

 enemies=enemies.filter(e=>{
  e.y+=e.speed;
  drawEnemy(e);
  for(let b of bullets){
   if(Math.abs(b.x-e.x)<25&&Math.abs(b.y-e.y)<25){
    b.y=-100;
    e.hp--;
    explosion(e.x,e.y,e.color);
    if(e.hp<=0){score+=e.boss?100:10;return false}
   }
  }
  if(Math.abs(player.x-e.x)<28&&Math.abs(player.y-e.y)<30){
   explosion(player.x,player.y,"#ff4c87");
   endGame();return false;
  }
  if(e.y>H+50){score+=1;return false}
  return true;
 });

 particles=particles.filter(p=>{
  p.x+=p.vx;p.y+=p.vy;p.life--;
  ctx.globalAlpha=Math.max(0,p.life/30);
  ctx.fillStyle=p.color;ctx.fillRect(p.x,p.y,4,4);
  ctx.globalAlpha=1;
  return p.life>0;
 });
}
loop();

addEventListener("keydown",e=>{
 keys[e.key]=true;
 if(["ArrowUp","ArrowDown","ArrowLeft","ArrowRight"," "].includes(e.key))
  e.preventDefault();
 if(e.code==="Space")keys.Space=true;
});
addEventListener("keyup",e=>{
 keys[e.key]=false;
 if(e.code==="Space")keys.Space=false;
});

function bind(id,prop){
 const el=$(id);
 el.addEventListener("pointerdown",e=>{e.preventDefault();touch[prop]=true});
 ["pointerup","pointercancel","pointerleave"].forEach(ev=>
  el.addEventListener(ev,()=>touch[prop]=false));
}
bind("left","left");bind("right","right");bind("fire","fire");
</script>
</body>
</html>

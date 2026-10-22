<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<title>تیرباز فضایی 🚀</title>

<style>
:root {
  --bg:#030610;
  --panel:#06151d;
  --line:#10404b;
  --cyan:#00f0e5;
  --pink:#e84bd7;
  --gold:#ffd91a;
  --text:#e9f7ff;
  --muted:#8aa9b8;
}

* { box-sizing:border-box; }

body {
  margin:0;
  background:radial-gradient(ellipse at top,#0a1b2a,#030610 65%);
  color:var(--text);
  font-family:system-ui,-apple-system,"Segoe UI",Tahoma,sans-serif;
}

header {
  position:sticky;
  top:0;
  z-index:10;
  background:#030610ee;
  border-bottom:1px solid #12323e;
  padding:10px 8px 0;
}

.brand {
  text-align:center;
  font-weight:900;
  font-size:23px;
  padding:5px;
  background:linear-gradient(90deg,#65eaff,#e77aff);
  -webkit-background-clip:text;
  color:transparent;
}

nav {
  display:flex;
  justify-content:space-around;
  gap:3px;
  margin-top:8px;
}

nav button {
  flex:1;
  padding:12px 2px;
  border:0;
  border-bottom:3px solid transparent;
  background:transparent;
  color:#7895a5;
  font-weight:800;
  font-size:13px;
}

nav button.active {
  color:#8ffaff;
  border-bottom-color:var(--cyan);
  text-shadow:0 0 12px #00ffff77;
}

main {
  width:100%;
  max-width:760px;
  margin:auto;
  padding:12px;
}

.view { display:none; }
.view.active { display:block; }

h2 {
  text-align:center;
  font-size:25px;
  margin:15px 0 20px;
  color:#9ceaff;
  text-shadow:0 0 15px #00ffff22;
}

.panel {
  background:linear-gradient(145deg,#06171d,#030911);
  border:1px solid #0c4149;
  border-radius:22px;
  padding:15px;
  margin:13px 0;
  box-shadow:0 0 15px #00e5e508;
}

button { cursor:pointer; }

.btn {
  border:1px solid var(--cyan);
  border-radius:30px;
  background:#03191e;
  color:var(--cyan);
  padding:11px 17px;
  font-weight:800;
  font-size:14px;
}

.btn.primary {
  background:var(--cyan);
  color:#001419;
  box-shadow:0 0 18px #00e5e544;
}

.btn:disabled {
  opacity:.4;
  cursor:not-allowed;
}

.pills {
  display:flex;
  gap:8px;
}

.pills button { flex:1; }

.pills .selected {
  background:var(--cyan);
  color:#001419;
}

.small {
  font-size:12px;
  color:var(--muted);
}

.notice {
  font-size:12px;
  line-height:1.9;
  color:var(--muted);
}

input {
  width:100%;
  padding:12px;
  margin:7px 0;
  color:white;
  background:#020a12;
  border:1px solid #12505a;
  border-radius:13px;
  font-size:16px;
}

/* بازی */

#gamebox {
  position:relative;
  width:100%;
  height:min(72vh,650px);
  min-height:420px;
  background:#01040b;
  border:1px solid #14505a;
  border-radius:25px;
  overflow:hidden;
  box-shadow:0 0 22px #00ffff0b;
}

#gamecanvas {
  width:100%;
  height:100%;
  display:block;
  touch-action:none;
}

.hud {
  position:absolute;
  top:12px;
  left:12px;
  right:12px;
  display:flex;
  justify-content:space-between;
  gap:5px;
  font-size:14px;
  font-weight:900;
  color:#9cffff;
  text-shadow:0 0 10px #00eaff;
  pointer-events:none;
}

.gamehint {
  color:var(--muted);
  font-size:13px;
  text-align:center;
  line-height:1.8;
  padding:8px 4px;
}

.gamebuttons {
  display:flex;
  justify-content:center;
  gap:10px;
  flex-wrap:wrap;
}

/* فروشگاه */

.item {
  display:grid;
  grid-template-columns:100px 1fr;
  gap:12px;
  align-items:center;
  padding:13px;
  margin:12px 0;
  background:linear-gradient(140deg,#03130f,#030911);
  border:1px solid #15552c;
  border-radius:20px;
}

.item-art {
  width:95px;
  height:95px;
  display:grid;
  place-items:center;
  border-radius:17px;
  background:#07141d;
  border:1px solid #14343e;
  font-size:52px;
  filter:drop-shadow(0 0 8px #00eaff33);
}

.item h3 {
  margin:0 0 5px;
  font-size:17px;
}

.item p {
  margin:4px 0;
  font-size:12px;
  color:var(--muted);
}

.item-actions {
  display:flex;
  align-items:center;
  gap:8px;
  flex-wrap:wrap;
  margin-top:9px;
}

.price {
  color:var(--gold);
  font-weight:900;
}

/* رتبه‌بندی */

.rankrow {
  display:grid;
  grid-template-columns:40px 1fr auto;
  align-items:center;
  gap:10px;
  padding:12px;
  margin:9px 0;
  border:1px solid #10404b;
  border-radius:17px;
  background:#04121a;
}

.ranknum {
  width:35px;
  height:35px;
  border-radius:50%;
  display:grid;
  place-items:center;
  background:#07313a;
  color:#8ffaff;
  font-weight:900;
}

.ranknum.top {
  background:var(--gold);
  color:#251900;
}

.rankplayer {
  display:flex;
  align-items:center;
  gap:9px;
  min-width:0;
}

.rankavatar {
  font-size:25px;
  width:42px;
  height:42px;
  display:grid;
  place-items:center;
  background:#0a101b;
  border-radius:12px;
}

/* پروفایل */

.profilegrid {
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:12px;
}

.profilecard {
  text-align:center;
}

.bigavatar {
  font-size:60px;
  margin:8px;
  filter:drop-shadow(0 0 13px #00eaff66);
}

.avatars {
  display:flex;
  flex-wrap:wrap;
  gap:7px;
  margin-top:8px;
}

.avatars button {
  font-size:24px;
  border:1px solid transparent;
  border-radius:12px;
  background:#0b111c;
  padding:7px;
}

.avatars button.chosen {
  border-color:var(--cyan);
  box-shadow:0 0 12px #00ffff55;
}

.toggle {
  display:flex;
  align-items:center;
  justify-content:space-between;
}

.toggle input {
  width:22px;
  height:22px;
  accent-color:var(--cyan);
}

@media(max-width:420px) {
  nav button { font-size:12px; }
  .item { grid-template-columns:75px 1fr; }
  .item-art { width:72px;height:72px;font-size:40px; }
  .profilegrid { grid-template-columns:1fr; }
}
</style>
</head>

<body>

<header>
  <div class="brand">🚀 تیرباز فضایی</div>
  <nav>
    <button class="active" data-tab="game">🎮 بازی</button>
    <button data-tab="store">🛍 فروشگاه</button>
    <button data-tab="ranks">🏆 رتبه‌ها</button>
    <button data-tab="profile">👤 پروفایل</button>
  </nav>
</header>

<main>

<!-- ================= بازی ================= -->

<section id="game" class="view active">

  <h2>ماموریت فضایی</h2>

  <div id="gamebox">

    <canvas id="gamecanvas"></canvas>

    <div class="hud">
      <span>❤️ <b id="lives">3</b></span>
      <span>🪙 <b id="coins">100</b></span>
      <span>⭐ <b id="score">0</b></span>
    </div>

  </div>

  <div class="gamehint">
    سفینه را روی صفحه حرکت بده. شلیک خودکار است.
    دشمن‌ها را نابود کن تا امتیاز و سکه بگیری.
  </div>

  <div class="gamebuttons">
    <button class="btn" id="pause">⏸ توقف</button>
    <button class="btn primary" id="restart">شروع دوباره</button>
  </div>

</section>

<!-- ================= فروشگاه ================= -->

<section id="store" class="view">

  <h2>🛒 فروشگاه</h2>

  <div class="panel" style="text-align:center;color:var(--gold);font-weight:900">
    موجودی شما: <span id="storecoins">100</span> 🪙
  </div>

  <div class="pills">
    <button class="btn selected" data-kind="weapons">🔫 سلاح‌ها</button>
    <button class="btn" data-kind="ships">🚀 سفینه‌ها</button>
  </div>

  <div id="storeitems"></div>

</section>

<!-- ================= رتبه‌بندی ================= -->

<section id="ranks" class="view">

  <h2>🏆 رتبه‌بندی بازیکنان</h2>

  <div style="text-align:center">
    <button class="btn" id="refreshRanks">🔄 بروزرسانی</button>
  </div>

  <div id="ranklist"></div>

  <div class="panel">
    <p class="notice">
      بازیکنان با امتیاز بیشتر در رتبه‌های بالاتر قرار می‌گیرند.
      این نمونه رتبه‌ها را در همین مرورگر نگه می‌دارد.
      برای جدول مشترک بین همه بازیکنان، اتصال به سرور لازم است.
    </p>
  </div>

</section>

<!-- ================= پروفایل ================= -->

<section id="profile" class="view">

  <h2>👤 پروفایل خلبان</h2>

  <div class="panel profilegrid">

    <div class="profilecard">

      <div class="bigavatar" id="profileavatar">👻</div>

      <b id="profilename">خلبان</b>

      <p class="small">SPACE COMMANDER</p>

      <hr style="border-color:#10343c">

      <b style="color:var(--gold)">
        امتیاز: <span id="profilescore">0</span> ⭐
      </b>

    </div>

    <div>

      <label>نام خلبان:</label>

      <input id="nameinput" maxlength="20" placeholder="نام دلخواه">

      <label class="small">انتخاب آواتار:</label>

      <div class="avatars" id="avatars"></div>

    </div>

  </div>

  <div class="panel toggle">
    <b>🔊 افکت‌های صوتی</b>
    <input type="checkbox" id="sound" checked>
  </div>

  <div class="panel">

    <b style="color:var(--gold)">کد اختصاصی اکانت</b>

    <h2 id="playercode" style="color:white">SPC-000000</h2>

    <p class="notice">
      کد خودت را نگه دار تا بتوانی پروفایلت را در این مرورگر پیدا کنی.
    </p>

  </div>

  <div class="panel">

    <h3>🔎 یافتن اکانت با کد</h3>

    <input id="lookupcode" placeholder="کد بازیکن، مثلاً SPC-AB12CD">

    <button class="btn primary" id="lookupbutton">
      یافتن اکانت
    </button>

    <div id="lookupresult" class="notice"></div>

  </div>

  <div style="text-align:center">
    <button class="btn primary" id="saveprofile">ذخیره پروفایل</button>
  </div>

</section>

</main>

<script>
(() => {

const $ = selector => document.querySelector(selector);
const $$ = selector => [...document.querySelectorAll(selector)];

const AVATARS = [
  '🔷','👾','🟨','🔵','🟣',
  '🔺','🌟','👻','🚀','🛸'
];

const SAVE_KEY = 'space_game_accounts_v2';
const CURRENT_KEY = 'space_game_current_v2';

function readAccounts() {
  try {
    return JSON.parse(localStorage.getItem(SAVE_KEY) || '{}');
  } catch {
    return {};
  }
}

function writeAccounts(accounts) {
  localStorage.setItem(SAVE_KEY, JSON.stringify(accounts));
}

function makeCode() {
  return 'SPC-' + Math.random().toString(36).slice(2,8).toUpperCase();
}

function newAccount() {
  const code = makeCode();

  return {
    code,
    name: '',
    avatar: '👻',
    score: 0,
    coins: 100,
    lives: 3,
    sound: true,
    ship: 0,
    weapon: 0,
    ownedShips: [0],
    ownedWeapons: [0]
  };
}

let accounts = readAccounts();
let currentCode = localStorage.getItem(CURRENT_KEY);

if (!currentCode || !accounts[currentCode]) {
  const account = newAccount();
  currentCode = account.code;
  accounts[currentCode] = account;
  localStorage.setItem(CURRENT_KEY, currentCode);
  writeAccounts(accounts);
}

let data = accounts[currentCode];

function save() {
  accounts[currentCode] = data;
  writeAccounts(accounts);
  localStorage.setItem(CURRENT_KEY, currentCode);
  updateHUD();
}

function safeName(account) {
  if (account.name && account.name.trim()) {
    return account.name.trim();
  }

  return 'خلبان ' + account.code.slice(-4);
}

function escapeHTML(text) {
  return String(text).replace(/[&<>"']/g, c => ({
    '&':'&amp;',
    '<':'&lt;',
    '>':'&gt;',
    '"':'&quot;',
    "'":'&#39;'
  }[c]));
}

function updateHUD() {
  $('#score').textContent = data.score;
  $('#coins').textContent = data.coins;
  $('#storecoins').textContent = data.coins;
  $('#lives').textContent = data.lives;
  $('#profilescore').textContent = data.score;
  $('#profilename').textContent = safeName(data);
  $('#profileavatar').textContent = data.avatar;
  $('#playercode').textContent = data.code;
}

/* پیمایش تب‌ها */

$$('nav button').forEach(button => {
  button.onclick = () => {

    $$('nav button').forEach(b =>
      b.classList.toggle('active', b === button)
    );

    $$('.view').forEach(view =>
      view.classList.toggle('active', view.id === button.dataset.tab)
    );

    if (button.dataset.tab === 'store') renderStore();
    if (button.dataset.tab === 'ranks') renderRanks();
    if (button.dataset.tab === 'profile') renderProfile();
    if (button.dataset.tab === 'game') resizeCanvas();
  };
});

/* داده‌های ۱۰ سلاح و ۱۰ سفینه */

const weapons = [
  ['لیزر ساده','🔴',200,'شلیک معمولی'],
  ['لیزر دوقلو','🔫',450,'دو گلوله در هر نوبت'],
  ['لیزر سریع','⚡',650,'سرعت شلیک بیشتر'],
  ['پلاسما','💠',850,'قدرت ضربه بیشتر'],
  ['پرتابگر موشک','🚀',1100,'موشک‌های قدرتمند'],
  ['لیزر سه‌گانه','🔱',1400,'سه مسیر شلیک'],
  ['توپ انرژی','🟣',1750,'پرتابه انرژی'],
  ['شلیک انفجاری','💥',2200,'آسیب ناحیه‌ای'],
  ['لیزر یخی','❄️',2700,'کند کردن دشمن'],
  ['سلاح کهکشانی','🌌',3500,'سلاح نهایی']
];

const ships = [
  ['سفینه پایه','🚀',0,'متعادل'],
  ['شبح نئونی','👻',350,'چابک'],
  ['شکارچی ستاره','🛸',600,'سرعت بالا'],
  ['بال طلایی','🌟',850,'مقاومت بیشتر'],
  ['سفینه الماسی','💎',1100,'حرکت دقیق'],
  ['سفینه آذرخش','⚡',1450,'سرعت زیاد'],
  ['سفینه پلاسما','🔮',1800,'بدنه مقاوم'],
  ['سفینه سایه','🌑',2300,'جنگنده پیشرفته'],
  ['سفینه ققنوس','🔥',2900,'سفینه ویژه'],
  ['ناو کهکشانی','🌌',4000,'ناو نهایی']
];

let storeKind = 'weapons';

$$('[data-kind]').forEach(button => {
  button.onclick = () => {
    storeKind = button.dataset.kind;

    $$('[data-kind]').forEach(b =>
      b.classList.toggle('selected', b === button)
    );

    renderStore();
  };
});

function renderStore() {

  const list = storeKind === 'weapons' ? weapons : ships;
  const owned = storeKind === 'weapons'
    ? data.ownedWeapons
    : data.ownedShips;

  const selected = storeKind === 'weapons'
    ? data.weapon
    : data.ship;

  $('#storeitems').innerHTML = list.map((item, i) => {

    const has = owned.includes(i);
    const equipped = selected === i;

    return `
      <div class="item">

        <div class="item-art">${item[1]}</div>

        <div>

          <h3>${item[0]}</h3>

          <p>${item[3]}</p>

          <p>
            ${equipped ? 'مجهز' : has ? 'خریداری‌شده' : 'قابل خرید'}
          </p>

          <div class="item-actions">

            <span class="price">${item[2] || 'رایگان'} 🪙</span>

            <button class="btn ${equipped ? 'primary' : ''}"
                    data-purchase="${i}">

              ${equipped ? 'مجهز' : has ? 'تجهیز' : 'خرید'}

            </button>

          </div>

        </div>

      </div>
    `;
  }).join('');

  $$('[data-purchase]').forEach(button => {

    button.onclick = () => {

      const i = Number(button.dataset.purchase);
      const owned = storeKind === 'weapons'
        ? data.ownedWeapons
        : data.ownedShips;

      const price = list[i][2];

      if (!owned.includes(i)) {

        if (data.coins < price) {
          alert('سکه کافی نیست!');
          return;
        }

        data.coins -= price;
        owned.push(i);
      }

      if (storeKind === 'weapons') {
        data.weapon = i;
      } else {
        data.ship = i;
      }

      save();
      renderStore();
      playTone(600);
    };
  });
}

/* پروفایل و آواتار */

function renderProfile() {

  $('#nameinput').value = data.name;
  $('#sound').checked = data.sound;

  $('#avatars').innerHTML = AVATARS.map(avatar => `
    <button class="${avatar === data.avatar ? 'chosen' : ''}"
            data-avatar="${avatar}">
      ${avatar}
    </button>
  `).join('');

  $$('[data-avatar]').forEach(button => {
    button.onclick = () => {
      data.avatar = button.dataset.avatar;
      renderProfile();
      updateHUD();
    };
  });

  updateHUD();
}

$('#saveprofile').onclick = () => {

  data.name = $('#nameinput').value.trim();
  data.sound = $('#sound').checked;

  save();
  renderProfile();
};

/* پیدا کردن حساب با کد */

$('#lookupbutton').onclick = () => {

  const code = $('#lookupcode').value.trim().toUpperCase();
  const account = accounts[code];

  if (!account) {
    $('#lookupresult').innerHTML =
      'اکانتی با این کد در این مرورگر پیدا نشد.';
    return;
  }

  $('#lookupresult').innerHTML = `
    <div class="panel">
      <div style="font-size:35px">${account.avatar}</div>
      <b>${escapeHTML(safeName(account))}</b>
      <p>امتیاز: ${account.score} ⭐</p>
      <p>کد: ${escapeHTML(account.code)}</p>
      <button class="btn" id="switchaccount">ورود به این اکانت</button>
    </div>
  `;

  $('#switchaccount').onclick = () => {

    currentCode = account.code;
    data = accounts[currentCode];

    localStorage.setItem(CURRENT_KEY, currentCode);

    updateHUD();
    renderProfile();
    renderStore();

    $('#lookupresult').textContent =
      'اکانت انتخاب‌شده در همین مرورگر فعال شد.';
  };
};

/* رتبه‌بندی */

function renderRanks() {

  const rows = Object.values(accounts)
    .sort((a,b) => b.score - a.score)
    .slice(0,50);

  $('#ranklist').innerHTML = rows.map((account, i) => `
    <div class="rankrow">

      <div class="ranknum ${i < 3 ? 'top' : ''}">
        ${i + 1}
      </div>

      <div class="rankplayer">

        <span class="rankavatar">${account.avatar}</span>

        <span>
          <b>${escapeHTML(safeName(account))}</b>
          <div class="small">${account.code === currentCode ? 'شما' : 'خلبان فضایی'}</div>
        </span>

      </div>

      <b>${account.score} ⭐</b>

    </div>
  `).join('');
}

$('#refreshRanks').onclick = renderRanks;

/* صدا */

function playTone(freq) {

  if (!data.sound) return;

  try {
    const audio = new (window.AudioContext || window.webkitAudioContext)();
    const oscillator = audio.createOscillator();
    const gain = audio.createGain();

    oscillator.frequency.value = freq;
    gain.gain.value = 0.025;

    oscillator.connect(gain);
    gain.connect(audio.destination);

    oscillator.start();
    oscillator.stop(audio.currentTime + 0.06);

    oscillator.onended = () => audio.close();
  } catch(e) {}
}

/* موتور بازی */

const canvas = $('#gamecanvas');
const ctx = canvas.getContext('2d');

let W = 0, H = 0;
let shipX = 0, shipY = 0;

let bullets = [];
let enemies = [];
let stars = [];

let running = true;
let lastTime = 0;
let spawnTimer = 0;
let shotTimer = 0;
let saveTimer = 0;

function resizeCanvas() {

  const rect = canvas.getBoundingClientRect();

  W = canvas.width = Math.max(1, rect.width * devicePixelRatio);
  H = canvas.height = Math.max(1, rect.height * devicePixelRatio);

  shipX = W / 2;
  shipY = H * 0.86;

  stars = Array.from({length:100}, () => ({
    x:Math.random()*W,
    y:Math.random()*H,
    size:Math.random()*2+0.4,
    speed:Math.random()*1.1+0.3
  }));
}

function spawnEnemy() {

  const size = (20 + Math.random()*10) * devicePixelRatio;

  enemies.push({
    x:size + Math.random()*(W-size*2),
    y:-size,
    size,
    speed:(0.65+Math.random()*0.65)*devicePixelRatio,
    hp:3 + Math.floor(data.score/1500),
    maxHp:3 + Math.floor(data.score/1500),
    phase:Math.random()*Math.PI*2
  });
}

/* سفینه بزرگ‌تر */

function drawShip(x,y) {

  const size = 1.6 * devicePixelRatio;

  ctx.save();
  ctx.translate(x,y);
  ctx.scale(size,size);

  ctx.shadowBlur = 24;
  ctx.shadowColor = '#00fff0';

  ctx.fillStyle = '#00e7d8';

  ctx.beginPath();
  ctx.moveTo(0,-25);
  ctx.lineTo(19,17);
  ctx.lineTo(0,9);
  ctx.lineTo(-19,17);
  ctx.closePath();
  ctx.fill();

  ctx.fillStyle = '#b4ffff';
  ctx.beginPath();
  ctx.ellipse(0,-5,5,10,0,0,Math.PI*2);
  ctx.fill();

  ctx.fillStyle = '#ff65ed';
  ctx.fillRect(-5,16,10,13);

  ctx.restore();
}

/* دشمن شش‌ضلعی با چشم */

function drawEnemy(enemy) {

  const r = enemy.size;

  ctx.save();
  ctx.translate(enemy.x,enemy.y);

  ctx.shadowBlur = 22;
  ctx.shadowColor = '#ff8b18';

  ctx.fillStyle = '#ff941d';
  ctx.strokeStyle = '#ffd16b';
  ctx.lineWidth = 2*devicePixelRatio;

  ctx.beginPath();

  for (let i=0;i<6;i++) {

    const angle = Math.PI/3*i - Math.PI/6;
    const x = Math.cos(angle)*r;
    const y = Math.sin(angle)*r;

    if (i===0) ctx.moveTo(x,y);
    else ctx.lineTo(x,y);
  }

  ctx.closePath();
  ctx.fill();
  ctx.stroke();

  /* چشم‌ها */

  ctx.shadowBlur = 10;
  ctx.shadowColor = '#ff2525';
  ctx.fillStyle = '#2a0710';

  ctx.beginPath();
  ctx.ellipse(-r*.28,-r*.08,r*.18,r*.25,0,0,Math.PI*2);
  ctx.ellipse(r*.28,-r*.08,r*.18,r*.25,0,0,Math.PI*2);
  ctx.fill();

  ctx.fillStyle = '#ff3131';

  ctx.beginPath();
  ctx.arc(-r*.28,-r*.08,r*.07,0,Math.PI*2);
  ctx.arc(r*.28,-r*.08,r*.07,0,Math.PI*2);
  ctx.fill();

  /* نوار سلامتی */

  ctx.shadowBlur = 0;
  ctx.fillStyle = '#280c0c';
  ctx.fillRect(-r,-r-8*devicePixelRatio,r*2,4*devicePixelRatio);

  ctx.fillStyle = '#00f5bc';
  ctx.fillRect(
    -r,
    -r-8*devicePixelRatio,
    r*2*(enemy.hp/enemy.maxHp),
    4*devicePixelRatio
  );

  ctx.restore();
}

/* شلیک */

function shoot() {

  const level = data.weapon;

  if (level === 1 || level === 5) {

    bullets.push({x:shipX-8*devicePixelRatio,y:shipY-20*devicePixelRatio,damage:1});
    bullets.push({x:shipX+8*devicePixelRatio,y:shipY-20*devicePixelRatio,damage:1});

  } else if (level === 2 || level === 6) {

    bullets.push({x:shipX,y:shipY-20*devicePixelRatio,damage:2});

  } else if (level === 7) {

    bullets.push({x:shipX,y:shipY-20*devicePixelRatio,damage:3,radius:10*devicePixelRatio});

  } else {

    bullets.push({x:shipX,y:shipY-20*devicePixelRatio,damage:1});
  }
}

/* برخورد گلوله و دشمن */

function collide() {

  for (const enemy of enemies) {

    for (const bullet of bullets) {

      if (bullet.dead) continue;

      const distance = Math.hypot(
        enemy.x-bullet.x,
        enemy.y-bullet.y
      );

      if (distance < enemy.size + 5*devicePixelRatio) {

        bullet.dead = true;
        enemy.hp -= bullet.damage || 1;

        if (enemy.hp <= 0) {

          enemy.dead = true;

          /* امتیاز و سکه آهسته‌تر */

          data.score += 25;
          data.coins += 1;

          playTone(700);
        }

        break;
      }
    }

    /* برخورد دشمن با سفینه */

    if (
      !enemy.dead &&
      enemy.y + enemy.size > shipY - 20*devicePixelRatio &&
      Math.abs(enemy.x-shipX) < enemy.size + 15*devicePixelRatio
    ) {

      enemy.dead = true;
      data.lives--;

      playTone(150);

      if (data.lives <= 0) {
        running = false;
      }
    }
  }

  bullets = bullets.filter(b => !b.dead && b.y > -20);
  enemies = enemies.filter(e => !e.dead && e.y < H + 50);
}

/* حلقه اصلی */

function gameLoop(time) {

  requestAnimationFrame(gameLoop);

  if (!W || !H) return;

  const dt = Math.min(2,(time-lastTime)/16 || 1);

  lastTime = time;

  ctx.clearRect(0,0,W,H);

  ctx.fillStyle = '#01040b';
  ctx.fillRect(0,0,W,H);

  /* ستاره‌ها */

  for (const star of stars) {

    star.y += star.speed*dt;

    if (star.y > H) star.y = 0;

    ctx.fillStyle = `rgba(130,230,255,${0.25+star.size/3})`;

    ctx.fillRect(star.x,star.y,star.size,star.size);
  }

  if (running && data.lives > 0) {

    spawnTimer += dt;
    shotTimer += dt;
    saveTimer += dt;

    /* دشمن‌ها کمی دیرتر ظاهر می‌شوند */

    if (spawnTimer > 65) {
      spawnTimer = 0;
      spawnEnemy();
    }

    /* شلیک خودکار */

    const fireRate = data.weapon >= 2 ? 18 : 28;

    if (shotTimer > fireRate) {
      shotTimer = 0;
      shoot();
    }

    bullets.forEach(bullet => {
      bullet.y -= 7*devicePixelRatio*dt;
    });

    enemies.forEach(enemy => {
      enemy.y += enemy.speed*dt;
      enemy.x += Math.sin(time/600+enemy.phase)*0.35*devicePixelRatio;
    });

    collide();

    if (saveTimer > 120) {
      saveTimer = 0;
      save();
    }
  }

  /* گلوله‌های نورانی */

  for (const bullet of bullets) {

    ctx.shadowBlur = 13;
    ctx.shadowColor = '#00fff0';

    ctx.fillStyle = '#9fffff';

    ctx.fillRect(
      bullet.x-2*devicePixelRatio,
      bullet.y-12*devicePixelRatio,
      4*devicePixelRatio,
      16*devicePixelRatio
    );
  }

  enemies.forEach(drawEnemy);

  drawShip(shipX,shipY);

  updateHUD();

  if (!running || data.lives <= 0) {

    ctx.fillStyle = '#ff5b7d';
    ctx.font = `bold ${28*devicePixelRatio}px sans-serif`;
    ctx.textAlign = 'center';

    ctx.fillText(
      'پایان ماموریت',
      W/2,
      H/2
    );
  }
}

/* کنترل لمسی */

function moveShip(event) {

  const rect = canvas.getBoundingClientRect();

  shipX = Math.max(
    25*devicePixelRatio,
    Math.min(
      W-25*devicePixelRatio,
      (event.clientX-rect.left)*devicePixelRatio
    )
  );
}

canvas.addEventListener('pointerdown', event => {
  canvas.setPointerCapture(event.pointerId);
  moveShip(event);
});

canvas.addEventListener('pointermove', event => {
  if (event.buttons) moveShip(event);
});

/* توقف و شروع مجدد */

$('#pause').onclick = () => {

  running = !running;

  $('#pause').textContent =
    running ? '⏸ توقف' : '▶ ادامه';
};

$('#restart').onclick = () => {

  data.lives = 3;
  data.score = 0;

  bullets = [];
  enemies = [];

  spawnTimer = 0;
  shotTimer = 0;
  running = true;

  save();
};

/* راه‌اندازی */

resizeCanvas();

window.addEventListener('resize',resizeCanvas);

updateHUD();
renderStore();
renderProfile();

requestAnimationFrame(gameLoop);

})();
</script>

</body>
</html>

<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<title>تیرباز فضایی 🚀</title>

<style>
:root {
  --bg: #030610;
  --panel: #06151d;
  --line: #073b45;
  --cyan: #00f0e5;
  --pink: #e84bd7;
  --gold: #ffd91a;
  --text: #e9f7ff;
  --muted: #8aa9b8;
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: radial-gradient(ellipse at 50% 0, #091b2a 0, #030610 55%);
  color: var(--text);
  font-family: system-ui, -apple-system, "Segoe UI", Tahoma, sans-serif;
}

header {
  position: sticky;
  top: 0;
  z-index: 5;
  background: #030610ed;
  border-bottom: 1px solid #12323e;
  padding: 12px 10px 0;
}

.brand {
  text-align: center;
  font-weight: 900;
  font-size: 22px;
  background: linear-gradient(90deg, #69eaff, #e77aff);
  -webkit-background-clip: text;
  color: transparent;
  padding: 4px;
}

nav {
  display: flex;
  gap: 4px;
  justify-content: space-around;
  margin-top: 8px;
}

nav button {
  flex: 1;
  background: transparent;
  border: 0;
  color: #7e9ba9;
  font-weight: 800;
  padding: 12px 3px;
  border-bottom: 3px solid transparent;
  font-size: 14px;
}

nav button.active {
  color: #8ffaff;
  border-bottom: 3px solid var(--cyan);
  text-shadow: 0 0 12px #00ffff55;
}

main {
  max-width: 760px;
  margin: auto;
  padding: 14px 12px 35px;
}

.view {
  display: none;
}

.view.active {
  display: block;
}

h2 {
  text-align: center;
  margin: 12px 0 18px;
  font-size: 25px;
  background: linear-gradient(90deg, #7cecff, #d58bff);
  -webkit-background-clip: text;
  color: transparent;
}

.panel {
  background: linear-gradient(145deg, #06171d, #030911);
  border: 1px solid #0a4147;
  border-radius: 22px;
  padding: 16px;
  margin: 12px 0;
  box-shadow: 0 0 16px #00e5e510;
}

.btn {
  border: 1px solid var(--cyan);
  border-radius: 30px;
  background: #03191e;
  color: var(--cyan);
  padding: 11px 17px;
  font-weight: 800;
  cursor: pointer;
  box-shadow: 0 0 12px #00e5e51a;
}

.btn.primary {
  background: var(--cyan);
  color: #001419;
  box-shadow: 0 0 18px #00e5e555;
}

.btn:disabled {
  opacity: 0.4;
}

.pills {
  display: flex;
  gap: 8px;
}

.pills button {
  flex: 1;
}

.pills button.selected {
  background: var(--cyan);
  color: #001;
}

/* محیط بازی */

#gamebox {
  position: relative;
  overflow: hidden;
  border: 1px solid #10414b;
  border-radius: 18px;
  background: #01040b;
  height: min(68vh, 590px);
  min-height: 390px;
}

#gamecanvas {
  width: 100%;
  height: 100%;
  display: block;
  touch-action: none;
}

.hud {
  position: absolute;
  top: 10px;
  left: 10px;
  right: 10px;
  display: flex;
  justify-content: space-between;
  font-weight: 900;
  text-shadow: 0 0 8px #00eaff;
  font-size: 14px;
  pointer-events: none;
}

.gamehint {
  text-align: center;
  color: var(--muted);
  font-size: 13px;
  margin: 9px;
}

/* فروشگاه */

.item {
  display: grid;
  grid-template-columns: 82px 1fr;
  gap: 12px;
  align-items: center;
  border: 1px solid #12482a;
  background: #03120d;
  border-radius: 20px;
  padding: 13px;
  margin: 12px 0;
}

.item .art {
  width: 78px;
  height: 78px;
  display: grid;
  place-items: center;
  font-size: 45px;
  background: #07141c;
  border-radius: 15px;
}

.item h3 {
  margin: 0 0 4px;
  font-size: 18px;
}

.item p {
  margin: 3px 0;
  color: var(--muted);
  font-size: 13px;
}

.item .actions {
  display: flex;
  gap: 7px;
  flex-wrap: wrap;
  margin-top: 9px;
}

.price {
  color: #ffe34c;
  font-weight: 900;
}

/* رتبه‌بندی */

.rankrow {
  display: grid;
  grid-template-columns: 42px 1fr auto;
  gap: 10px;
  align-items: center;
  padding: 13px;
  border: 1px solid #10414b;
  border-radius: 17px;
  margin: 9px 0;
  background: #04121a;
}

.ranknum {
  border-radius: 50%;
  background: #07313a;
  color: #8ffaff;
  text-align: center;
  padding: 8px;
  font-weight: 900;
}

.ranknum.top {
  background: #ffd329;
  color: #211900;
}

.avatar {
  font-size: 32px;
  display: grid;
  place-items: center;
  width: 45px;
  height: 45px;
  border-radius: 13px;
  background: #0a101b;
}

/* پروفایل */

.profilegrid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

.profilecard {
  text-align: center;
}

.bigavatar {
  font-size: 55px;
  filter: drop-shadow(0 0 12px #00eaff);
  margin: 6px;
}

input {
  width: 100%;
  background: #020a12;
  border: 1px solid #12505a;
  border-radius: 14px;
  color: white;
  padding: 13px;
  font-size: 16px;
  margin: 7px 0;
}

.avatars {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.avatars button {
  font-size: 25px;
  background: #0b111c;
  border: 1px solid transparent;
  border-radius: 13px;
  padding: 8px;
}

.avatars button.chosen {
  border-color: var(--cyan);
  box-shadow: 0 0 12px #00ffff66;
}

.small {
  font-size: 12px;
  color: var(--muted);
}

.toggle {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.switch {
  accent-color: var(--cyan);
  width: 22px;
  height: 22px;
}

.notice {
  color: #9db5c2;
  font-size: 12px;
  line-height: 1.8;
}

@media (max-width: 420px) {
  .brand {
    font-size: 19px;
  }

  nav button {
    font-size: 12px;
  }

  .item {
    grid-template-columns: 68px 1fr;
  }

  .item .art {
    width: 66px;
    height: 66px;
  }
}
</style>
</head>

<body>

<header>
  <div class="brand">تیرباز فضایی 🚀</div>

  <nav>
    <button class="active" data-tab="game">🎮 بازی</button>
    <button data-tab="store">🛍 فروشگاه</button>
    <button data-tab="ranks">🏆 رتبه‌ها</button>
    <button data-tab="profile">👤 پروفایل</button>
  </nav>
</header>

<main>

<!-- بازی -->
<section id="game" class="view active">

  <h2>ماموریت فضایی</h2>

  <div id="gamebox">
    <canvas id="gamecanvas"></canvas>

    <div class="hud">
      <span>⭐ <b id="score">0</b></span>
      <span>🪙 <b id="coins">250</b></span>
      <span id="lives">❤️❤️❤️</span>
    </div>
  </div>

  <div class="gamehint">
    برای جابه‌جایی سفینه روی صفحه بکش؛ شلیک خودکار است.
    با نابودی دشمن‌ها سکه و امتیاز بگیر!
  </div>

  <div style="display:flex;gap:8px;justify-content:center">
    <button class="btn" id="pause">⏸ توقف</button>
    <button class="btn primary" id="restart">شروع دوباره</button>
  </div>

</section>

<!-- فروشگاه -->
<section id="store" class="view">

  <h2>🛒 فروشگاه</h2>

  <div class="panel" style="text-align:center;color:var(--gold);font-size:20px;font-weight:900">
    موجودی شما: <span id="storecoins">250</span> 🪙
  </div>

  <div class="pills">
    <button class="btn selected" data-kind="weapons">🔫 سلاح‌ها</button>
    <button class="btn" data-kind="ships">🚀 سفینه‌ها</button>
  </div>

  <div id="storeitems"></div>

</section>

<!-- رتبه‌بندی -->
<section id="ranks" class="view">

  <h2>🏆 رتبه‌بندی آنلاین</h2>

  <div style="text-align:center">
    <button class="btn" id="refreshRanks">🔄 بروزرسانی لیست</button>
  </div>

  <div id="ranklist"></div>

  <p class="notice">
    این نسخه جدول امتیاز را روی همین دستگاه ذخیره می‌کند.
    برای جدول آنلاین مشترک بین همه بازیکنان باید به سرور یا دیتابیس متصل شود.
  </p>

</section>

<!-- پروفایل -->
<section id="profile" class="view">

  <h2>👤 پروفایل شما</h2>

  <div class="panel profilegrid">

    <div class="profilecard">

      <div class="bigavatar" id="profileavatar">👻</div>

      <b id="profilename">خلبان ۱۹۷۲</b>

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
    <input class="switch" type="checkbox" id="sound" checked>
  </div>

  <div class="panel">

    <div style="color:var(--gold);font-weight:900">
      کد اختصاصی بازیکن شما
    </div>

    <h2 id="playercode" style="color:#fff;margin:8px">
      SPC-000000
    </h2>

    <p class="notice">
      این کد در این نسخه برای شناسایی پروفایل ساخته می‌شود.
    </p>

  </div>

  <div style="text-align:center">
    <button class="btn primary" id="saveprofile">
      ذخیره پروفایل
    </button>
  </div>

</section>

</main>

<script>
(() => {

const $ = s => document.querySelector(s);
const $$ = s => [...document.querySelectorAll(s)];

const emojis = [
  '🔷','👾','🟨','🔵','🟣',
  '🔺','🌟','👻','🚀','🛸'
];

let data = JSON.parse(localStorage.getItem('space_save') || 'null') || {
  score: 0,
  coins: 250,
  lives: 3,
  name: '',
  avatar: '👻',
  sound: true,
  ship: 0,
  weapon: 0,
  ownedShips: [0],
  ownedWeapons: [0],
  code: 'SPC-' + Math.random().toString(36).slice(2,8).toUpperCase()
};

function save() {
  localStorage.setItem('space_save', JSON.stringify(data));
  updateHUD();
}

function displayName() {
  return data.name.trim() ||
    ('خلبان ' + (1972 + Math.floor(Math.random() * 8000)));
}

function updateHUD() {
  $('#score').textContent = data.score;
  $('#coins').textContent = data.coins;
  $('#storecoins').textContent = data.coins;
  $('#profilescore').textContent = data.score;
  $('#lives').textContent = '❤️'.repeat(Math.max(0, data.lives));
  $('#profilename').textContent = displayName();
  $('#profileavatar').textContent = data.avatar;
  $('#playercode').textContent = data.code;
}

/* جابه‌جایی بین صفحات */

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
    if (button.dataset.tab === 'game') resize();
  };

});

/* اطلاعات فروشگاه */

let kind = 'weapons';

const weapons = [
  ['لیزر ساده','🔴',200,'تک‌تیر، آسیب پایه'],
  ['شلیک دوقلو','🟣',450,'دو پرتابه هم‌زمان'],
  ['لیزر سریع','⚡',700,'سرعت شلیک بیشتر'],
  ['پرتاب پلاسما','💠',1100,'پرتابه قدرتمند']
];

const ships = [
  ['سفینه پیش‌فرض','🚀',0,'متعادل و چابک'],
  ['شبح نئونی','👻',500,'حرکت سریع'],
  ['شکارچی ستاره','🛸',900,'سفینه مقاوم'],
  ['بال طلایی','🌟',1500,'سفینه ویژه']
];

$$('[data-kind]').forEach(button => {

  button.onclick = () => {

    kind = button.dataset.kind;

    $$('[data-kind]').forEach(b =>
      b.classList.toggle('selected', b === button)
    );

    renderStore();
  };

});

function renderStore() {

  const list = kind === 'weapons' ? weapons : ships;

  const owned = kind === 'weapons'
    ? data.ownedWeapons
    : data.ownedShips;

  const current = kind === 'weapons'
    ? data.weapon
    : data.ship;

  $('#storeitems').innerHTML = list.map((item, i) => `

    <div class="item">

      <div class="art">${item[1]}</div>

      <div>

        <h3>${item[0]}</h3>

        <p>${item[3]}</p>

        <p>
          ${owned.includes(i)
            ? (i === current ? 'مجهز' : 'خریداری‌شده')
            : 'قابل خرید'}
        </p>

        <div class="actions">

          <span class="price">${item[2] || 'رایگان'} 🪙</span>

          <button class="btn ${i === current ? 'primary' : ''}"
                  data-buy="${i}">

            ${i === current
              ? 'مجهز'
              : owned.includes(i)
                ? 'تجهیز کردن'
                : 'خرید'}

          </button>

        </div>

      </div>

    </div>

  `).join('');

  $$('[data-buy]').forEach(button => {

    button.onclick = () => {

      const i = Number(button.dataset.buy);

      const owned = kind === 'weapons'
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

      if (kind === 'weapons') {
        data.weapon = i;
      } else {
        data.ship = i;
      }

      save();
      renderStore();
      sound(660);

    };

  });

}

/* پروفایل */

function renderProfile() {

  $('#nameinput').value = data.name;
  $('#sound').checked = data.sound;

  $('#avatars').innerHTML = emojis.map(emoji => `

    <button class="${emoji === data.avatar ? 'chosen' : ''}"
            data-avatar="${emoji}">

      ${emoji}

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
  sound(500);

};

/* رتبه‌بندی محلی */

function renderRanks() {

  let rows = JSON.parse(localStorage.getItem('space_ranks') || '[]');

  rows = rows.filter(row => row.code !== data.code);

  rows.push({
    name: displayName(),
    score: data.score,
    avatar: data.avatar,
    code: data.code
  });

  rows.sort((a, b) => b.score - a.score);

  rows = rows.slice(0, 50);

  localStorage.setItem('space_ranks', JSON.stringify(rows));

  $('#ranklist').innerHTML = rows.map((row, i) => `

    <div class="rankrow">

      <div class="ranknum ${i < 3 ? 'top' : ''}">
        ${i + 1}
      </div>

      <div style="display:flex;align-items:center;gap:10px">

        <span class="avatar">${row.avatar}</span>

        <span>

          <b>${escapeHTML(row.name)}</b>

          <div class="small">
            ${row.code === data.code ? 'شما' : 'خلبان فضایی'}
          </div>

        </span>

      </div>

      <b>${row.score} ⭐</b>

    </div>

  `).join('');

}

function escapeHTML(text) {

  return text.replace(/[&<>"']/g, char => ({
    '&': '&amp;',
    '<': '&lt;',
    '>': '&gt;',
    '"': '&quot;',
    "'": '&#39;'
  }[char]));

}

$('#refreshRanks').onclick = renderRanks;

/* افکت صوتی */

function sound(frequency) {

  if (!data.sound) return;

  try {

    const audio = new (window.AudioContext || window.webkitAudioContext)();

    const oscillator = audio.createOscillator();
    const gain = audio.createGain();

    oscillator.frequency.value = frequency;
    gain.gain.value = 0.025;

    oscillator.connect(gain);
    gain.connect(audio.destination);

    oscillator.start();
    oscillator.stop(audio.currentTime + 0.07);

    oscillator.onended = () => audio.close();

  } catch (error) {}

}

/* بازی */

const canvas = $('#gamecanvas');
const ctx = canvas.getContext('2d');

let W = 0;
let H = 0;

let shipX = 0;
let shipY = 0;

let bullets = [];
let enemies = [];
let stars = [];

let running = true;
let last = 0;
let spawn = 0;

function resize() {

  const rect = canvas.getBoundingClientRect();

  W = canvas.width = Math.max(1, rect.width * devicePixelRatio);
  H = canvas.height = Math.max(1, rect.height * devicePixelRatio);

  shipX = W / 2;
  shipY = H * 0.86;

  stars = Array.from({length: 90}, () => ({
    x: Math.random() * W,
    y: Math.random() * H,
    s: Math.random() * 1.7 + 0.3,
    v: Math.random() * 1.2 + 0.3
  }));

}

function addEnemy() {

  const x = Math.random() * (W - 35) + 17;

  enemies.push({
    x,
    y: -20,
    r: 10 + Math.random() * 5,
    v: 1.1 + Math.random() * 1.2,
    hp: 1
  });

}

function drawShip(x, y) {

  ctx.save();

  ctx.translate(x, y);

  ctx.shadowBlur = 18;
  ctx.shadowColor = '#00ffe7';

  ctx.fillStyle = '#00d9d2';

  ctx.beginPath();

  ctx.moveTo(0, -20);
  ctx.lineTo(15, 15);
  ctx.lineTo(0, 8);
  ctx.lineTo(-15, 15);

  ctx.closePath();
  ctx.fill();

  ctx.fillStyle = '#f58bff';
  ctx.fillRect(-3, 10, 6, 9);

  ctx.restore();

}

function frame(time) {

  requestAnimationFrame(frame);

  if (!W) return;

  const dt = Math.min(2, (time - last) / 16 || 1);

  last = time;

  ctx.clearRect(0, 0, W, H);

  ctx.fillStyle = '#01040b';
  ctx.fillRect(0, 0, W, H);

  /* ستاره‌های پس‌زمینه */

  stars.forEach(star => {

    star.y += star.v * dt;

    if (star.y > H) star.y = 0;

    ctx.fillStyle = `rgba(130,230,255,${0.25 + star.s / 3})`;

    ctx.fillRect(star.x, star.y, star.s, star.s);

  });

  if (running && data.lives > 0) {

    spawn += dt;

    if (spawn > 38) {
      spawn = 0;
      addEnemy();
    }

    /* شلیک خودکار */

    if (Math.random() < 0.13) {

      bullets.push({
        x: shipX,
        y: shipY - 20,
        v: 7
      });

    }

    bullets.forEach(bullet => {
      bullet.y -= bullet.v * dt;
    });

    bullets = bullets.filter(bullet => bullet.y > -10);

    /* حرکت دشمن‌ها */

    enemies.forEach(enemy => {
      enemy.y += enemy.v * dt;
    });

    /* برخورد گلوله و دشمن */

    for (const enemy of enemies) {

      for (const bullet of bullets) {

        if (
          Math.hypot(enemy.x - bullet.x, enemy.y - bullet.y)
          < enemy.r + 4
        ) {

          enemy.hp = 0;
          bullet.y = -99;

          data.score += 100;
          data.coins += 5;

          sound(820);

          break;

        }

      }

      /* برخورد دشمن با سفینه */

      if (
        enemy.y > shipY - 15 &&
        Math.abs(enemy.x - shipX) < 24
      ) {

        enemy.hp = 0;
        data.lives--;

        sound(180);

      }

    }

    enemies = enemies.filter(enemy =>
      enemy.hp > 0 && enemy.y < H + 30
    );

    if (data.lives <= 0) {

      running = false;

      ctx.fillStyle = '#ff5b7d';
      ctx.font = 'bold 28px sans-serif';
      ctx.textAlign = 'center';

      ctx.fillText('پایان ماموریت', W / 2, H / 2);

    }

    if (time % 1000 < 20) save();

  }

  /* رسم گلوله‌ها */

  bullets.forEach(bullet => {

    ctx.shadowBlur = 10;
    ctx.shadowColor = '#00ffff';

    ctx.fillStyle = '#8fffff';

    ctx.fillRect(
      bullet.x - 2,
      bullet.y - 8,
      4,
      12
    );

  });

  /* رسم دشمن‌ها */

  enemies.forEach(enemy => {

    ctx.shadowBlur = 12;
    ctx.shadowColor = '#ff9b18';

    ctx.fillStyle = '#ff9b18';

    ctx.beginPath();

    ctx.arc(
      enemy.x,
      enemy.y,
      enemy.r,
      0,
      Math.PI * 2
    );

    ctx.fill();

    ctx.fillStyle = '#77410d';

    ctx.fillRect(
      enemy.x - 3,
      enemy.y + enemy.r,
      6,
      12
    );

  });

  drawShip(shipX, shipY);

  updateHUD();

}

/* کنترل سفینه با لمس یا ماوس */

function pointer(event) {

  const rect = canvas.getBoundingClientRect();

  shipX = Math.max(
    18,
    Math.min(
      W - 18,
      (event.clientX - rect.left) * devicePixelRatio
    )
  );

}

canvas.addEventListener('pointerdown', event => {

  canvas.setPointerCapture(event.pointerId);
  pointer(event);

});

canvas.addEventListener('pointermove', event => {

  if (event.buttons) pointer(event);

});

/* توقف و شروع دوباره */

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

  running = true;
  spawn = 0;

  save();

};

/* شروع */

resize();

window.addEventListener('resize', resize);

updateHUD();
renderStore();
renderProfile();

requestAnimationFrame(frame);

})();
</script>

</body>
</html>

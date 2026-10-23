<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<title>جنگجویان کهکشانی</title>

<style>
* {
  box-sizing: border-box;
  -webkit-tap-highlight-color: transparent;
}

body {
  margin: 0;
  background: #070b19;
  color: white;
  font-family: Tahoma, Arial, sans-serif;
  overflow-x: hidden;
}

button, input {
  font-family: inherit;
}

header {
  display: flex;
  width: 100%;
  background: #111a32;
  border-bottom: 2px solid #27385c;
  position: sticky;
  top: 0;
  z-index: 20;
}

header button {
  flex: 1;
  padding: 15px 3px;
  color: white;
  border: none;
  background: transparent;
  font-size: 14px;
  font-weight: bold;
}

header button.active {
  background: #263c70;
  color: #69e8ff;
  border-bottom: 3px solid #69e8ff;
}

#hud {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  padding: 10px;
  background: #0b1225;
  font-size: 13px;
  gap: 8px;
}

#hud span {
  color: #ffe16b;
}

#game {
  width: 100%;
  height: min(68vh, 620px);
  display: block;
  background: #030611;
  touch-action: none;
}

.panel {
  padding: 14px;
  display: none;
}

.panel.active {
  display: block;
}

h2 {
  color: #69e8ff;
  font-size: 19px;
}

.card {
  background: #121c34;
  border: 1px solid #2a3c60;
  border-radius: 12px;
  padding: 13px;
  margin: 10px 0;
}

.card h3 {
  margin: 0 0 9px;
  color: #ffe16b;
}

.card p {
  font-size: 13px;
  line-height: 1.9;
  margin: 5px 0;
}

button.action {
  border: 0;
  border-radius: 8px;
  padding: 10px 14px;
  background: #176eaa;
  color: white;
  margin: 5px 3px;
  font-weight: bold;
}

button.action:disabled {
  background: #444c5d;
  color: #aaa;
}

input {
  background: #080e20;
  color: white;
  border: 1px solid #42557b;
  padding: 11px;
  border-radius: 8px;
  width: 100%;
  margin: 7px 0;
}

.note {
  color: #a6b5d6;
  font-size: 12px;
  line-height: 1.8;
}

#gameOver {
  display: none;
  position: absolute;
  z-index: 5;
  top: 40%;
  left: 0;
  width: 100%;
  text-align: center;
  pointer-events: none;
}

#gameOver h2 {
  color: #ff657d;
  font-size: 27px;
  text-shadow: 0 0 12px #f00;
}

#gameOver p {
  color: white;
}

#stage {
  position: relative;
}

#creator {
  color: #b7c5e4;
  font-size: 11px;
  text-align: center;
  padding: 12px;
}
</style>
</head>

<body>

<header>
  <button class="active" onclick="showPanel('gamePanel',this)">🚀 بازی</button>
  <button onclick="showPanel('shopPanel',this)">🛒 فروشگاه</button>
  <button onclick="showPanel('profilePanel',this)">👤 پروفایل</button>
</header>

<div id="hud">
  <div>امتیاز این دست: <span id="score">0</span></div>
  <div>💰 <span id="coins">0</span></div>
  <div>❤️ <span id="lives">3</span></div>
  <div>رکورد: <span id="record">0</span></div>
</div>

<div id="gamePanel" class="panel active" style="padding:0">
  <div id="stage">
    <canvas id="game"></canvas>

    <div id="gameOver">
      <h2>سفینه نابود شد!</h2>
      <p>برای شروع دوباره لمس کن</p>
    </div>
  </div>

  <div class="note" style="padding:10px">
    انگشتت را روی صفحه بکش تا سفینه حرکت کند.
    با نگه‌داشتن انگشت شلیک می‌کنی.
  </div>
</div>

<div id="shopPanel" class="panel">
  <h2>🛒 فروشگاه کهکشانی</h2>

  <h3>🔫 اسلحه‌ها</h3>
  <div id="weaponList"></div>

  <h3>🛸 سفینه‌ها</h3>
  <div id="shipList"></div>
</div>

<div id="profilePanel" class="panel">
  <h2>👤 پروفایل خلبان</h2>

  <div class="card">
    <p>نام خلبان:</p>
    <input id="nameInput" maxlength="20" placeholder="نام دلخواهت را بنویس">

    <button class="action" onclick="saveName()">ذخیره نام</button>

    <p>کد اختصاصی حساب:</p>
    <p id="accountCode" style="color:#69e8ff;direction:ltr"></p>

    <button class="action" onclick="copyCode()">کپی کد حساب</button>
  </div>

  <div class="card">
    <h3>بازیابی حساب</h3>
    <p class="note">
      کد حساب قبلی را وارد کن. اگر اطلاعات آن حساب در همین مرورگر
      ذخیره شده باشد، می‌توانی آن را بازیابی کنی.
    </p>

    <input id="restoreInput" placeholder="کد حساب قبلی">
    <button class="action" onclick="restoreAccount()">بازیابی</button>
  </div>

  <div class="card">
    <p>رکورد شخصی: <span id="profileRecord">0</span></p>
    <p>مجموع سکه‌ها: <span id="profileCoins">0</span></p>
  </div>
</div>

<div id="creator">جنگجویان کهکشانی | نسخه آزمایشی</div>

<script>
// ==============================
// تنظیمات سازنده
// ==============================

// در نسخه آزمایشی، اگر true باشد، حساب فعلی سکه نامحدود دارد.
// برای بازی عمومی این گزینه را false بگذار.
const CREATOR_MODE = false;

// ==============================
// ذخیره اطلاعات
// ==============================

const SAVE_KEY = "galaxy_game_save_v1";
const ACCOUNTS_KEY = "galaxy_accounts_v1";

function makeCode() {
  return "PILOT-" + Math.random().toString(36).slice(2, 10).toUpperCase();
}

function defaultData() {
  return {
    name: "خلبان " + (Math.floor(Math.random() * 7000) + 1000),
    code: makeCode(),
    coins: 0,
    record: 0,
    weapon: 0,
    weaponLevels: [0, 0, 0, 0, 0],
    ownedWeapons: [true, false, false, false, false],
    ship: 0,
    ownedShips: [true, false, false, false, false]
  };
}

let data;

try {
  data = JSON.parse(localStorage.getItem(SAVE_KEY)) || defaultData();
} catch {
  data = defaultData();
}

function save() {
  if (CREATOR_MODE) {
    data.coins = Infinity;
  }

  localStorage.setItem(SAVE_KEY, JSON.stringify(data));

  // ذخیره محلی حساب برای بازیابی
  let accounts = {};

  try {
    accounts = JSON.parse(localStorage.getItem(ACCOUNTS_KEY)) || {};
  } catch {}

  accounts[data.code] = data;
  localStorage.setItem(ACCOUNTS_KEY, JSON.stringify(accounts));
}

if (CREATOR_MODE) data.coins = Infinity;

save();

// ==============================
// اطلاعات اسلحه‌ها
// ==============================

const weapons = [
  {
    name: "لیزر ساده",
    damage: 1,
    shots: 1,
    price: 0
  },
  {
    name: "شلیک دوقلو",
    damage: 1,
    shots: 2,
    price: 100
  },
  {
    name: "لیزر سرخ",
    damage: 2,
    shots: 1,
    price: 300
  },
  {
    name: "سه‌گانه",
    damage: 1,
    shots: 3,
    price: 500
  },
  {
    name: "پلاسما",
    damage: 4,
    shots: 1,
    price: 1000
  }
];

const ships = [
  { name: "بال قرمز", price: 0, color: "#ff405e" },
  { name: "شاهین کهکشانی", price: 500, color: "#40caff" },
  { name: "شبح بنفش", price: 1000, color: "#b65cff" },
  { name: "سپر طلایی", price: 2000, color: "#ffce43" },
  { name: "ستاره کریستالی", price: 5000, color: "#b8fff9" }
];

// ==============================
// رابط کاربری
// ==============================

function updateHUD() {
  document.getElementById("score").textContent = score;
  document.getElementById("coins").textContent =
    CREATOR_MODE ? "∞" : data.coins;
  document.getElementById("lives").textContent = lives;
  document.getElementById("record").textContent = data.record;

  document.getElementById("profileRecord").textContent = data.record;
  document.getElementById("profileCoins").textContent =
    CREATOR_MODE ? "∞" : data.coins;

  document.getElementById("nameInput").value = data.name;
  document.getElementById("accountCode").textContent = data.code;
}

function showPanel(id, button) {
  document.querySelectorAll(".panel").forEach(p => {
    p.classList.remove("active");
  });

  document.getElementById(id).classList.add("active");

  document.querySelectorAll("header button").forEach(b => {
    b.classList.remove("active");
  });

  button.classList.add("active");

  // بازی فقط در بخش بازی اجرا می‌شود
  playing = id === "gamePanel" && lives > 0;

  if (id === "shopPanel") renderShop();
  if (id === "profilePanel") updateHUD();
}

function saveName() {
  const name = document.getElementById("nameInput").value.trim();

  if (name.length > 0) {
    data.name = name;
    save();
    updateHUD();
    alert("نام خلبان ذخیره شد!");
  }
}

function copyCode() {
  const text = data.code;

  if (navigator.clipboard) {
    navigator.clipboard.writeText(text)
      .then(() => alert("کد حساب کپی شد!"))
      .catch(() => prompt("کد حساب:", text));
  } else {
    prompt("کد حساب:", text);
  }
}

function restoreAccount() {
  const code = document.getElementById("restoreInput")
    .value.trim().toUpperCase();

  let accounts = {};

  try {
    accounts = JSON.parse(localStorage.getItem(ACCOUNTS_KEY)) || {};
  } catch {}

  if (accounts[code]) {
    data = accounts[code];
    save();
    updateHUD();
    renderShop();
    alert("حساب بازیابی شد!");
  } else {
    alert("این کد در حافظه این مرورگر پیدا نشد.");
  }
}

// ==============================
// فروشگاه اسلحه
// ==============================

function renderShop() {
  const weaponList = document.getElementById("weaponList");
  const shipList = document.getElementById("shipList");

  weaponList.innerHTML = "";

  weapons.forEach((w, i) => {
    const owned = data.ownedWeapons[i];
    const equipped = data.weapon === i;
    const level = data.weaponLevels[i];

    const currentDamage = w.damage + level;

    const card = document.createElement("div");
    card.className = "card";

    let html = `
      <h3>${w.name}</h3>
      <p>دمیج: ${currentDamage}</p>
      <p>تعداد تیر: ${w.shots}</p>
    `;

    if (equipped) {
      html += `<p style="color:#69ffab">مجهز شده ✓</p>`;
    } else if (owned) {
      html += `<button class="action" onclick="equipWeapon(${i})">مجهز کردن</button>`;
    } else {
      html += `<p>هزینه خرید: ${w.price} سکه</p>`;
      html += `<button class="action" onclick="buyWeapon(${i})">خرید اسلحه</button>`;
    }

    if (owned) {
      html += `
        <p>هزینه ارتقا: 200 سکه</p>
        <button class="action" onclick="upgradeWeapon(${i})">ارتقا</button>
      `;
    }

    card.innerHTML = html;
    weaponList.appendChild(card);
  });

  // ==============================
  // فروشگاه سفینه
  // ==============================

  shipList.innerHTML = "";

  ships.forEach((s, i) => {
    const owned = data.ownedShips[i];
    const equipped = data.ship === i;

    const card = document.createElement("div");
    card.className = "card";

    card.innerHTML = `
      <h3>${s.name}</h3>
      <p>هزینه خرید: ${s.price} سکه</p>
      <p style="color:${s.color}">رنگ سفینه: ${s.color}</p>
      ${
        equipped
          ? '<p style="color:#69ffab">مجهز شده ✓</p>'
          : owned
            ? `<button class="action" onclick="equipShip(${i})">مجهز کردن</button>`
            : `<button class="action" onclick="buyShip(${i})">خرید سفینه</button>`
      }
    `;

    shipList.appendChild(card);
  });
}

function canPay(price) {
  return CREATOR_MODE || data.coins >= price;
}

function pay(price) {
  if (!CREATOR_MODE) data.coins -= price;
}

function buyWeapon(i) {
  if (data.ownedWeapons[i]) return;

  if (!canPay(weapons[i].price)) {
    alert("سکه کافی نداری!");
    return;
  }

  pay(weapons[i].price);
  data.ownedWeapons[i] = true;

  save();
  renderShop();
  updateHUD();
}

function equipWeapon(i) {
  if (!data.ownedWeapons[i]) return;

  data.weapon = i;

  save();
  renderShop();
}

function upgradeWeapon(i) {
  if (!data.ownedWeapons[i]) return;

  if (!canPay(200)) {
    alert("برای ارتقا ۲۰۰ سکه نیاز داری!");
    return;
  }

  pay(200);
  data.weaponLevels[i]++;

  save();
  renderShop();
  updateHUD();
}

function buyShip(i) {
  if (data.ownedShips[i]) return;

  if (!canPay(ships[i].price)) {
    alert("سکه کافی نداری!");
    return;
  }

  pay(ships[i].price);
  data.ownedShips[i] = true;

  save();
  renderShop();
  updateHUD();
}

function equipShip(i) {
  if (!data.ownedShips[i]) return;

  data.ship = i;

  save();
  renderShop();
}

// ==============================
// موتور بازی
// ==============================

const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let W, H;

function resize() {
  const rect = canvas.getBoundingClientRect();

  W = canvas.width = rect.width * devicePixelRatio;
  H = canvas.height = rect.height * devicePixelRatio;

  ctx.setTransform(devicePixelRatio, 0, 0, devicePixelRatio, 0, 0);
}

window.addEventListener("resize", resize);
resize();

let score = 0;
let lives = 3;
let playing = true;

let player = {
  x: 0,
  y: 0,
  size: 24
};

let bullets = [];
let enemies = [];
let lastShot = 0;
let lastEnemy = 0;
let lastFrame = 0;

const stars = Array.from({ length: 75 }, () => ({
  x: Math.random(),
  y: Math.random(),
  speed: 0.2 + Math.random() * 0.8
}));

function drawStars(dt) {
  ctx.fillStyle = "#030611";
  ctx.fillRect(0, 0, W, H);

  ctx.fillStyle = "#fff";

  stars.forEach(s => {
    s.y += s.speed * dt / 1000;

    if (s.y > 1) s.y = 0;

    ctx.globalAlpha = 0.3 + Math.random() * 0.6;

    ctx.fillRect(
      s.x * (W / devicePixelRatio),
      s.y * (H / devicePixelRatio),
      1.5,
      1.5
    );
  });

  ctx.globalAlpha = 1;
}

function drawPlayer() {
  const color = ships[data.ship].color;
  const x = player.x;
  const y = player.y;

  ctx.save();
  ctx.translate(x, y);

  // جنگنده مثل B-2، با بال‌های متفاوت
  ctx.fillStyle = color;

  ctx.beginPath();
  ctx.moveTo(-player.size, -player.size * 0.35);
  ctx.lineTo(-player.size * 0.25, -player.size * 0.15);
  ctx.lineTo(0, -player.size);
  ctx.lineTo(player.size * 0.25, -player.size * 0.15);
  ctx.lineTo(player.size, -player.size * 0.35);
  ctx.lineTo(player.size * 0.65, player.size * 0.5);
  ctx.lineTo(0, player.size * 0.2);
  ctx.lineTo(-player.size * 0.65, player.size * 0.5);
  ctx.closePath();
  ctx.fill();

  ctx.fillStyle = "#9cecff";
  ctx.fillRect(-3, -5, 6, 9);

  ctx.restore();
}

function shoot() {
  const weapon = weapons[data.weapon];
  const count = weapon.shots;

  for (let i = 0; i < count; i++) {
    let offset = (i - (count - 1) / 2) * 13;

    bullets.push({
      x: player.x + offset,
      y: player.y - 20,
      damage: weapon.damage + data.weaponLevels[data.weapon],
      speed: 430
    });
  }
}

function spawnEnemy() {
  const width = W / devicePixelRatio;

  enemies.push({
    x: 20 + Math.random() * (width - 40),
    y: -20,
    size: 15,
    speed: 75 + Math.random() * 60,
    hp: 1
  });
}

function drawHexagon(e) {
  ctx.fillStyle = "#ff4e72";
  ctx.strokeStyle = "#ffb2c4";
  ctx.lineWidth = 2;

  ctx.beginPath();

  for (let i = 0; i < 6; i++) {
    const angle = Math.PI / 3 * i;

    const x = e.x + Math.cos(angle) * e.size;
    const y = e.y + Math.sin(angle) * e.size;

    if (i === 0) ctx.moveTo(x, y);
    else ctx.lineTo(x, y);
  }

  ctx.closePath();
  ctx.fill();
  ctx.stroke();
}

function loseLife() {
  lives--;

  updateHUD();

  if (lives <= 0) {
    playing = false;

    if (score > data.record) {
      data.record = score;
    }

    save();

    document.getElementById("gameOver").style.display = "block";
  }
}

function resetGame() {
  score = 0;
  lives = 3;

  bullets = [];
  enemies = [];

  player.x = W / devicePixelRatio / 2;
  player.y = H / devicePixelRatio - 65;

  document.getElementById("gameOver").style.display = "none";

  updateHUD();
}

function gameLoop(time) {
  const dt = Math.min(time - lastFrame, 40);
  lastFrame = time;

  drawStars(dt);

  if (playing && document.getElementById("gamePanel").classList.contains("active")) {
    const width = W / devicePixelRatio;
    const height = H / devicePixelRatio;

    // شلیک خودکار هنگام نگه‌داشتن انگشت
    if (touching && time - lastShot > 250) {
      shoot();
      lastShot = time;
    }

    if (time - lastEnemy > 850) {
      spawnEnemy();
      lastEnemy = time;
    }

    // گلوله‌ها
    bullets.forEach(b => {
      b.y -= b.speed * dt / 1000;

      ctx.fillStyle = "#69e8ff";
      ctx.fillRect(b.x - 2, b.y, 4, 12);
    });

    bullets = bullets.filter(b => b.y > -20);

    // دشمن‌ها
    enemies.forEach(e => {
      e.y += e.speed * dt / 1000;

      drawHexagon(e);

      // برخورد دشمن با سفینه
      const dx = e.x - player.x;
      const dy = e.y - player.y;

      if (Math.hypot(dx, dy) < e.size + player.size * 0.6) {
        e.dead = true;
        loseLife();
      }

      // عبور دشمن از پایین صفحه
      if (e.y > height + 20) {
        e.dead = true;
        loseLife();
      }

      // برخورد گلوله به دشمن
      bullets.forEach(b => {
        if (!e.dead && Math.hypot(e.x - b.x, e.y - b.y) < e.size + 6) {
          e.hp -= b.damage;
          b.dead = true;

          if (e.hp <= 0) {
            e.dead = true;

            score += 10;

            if (!CREATOR_MODE) data.coins += 2;

            if (score > data.record) {
              data.record = score;
            }

            save();
            updateHUD();
          }
        }
      });
    });

    enemies = enemies.filter(e => !e.dead);
    bullets = bullets.filter(b => !b.dead);

    drawPlayer();
  } else {
    drawPlayer();
  }

  requestAnimationFrame(gameLoop);
}

// ==============================
// کنترل لمسی
// ==============================

let touching = false;

function getPointerPosition(event) {
  const rect = canvas.getBoundingClientRect();

  return {
    x: event.clientX - rect.left,
    y: event.clientY - rect.top
  };
}

canvas.addEventListener("pointerdown", event => {
  event.preventDefault();

  if (lives <= 0) {
    resetGame();
    playing = true;
  }

  touching = true;

  const p = getPointerPosition(event);

  player.x = p.x;
  player.y = p.y;

  canvas.setPointerCapture(event.pointerId);
});

canvas.addEventListener("pointermove", event => {
  if (!touching) return;

  event.preventDefault();

  const p = getPointerPosition(event);

  const width = W / devicePixelRatio;
  const height = H / devicePixelRatio;

  player.x = Math.max(20, Math.min(width - 20, p.x));
  player.y = Math.max(35, Math.min(height - 30, p.y));
});

canvas.addEventListener("pointerup", () => {
  touching = false;
});

canvas.addEventListener("pointercancel", () => {
  touching = false;
});

// ==============================
// شروع
// ==============================

resetGame();
updateHUD();
renderShop();

requestAnimationFrame(gameLoop);

</script>

</body>
</html>

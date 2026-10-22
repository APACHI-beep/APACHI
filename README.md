<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<title>جنگ جهانی</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: Tahoma, sans-serif;
    background: linear-gradient(135deg, #1a1a2e, #16213e);
    color: #fff;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
  }
  .container {
    background: rgba(0,0,0,0.6);
    border: 2px solid #e94560;
    border-radius: 20px;
    padding: 30px;
    max-width: 900px;
    width: 100%;
    box-shadow: 0 0 40px rgba(233,69,96,0.5);
  }
  h1 { color: #e94560; text-align: center; margin-bottom: 20px; }
  h2 { color: #f9c74f; text-align: center; margin-bottom: 15px; }
  input {
    width: 100%;
    padding: 12px;
    border-radius: 10px;
    border: none;
    font-size: 16px;
    margin-bottom: 15px;
    background: #0f3460;
    color: #fff;
  }
  button {
    background: #e94560;
    color: #fff;
    border: none;
    padding: 14px 24px;
    border-radius: 10px;
    font-size: 16px;
    cursor: pointer;
    width: 100%;
    font-weight: bold;
    transition: 0.3s;
  }
  button:hover { background: #c92c48; transform: scale(1.02); }
  .slots {
    display: flex;
    gap: 20px;
    justify-content: center;
    margin: 20px 0;
  }
  .slot {
    width: 100px;
    height: 100px;
    border-radius: 50%;
    border: 3px dashed #e94560;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 12px;
    text-align: center;
    flex-direction: column;
    padding: 5px;
  }
  .slot.filled {
    border: 3px solid #4ade80;
    background: rgba(74,222,128,0.15);
  }
  .slot .player-name { font-weight: bold; font-size: 13px; }
  .slot .player-country { font-size: 20px; }
  .hidden { display: none; }

  .game-area { display: flex; gap: 20px; flex-wrap: wrap; }
  .map {
    flex: 2;
    min-width: 320px;
    background: #0a1929;
    border-radius: 15px;
    padding: 10px;
    min-height: 400px;
  }
  .map-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(90px, 1fr));
    gap: 8px;
  }
  .country {
    background: #1e3a5f;
    border-radius: 8px;
    padding: 8px;
    text-align: center;
    font-size: 11px;
    cursor: pointer;
    border: 2px solid transparent;
    transition: 0.2s;
    position: relative;
  }
  .country:hover { border-color: #f9c74f; transform: scale(1.05); }
  .country .flag { font-size: 24px; }
  .country .name { margin: 4px 0; font-weight: bold; }
  .country .soldiers { color: #4ade80; font-size: 10px; }
  .country.owned-p1 { border-color: #00d4ff; background: rgba(0,212,255,0.2); }
  .country.owned-p2 { border-color: #ff6b6b; background: rgba(255,107,107,0.2); }
  .country.owned-p3 { border-color: #f9c74f; background: rgba(249,199,79,0.2); }
  .country.owned-bot { border-color: #a855f7; background: rgba(168,85,247,0.2); }

  .sidebar {
    flex: 1;
    min-width: 250px;
    background: rgba(0,0,0,0.4);
    border-radius: 15px;
    padding: 15px;
  }
  .player-info {
    background: #0f3460;
    padding: 10px;
    border-radius: 10px;
    margin-bottom: 10px;
    font-size: 13px;
  }
  .player-info strong { color: #f9c74f; }
  .log {
    background: #0a1929;
    border-radius: 10px;
    padding: 10px;
    height: 200px;
    overflow-y: auto;
    font-size: 12px;
    margin-top: 10px;
  }
  .log div { margin-bottom: 5px; padding: 3px; border-right: 3px solid #e94560; padding-right: 8px; }

  #leaderboard { margin-top: 20px; }
  #leaderboard h2 { color: #e94560; }
  .lb-row {
    display: flex;
    justify-content: space-between;
    padding: 8px;
    background: #0f3460;
    margin-bottom: 5px;
    border-radius: 8px;
  }
  .lb-row.top { background: linear-gradient(90deg, #e94560, #0f3460); }
</style>
</head>
<body>
<div class="container">

  <!-- مرحله ۱: صفحه ورود -->
  <div id="login-screen">
    <h1>🌍 جنگ جهانی</h1>
    <h2>اسم بازیکن رو وارد کن</h2>
    <input id="player-name" type="text" placeholder="مثلا: ناپلئون" maxlength="15" />
    <button onclick="startGame()">⚔️ شروع بازی</button>
  </div>

  <!-- مرحله ۲: لابی و انتخاب کشور -->
  <div id="lobby-screen" class="hidden">
    <h1>در انتظار بازیکن‌ها...</h1>
    <div class="slots">
      <div class="slot" id="slot-1">بازیکن ۱</div>
      <div class="slot" id="slot-2">بازیکن ۲</div>
      <div class="slot" id="slot-3">بازیکن ۳</div>
    </div>
    <p style="text-align:center;color:#aaa;margin-bottom:15px;">
      اگر دایره‌ای پر نشود، ربات جایگزین می‌شود.
    </p>
    <button onclick="enterGame()">▶️ ورود به میدان نبرد</button>
  </div>

  <!-- مرحله ۳: بازی -->
  <div id="game-screen" class="hidden">
    <h1>🗺️ میدان نبرد جهانی</h1>
    <div class="game-area">
      <div class="map">
        <div class="map-grid" id="map-grid"></div>
      </div>
      <div class="sidebar">
        <div id="players-status"></div>
        <div class="log" id="log"></div>
        <button style="margin-top:10px;background:#4ade80;color:#000;" onclick="resetGame()">🔄 بازی جدید</button>
      </div>
    </div>
  </div>

  <!-- لیدربرد -->
  <div id="leaderboard">
    <h2>🏆 لیدربرد</h2>
    <div id="lb-list"></div>
  </div>

</div>

<script>
// ============ داده‌های کشورها ============
const COUNTRIES = [
  { flag: "🇮🇷", name: "ایران" },
  { flag: "🇺🇸", name: "آمریکا" },
  { flag: "🇷🇺", name: "روسیه" },
  { flag: "🇨🇳", name: "چین" },
  { flag: "🇩🇪", name: "آلمان" },
  { flag: "🇫🇷", name: "فرانسه" },
  { flag: "🇬🇧", name: "انگلیس" },
  { flag: "🇯🇵", name: "ژاپن" },
  { flag: "🇮🇳", name: "هند" },
  { flag: "🇧🇷", name: "برزیل" },
  { flag: "🇹🇷", name: "ترکیه" },
  { flag: "🇪🇬", name: "مصر" },
  { flag: "🇸🇦", name: "عربستان" },
  { flag: "🇮🇹", name: "ایتالیا" },
  { flag: "🇪🇸", name: "اسپانیا" },
  { flag: "🇰🇷", name: "کره جنوبی" }
];

// ============ وضعیت بازی ============
let gameState = {
  players: [],   // { name, isBot, color, soldiers, countries: [] }
  countries: [], // { ...country, owner: index, soldiers }
  turn: 0,
  playerName: ""
};

// ============ مرحله ۱ ============
function startGame() {
  const name = document.getElementById("player-name").value.trim();
  if (!name) { alert("لطفا اسم وارد کن!"); return; }
  gameState.playerName = name;
  document.getElementById("login-screen").classList.add("hidden");
  document.getElementById("lobby-screen").classList.remove("hidden");
  setupLobby();
}

// ============ مرحله ۲: لابی ============
function setupLobby() {
  // بازیکن اصلی
  gameState.players = [
    { name: gameState.playerName, isBot: false, color: "owned-p1", soldiers: 100000 },
  ];
  // ۲ ربات (شبیه‌سازی بازیکن آنلاین)
  gameState.players.push({ name: "ربات آلفا", isBot: true, color: "owned-p2", soldiers: 100000 });
  gameState.players.push({ name: "ربات براوو", isBot: true, color: "owned-p3", soldiers: 100000 });

  // نمایش اسلات‌ها
  document.getElementById("slot-1").classList.add("filled");
  document.getElementById("slot-1").innerHTML = `<div class="player-name">${gameState.playerName}</div><div class="player-country">👤</div>`;
  document.getElementById("slot-2").classList.add("filled");
  document.getElementById("slot-2").innerHTML = `<div class="player-name">ربات آلفا</div><div class="player-country">🤖</div>`;
  document.getElementById("slot-3").classList.add("filled");
  document.getElementById("slot-3").innerHTML = `<div class="player-name">ربات براوو</div><div class="player-country">🤖</div>`;
}

// ============ مرحله ۳: شروع بازی ============
function enterGame() {
  document.getElementById("lobby-screen").classList.add("hidden");
  document.getElementById("game-screen").classList.remove("hidden");

  // ساخت کشورها
  gameState.countries = COUNTRIES.map(c => ({ ...c, owner: null, soldiers: 0 }));

  // انتخاب کشور تصادفی برای هر بازیکن
  const shuffled = [...gameState.countries].sort(() => Math.random() - 0.5);
  gameState.players.forEach((p, i) => {
    const c = shuffled[i];
    c.owner = i;
    c.soldiers = 100000;
    p.soldiers = 100000;
    p.countries = [c];
  });

  // کشورهای باقی‌مانده (بی‌صاحب) با سرباز تصادفی
  gameState.countries.forEach(c => {
    if (c.owner === null) c.soldiers = Math.floor(Math.random() * 30000) + 10000;
  });

  gameState.turn = 0;
  logMessage("⚔️ جنگ آغاز شد!");
  logMessage(`🌍 ${gameState.playerName} وارد میدان شد!`);
  render();
}

// ============ رندر ============
function render() {
  // نقشه
  const grid = document.getElementById("map-grid");
  grid.innerHTML = "";
  gameState.countries.forEach((c, idx) => {
    const div = document.createElement("div");
    div.className = "country";
    if (c.owner !== null) div.classList.add(gameState.players[c.owner].color);
    div.innerHTML = `
      <div class="flag">${c.flag}</div>
      <div class="name">${c.name}</div>
      <div class="soldiers">🪖 ${formatNum(c.soldiers)}</div>
    `;
    div.onclick = () => attackCountry(idx);
    grid.appendChild(div);
  });

  // پنل بازیکن‌ها
  const status = document.getElementById("players-status");
  status.innerHTML = gameState.players.map((p, i) => `
    <div class="player-info" style="border-right: 4px solid ${
      p.color === "owned-p1" ? "#00d4ff" : p.color === "owned-p2" ? "#ff6b6b" : "#f9c74f"
    }">
      <strong>${p.name}</strong> ${p.isBot ? "🤖" : "👤"}<br>
      🪖 سرباز: ${formatNum(p.soldiers)}<br>
      🌍 کشورها: ${p.countries.length}
    </div>
  `).join("");
}

// ============ حمله ============
function attackCountry(targetIdx) {
  const target = gameState.countries[targetIdx];
  const playerIdx = 0; // بازیکن اصلی همیشه 0
  const player = gameState.players[playerIdx];

  if (target.owner === playerIdx) {
    logMessage("⚠️ این کشور خودت است!");
    return;
  }

  const myCountries = player.countries;
  const mySoldiers = myCountries.reduce((s, c) => s + c.soldiers, 0);

  if (mySoldiers < 5000) {
    logMessage("❌ سرباز کافی نداری! (حداقل 5000)");
    return;
  }

  // محاسبه تاکتیک: هرچه سرباز بیشتر، شانس بیشتر
  const attackPower = mySoldiers * (0.5 + Math.random());
  const defensePower = target.soldiers * (0.6 + Math.random() * 0.8);

  logMessage(`⚔️ حمله به ${target.name}...`);

  if (attackPower > defensePower) {
    // فتح!
    const enemy = target.owner !== null ? gameState.players[target.owner] : null;
    if (enemy) {
      enemy.countries = enemy.countries.filter(c => c !== target);
    }
    target.owner = playerIdx;
    target.soldiers = Math.max(20000, Math.floor(target.soldiers * 0.5));
    player.countries.push(target);
    player.soldiers += 20000; // پاداش فتح
    logMessage(`✅ ${target.name} فتح شد! +20000 سرباز`);
  } else {
    // شکست
    const lost = Math.floor(mySoldiers * 0.2);
    myCountries[0].soldiers = Math.max(1000, myCountries[0].soldiers - lost);
    logMessage(`❌ حمله به ${target.name} شکست خورد! -${formatNum(lost)} سرباز`);
  }

  render();
  checkWin();
  nextTurn();
}

// ============ نوبت ربات‌ها ============
function nextTurn() {
  gameState.turn++;
  setTimeout(botTurn, 1200);
}

function botTurn() {
  gameState.players.forEach((p, i) => {
    if (!p.isBot) return;
    if (p.countries.length === 0) return;

    // ربات هوشمند: ضعیف‌ترین کشور دشمن رو هدف می‌گیره
    const enemies = gameState.countries.filter(c => c.owner !== i);
    if (enemies.length === 0) return;
    const target = enemies.sort((a, b) => a.soldiers - b.soldiers)[0];
    const myPower = p.countries.reduce((s, c) => s + c.soldiers, 0);

    if (myPower < 5000) return;

    const attackPower = myPower * (0.5 + Math.random());
    const defensePower = target.soldiers * (0.6 + Math.random() * 0.8);

    if (attackPower > defensePower) {
      if (target.owner !== null) {
        gameState.players[target.owner].countries = 
          gameState.players[target.owner].countries.filter(c => c !== target);
      }
      target.owner = i;
      target.soldiers = Math.max(20000, Math.floor(target.soldiers * 0.5));
      p.countries.push(target);
      p.soldiers += 20000;
      logMessage(`🤖 ${p.name} کشور ${target.name} را فتح کرد!`);
    } else {
      logMessage(`🤖 ${p.name} در حمله به ${target.name} شکست خورد.`);
    }
  });

  render();
  checkWin();
}

// ============ شرط برد ============
function checkWin() {
  const total = gameState.countries.length;
  for (let i = 0; i < gameState.players.length; i++) {
    if (gameState.players[i].countries.length === total) {
      logMessage(`🏆 ${gameState.players[i].name} تمام جهان را فتح کرد!`);
      updateLeaderboard(i);
      setTimeout(() => alert(`🏆 ${gameState.players[i].name} برنده شد!`), 500);
    }
  }
  updateLeaderboard();
}

// ============ لیدربرد ============
function updateLeaderboard(winnerIdx = null) {
  const list = document.getElementById("lb-list");
  const sorted = [...gameState.players].sort((a, b) => b.countries.length - a.countries.length);
  list.innerHTML = sorted.map((p, i) => `
    <div class="lb-row ${i === 0 ? "top" : ""}">
      <span>${i + 1}. ${p.name} ${p.isBot ? "🤖" : "👤"}</span>
      <span>${p.countries.length} کشور</span>
    </div>
  `).join("");
}

// ============ ابزارها ============
function logMessage(msg) {
  const log = document.getElementById("log");
  const div = document.createElement("div");
  div.textContent = msg;
  log.prepend(div);
  if (log.children.length > 30) log.removeChild(log.lastChild);
}

function formatNum(n) {
  if (n >= 1000) return (n / 1000).toFixed(0) + "K";
  return n;
}

function resetGame() {
  location.reload();
}
</script>
</body>
</html>

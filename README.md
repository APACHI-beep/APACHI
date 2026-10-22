<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="theme-color" content="#030711">
<title>تیراندازی فضایی 🚀</title>

<style>
@import url('https://fonts.googleapis.com/css2?family=Vazirmatn:wght@400;500;600;700;800&display=swap');

:root {
    --bg: #030711;
    --panel: #07151e;
    --panel2: #091c27;
    --cyan: #00f5f0;
    --pink: #ed4bcb;
    --purple: #a855f7;
    --green: #00ff9d;
    --yellow: #ffe600;
    --text: #eafaff;
    --muted: #8ca9ba;
    --border: rgba(0,245,240,.22);
}

* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Vazirmatn', Tahoma, sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
}

button, input {
    font: inherit;
}

button {
    cursor: pointer;
}

button:active {
    transform: scale(.97);
}

.app {
    max-width: 720px;
    min-height: 100vh;
    margin: auto;
    position: relative;
    padding-bottom: 35px;
    background:
        radial-gradient(ellipse at 50% 0%, rgba(0,245,240,.055), transparent 45%),
        var(--bg);
}

/* Header */

.header {
    height: 76px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 20px;
    background: #101114;
    border-bottom: 1px solid #242830;
}

.brand {
    display: flex;
    align-items: center;
    gap: 12px;
}

.brand-icon {
    font-size: 32px;
}

.brand-title {
    font-weight: 800;
    font-size: 17px;
}

.brand-sub {
    font-size: 12px;
    color: #9aa4b1;
    direction: ltr;
    text-align: right;
}

.header-button {
    background: none;
    color: white;
    border: none;
    font-size: 24px;
}

/* Navigation */

.tabs {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    background: #030711;
    border-bottom: 1px solid var(--border);
}

.tab {
    min-height: 62px;
    color: #718b9e;
    background: transparent;
    border: none;
    border-bottom: 3px solid transparent;
    font-size: 13px;
    font-weight: 700;
    transition: .2s;
}

.tab.active {
    color: var(--cyan);
    border-bottom-color: var(--pink);
    text-shadow: 0 0 14px var(--cyan);
}

.tab span {
    display: block;
    font-size: 21px;
    margin-bottom: 2px;
}

/* Page */

.page {
    display: none;
    padding: 22px 20px 30px;
    animation: appear .25s ease;
}

.page.active {
    display: block;
}

@keyframes appear {
    from { opacity: 0; transform: translateY(7px); }
    to { opacity: 1; transform: translateY(0); }
}

.title {
    text-align: center;
    font-size: 26px;
    font-weight: 800;
    margin: 10px 0 24px;
    background: linear-gradient(90deg, #8ffaff, #e3aaff);
    color: transparent;
    background-clip: text;
}

/* Profile */

.profile-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 14px;
}

.box {
    background: linear-gradient(145deg, #061923, #030a12);
    border: 1px solid var(--border);
    border-radius: 25px;
    padding: 17px;
    box-shadow: inset 0 0 18px rgba(0,245,240,.025);
}

.avatar-card {
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
    min-height: 220px;
}

.avatar-large {
    font-size: 66px;
    filter: drop-shadow(0 0 15px var(--cyan));
}

.commander-name {
    font-size: 24px;
    font-weight: 800;
    direction: ltr;
    margin-top: 4px;
}

.commander-rank {
    font-size: 12px;
    color: var(--cyan);
    letter-spacing: 1px;
    margin-top: 7px;
}

.points {
    border-top: 1px solid var(--border);
    margin-top: 17px;
    padding-top: 12px;
    color: var(--yellow);
    font-weight: 800;
}

.music-box {
    min-height: 220px;
}

.music-label {
    color: var(--cyan);
    font-size: 13px;
    font-weight: 700;
    margin: 15px 0;
}

.music-file {
    width: 100%;
    color: var(--cyan);
    background: transparent;
    border: 2px dashed var(--cyan);
    border-radius: 15px;
    padding: 13px 5px;
    font-size: 13px;
}

.music-controls {
    display: flex;
    gap: 10px;
    margin-top: 15px;
}

.music-controls button {
    flex: 1;
    padding: 14px 0;
    border-radius: 16px;
    border: 1px solid var(--cyan);
    background: var(--cyan);
    color: #001316;
    font-weight: 800;
}

.music-controls .pause {
    color: #ff6b9c;
    background: #140914;
    border-color: #ff6b9c;
}

.label {
    color: var(--muted);
    font-weight: 700;
    font-size: 14px;
    margin: 24px 0 10px;
}

.input {
    width: 100%;
    padding: 15px;
    background: #020810;
    border: 1px solid var(--border);
    color: white;
    border-radius: 17px;
    outline: none;
}

.input:focus {
    border-color: var(--cyan);
    box-shadow: 0 0 15px #00f5f022;
}

.avatar-list {
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    gap: 9px;
}

.avatar-option {
    background: #0a101b;
    border: 1px solid transparent;
    border-radius: 15px;
    padding: 10px 0;
    font-size: 25px;
}

.avatar-option.selected {
    border-color: var(--cyan);
    box-shadow: 0 0 14px #00f5f055;
    background: #09242c;
}

.sound-setting {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 15px;
    margin-top: 22px;
}

.toggle {
    width: 58px;
    height: 30px;
    border: none;
    border-radius: 30px;
    background: #263544;
    padding: 4px;
}

.toggle::after {
    content: "";
    display: block;
    width: 22px;
    height: 22px;
    background: white;
    border-radius: 50%;
    transition: .2s;
}

.toggle.on {
    background: var(--cyan);
    box-shadow: 0 0 15px #00f5f055;
}

.toggle.on::after {
    transform: translateX(-28px);
    background: #001316;
}

.save-code {
    margin-top: 20px;
    border: 1px dashed var(--yellow);
    border-radius: 20px;
    padding: 18px;
    text-align: center;
    background: #17160b;
}

.save-code h3 {
    color: var(--yellow);
    font-size: 14px;
}

.save-code strong {
    display: block;
    direction: ltr;
    font-size: 23px;
    letter-spacing: 2px;
    margin: 10px;
}

.save-code small {
    color: #b6a96d;
    font-size: 11px;
}

.primary-button {
    width: 100%;
    border: none;
    background: linear-gradient(90deg, #00e6ee, #00baff);
    color: #001419;
    border-radius: 35px;
    padding: 15px;
    font-weight: 800;
    margin-top: 20px;
    box-shadow: 0 0 20px #00e6ee22;
}

/* Rankings */

.refresh {
    display: block;
    margin: 0 auto 24px;
    border: 1px solid var(--cyan);
    background: #071820;
    color: var(--cyan);
    border-radius: 30px;
    padding: 12px 28px;
}

.rank-list {
    display: grid;
    gap: 12px;
}

.rank-row {
    min-height: 85px;
    background: #06141d;
    border: 1px solid var(--border);
    border-radius: 21px;
    padding: 15px;
    display: grid;
    grid-template-columns: 1fr 1fr auto;
    align-items: center;
    gap: 10px;
}

.rank-score {
    font-size: 23px;
    font-weight: 800;
    direction: ltr;
    text-align: left;
}

.rank-name {
    font-size: 13px;
    color: #a7bdca;
    text-align: center;
    overflow: hidden;
    text-overflow: ellipsis;
}

.rank-icon {
    font-size: 23px;
}

.rank-number {
    width: 36px;
    height: 36px;
    display: grid;
    place-items: center;
    border-radius: 50%;
    background: #082b33;
    color: #75faff;
    font-weight: 800;
}

/* Store */

.wallet {
    text-align: center;
    color: var(--yellow);
    font-size: 23px;
    font-weight: 800;
    margin-bottom: 24px;
    text-shadow: 0 0 12px #ffe60030;
}

.store-switch {
    display: flex;
    gap: 12px;
    margin-bottom: 25px;
}

.store-switch button {
    flex: 1;
    padding: 17px 8px;
    border-radius: 19px;
    border: 1px solid var(--border);
    color: var(--cyan);
    background: #071820;
    font-weight: 700;
}

.store-switch button.active {
    color: #001418;
    background: var(--cyan);
    box-shadow: 0 0 20px #00f5f044;
}

.weapon-list {
    display: grid;
    gap: 17px;
}

.weapon-card {
    display: grid;
    grid-template-columns: 115px 1fr;
    gap: 15px;
    align-items: center;
    padding: 15px;
    background: linear-gradient(135deg, #06120c, #030a12);
    border: 1px solid #16472b;
    border-radius: 24px;
}

.weapon-image {
    width: 115px;
    height: 115px;
    border-radius: 17px;
    background: radial-gradient(circle, #16372b, #06100e);
    display: grid;
    place-items: center;
    overflow: hidden;
}

.weapon-image svg {
    width: 100%;
    height: 100%;
}

.weapon-info h3 {
    font-size: 18px;
    margin-bottom: 6px;
}

.weapon-info p {
    color: var(--muted);
    font-size: 12px;
    margin-bottom: 12px;
}

.weapon-price {
    display: block;
    color: #ffdbe5;
    background: #770c23;
    border: 1px solid #a91e3c;
    border-radius: 13px;
    padding: 9px 6px;
    text-align: center;
    font-size: 13px;
    font-weight: 800;
}

.weapon-action {
    display: block;
    width: 100%;
    padding: 10px;
    margin-bottom: 10px;
    color: var(--cyan);
    background: transparent;
    border: 1px solid var(--cyan);
    border-radius: 30px;
    font-weight: 700;
}

.weapon-action.equipped {
    background: #00f5f019;
    color: var(--green);
    border-color: var(--green);
}

/* Game */

.game-area {
    position: relative;
    min-height: 580px;
    height: 70vh;
    max-height: 760px;
    overflow: hidden;
    background:
        radial-gradient(ellipse at 50% 25%, #25104c, transparent 40%),
        linear-gradient(#02050e, #050a1b);
    border: 1px solid #17253e;
    border-radius: 22px;
}

.game-hud {
    position: absolute;
    top: 18px;
    left: 15px;
    right: 15px;
    display: flex;
    justify-content: space-between;
    color: #87faff;
    font-weight: 800;
    z-index: 3;
}

.game-star {
    position: absolute;
    width: 3px;
    height: 3px;
    border-radius: 50%;
    background: white;
    animation: twinkle 2s infinite alternate;
}

@keyframes twinkle {
    from { opacity: .15; }
    to { opacity: 1; }
}

.ship {
    position: absolute;
    width: 65px;
    height: 75px;
    bottom: 45px;
    left: calc(50% - 32px);
    font-size: 53px;
    text-align: center;
    filter: drop-shadow(0 0 14px #db35ff);
    transition: left .08s;
}

.game-message {
    position: absolute;
    top: 40%;
    left: 0;
    right: 0;
    text-align: center;
    color: #dcaaff;
    font-weight: 800;
    font-size: 18px;
    padding: 20px;
}

.game-controls {
    display: flex;
    gap: 12px;
    margin-top: 15px;
}

.game-controls button {
    flex: 1;
    background: #091b29;
    color: var(--cyan);
    border: 1px solid var(--cyan);
    border-radius: 18px;
    padding: 14px;
    font-weight: 800;
}

.game-controls .start {
    background: var(--cyan);
    color: #001316;
}

/* Toast */

.toast {
    position: fixed;
    z-index: 20;
    bottom: 25px;
    left: 50%;
    transform: translateX(-50%);
    background: #10232d;
    color: white;
    border: 1px solid var(--cyan);
    box-shadow: 0 0 20px #00f5f044;
    border-radius: 30px;
    padding: 12px 20px;
    font-size: 13px;
    white-space: nowrap;
    opacity: 0;
    pointer-events: none;
    transition: opacity .2s;
}

.toast.show {
    opacity: 1;
}

/* Mobile */

@media (max-width: 430px) {
    .page {
        padding: 17px 14px 25px;
    }

    .title {
        font-size: 23px;
    }

    .profile-grid {
        gap: 10px;
    }

    .box {
        padding: 12px;
    }

    .avatar-large {
        font-size: 52px;
    }

    .commander-name {
        font-size: 20px;
    }

    .music-label {
        font-size: 12px;
    }

    .weapon-card {
        grid-template-columns: 95px 1fr;
        padding: 12px;
        gap: 12px;
    }

    .weapon-image {
        width: 95px;
        height: 105px;
    }

    .weapon-info h3 {
        font-size: 16px;
    }

    .rank-row {
        padding: 12px;
    }

    .rank-score {
        font-size: 20px;
    }
}
</style>
</head>

<body>

<div class="app">

    <header class="header">
        <button class="header-button" onclick="showToast('به پایگاه فضایی خوش آمدی!')">☰</button>

        <div class="brand">
            <div>
                <div class="brand-title">تیراندازی فضایی 🚀</div>
                <div class="brand-sub">SPACE COMMANDER</div>
            </div>
            <div class="brand-icon">🚀</div>
        </div>

        <button class="header-button" onclick="showToast('نسخه آزمایشی بازی')">⋮</button>
    </header>

    <nav class="tabs">
        <button class="tab active" data-page="profile">
            <span>♙</span>پروفایل
        </button>

        <button class="tab" data-page="ranks">
            <span>♜</span>رتبه‌ها
        </button>

        <button class="tab" data-page="store">
            <span>♧</span>فروشگاه
        </button>

        <button class="tab" data-page="game">
            <span>🎮</span>بازی
        </button>
    </nav>

    <!-- PROFILE -->

    <section class="page active" id="profile">

        <h1 class="title">👤 پروفایل شما</h1>

        <div class="profile-grid">

            <div class="box music-box">
                <div class="music-label">🎵 موزیک دلخواه:</div>

                <label class="music-file">
                    📁 انتخاب آهنگ
                    <input type="file" id="musicFile"
                           accept="audio/*" hidden>
                </label>

                <div class="music-controls">
                    <button class="pause" onclick="pauseMusic()">Ⅱ</button>
                    <button onclick="playMusic()">▶</button>
                </div>
            </div>

            <div class="box avatar-card">
                <div class="avatar-large" id="profileAvatar">👾</div>
                <div class="commander-name" id="profileName">APACHI</div>
                <div class="commander-rank">SPACE COMMANDER</div>
                <div class="points">🌟 امتیاز: <span id="points">15950</span></div>
            </div>

        </div>

        <div class="label">اسم شما (نام فرمانده):</div>

        <input class="input" id="nameInput"
               maxlength="20" value="APACHI"
               placeholder="اسم فرمانده">

        <div class="label">انتخاب آواتار (نشان فرماندهی):</div>

        <div class="avatar-list" id="avatarList">
            <button class="avatar-option" data-avatar="💎">💎</button>
            <button class="avatar-option" data-avatar="👹">👹</button>
            <button class="avatar-option" data-avatar="🟨">🟨</button>
            <button class="avatar-option" data-avatar="🔵">🔵</button>
            <button class="avatar-option" data-avatar="👽">👽</button>
            <button class="avatar-option" data-avatar="🔺">🔺</button>
            <button class="avatar-option" data-avatar="🌟">🌟</button>
            <button class="avatar-option" data-avatar="👾">👾</button>
        </div>

        <div class="sound-setting">
            <strong>🔊 افکت‌های صوتی</strong>
            <button class="toggle on" id="soundToggle"
                    onclick="toggleSound()"></button>
        </div>

        <div class="save-code">
            <h3>کد بازیابی اختصاصی شما:</h3>
            <strong id="saveCode">SEPI-AP01</strong>
            <small>این کد را نگه دار تا اطلاعات بازی را بازیابی کنی.</small>
        </div>

        <button class="primary-button" onclick="saveProfile()">
            ذخیره پروفایل (سیو بازی)
        </button>

    </section>

    <!-- RANKS -->

    <section class="page" id="ranks">

        <h1 class="title">🏆 رتبه‌بندی آنلاین</h1>

        <button class="refresh" onclick="renderRanks()">
            🔄 بروزرسانی لیست
        </button>

        <div class="rank-list" id="rankList"></div>

    </section>

    <!-- STORE -->

    <section class="page" id="store">

        <h1 class="title">🛒 فروشگاه</h1>

        <div class="wallet">
            💰 موجودی شما: <span id="coins">604</span> سکه
        </div>

        <div class="store-switch">
            <button class="active" data-store="weapons">🔫 سلاح‌ها</button>
            <button data-store="ships">🚀 اسکین سفینه</button>
        </div>

        <div class="weapon-list" id="weaponList"></div>

    </section>

    <!-- GAME -->

    <section class="page" id="game">

        <h1 class="title">🚀 میدان نبرد</h1>

        <div class="game-area" id="gameArea">

            <div class="game-hud">
                <span>⭐ امتیاز: <span id="gameScore">0</span></span>
                <span>❤️ <span id="lives">3</span></span>
            </div>

            <div class="game-message" id="gameMessage">
                آماده‌ای فرمانده؟<br>
                برای شروع بازی دکمه پایین را بزن!
            </div>

            <div class="ship" id="ship">🚀</div>

        </div>

        <div class="game-controls">
            <button onclick="moveShip(-1)">◀ حرکت</button>
            <button class="start" id="startButton"
                    onclick="startGame()">شروع بازی 🚀</button>
            <button onclick="moveShip(1)">حرکت ▶</button>
        </div>

        <p style="color:var(--muted);font-size:12px;text-align:center;margin-top:12px">
            با حرکت سفینه و لمس ستاره‌ها امتیاز جمع کن.
        </p>

    </section>

</div>

<div class="toast" id="toast"></div>

<script>

/* -----------------------------------
   ذخیره‌سازی اطلاعات
----------------------------------- */

const defaultState = {
    name: "APACHI",
    avatar: "👾",
    points: 15950,
    coins: 604,
    sound: true,
    purchased: [],
    equipped: "laser1",
    saveCode: "SEPI-AP01"
};

let state;

try {
    state = {
        ...defaultState,
        ...JSON.parse(localStorage.getItem("spaceCommanderSave") || "{}")
    };
} catch {
    state = {...defaultState};
}

function saveState() {
    localStorage.setItem("spaceCommanderSave", JSON.stringify(state));
}

function showToast(message) {
    const toast = document.getElementById("toast");
    toast.textContent = message;
    toast.classList.add("show");

    clearTimeout(window.toastTimer);

    window.toastTimer = setTimeout(() => {
        toast.classList.remove("show");
    }, 2200);
}

/* -----------------------------------
   تب‌ها
----------------------------------- */

document.querySelectorAll(".tab").forEach(tab => {
    tab.addEventListener("click", () => {

        document.querySelectorAll(".tab").forEach(t =>
            t.classList.remove("active")
        );

        document.querySelectorAll(".page").forEach(p =>
            p.classList.remove("active")
        );

        tab.classList.add("active");

        document.getElementById(tab.dataset.page)
                .classList.add("active");

        if (tab.dataset.page === "ranks") renderRanks();
        if (tab.dataset.page === "store") renderStore();
    });
});

/* -----------------------------------
   پروفایل
----------------------------------- */

function updateProfile() {
    document.getElementById("profileName").textContent = state.name;
    document.getElementById("nameInput").value = state.name;
    document.getElementById("profileAvatar").textContent = state.avatar;
    document.getElementById("points").textContent =
        state.points.toLocaleString("fa-IR");

    document.getElementById("coins").textContent =
        state.coins.toLocaleString("fa-IR");

    document.getElementById("saveCode").textContent = state.saveCode;

    document.querySelectorAll(".avatar-option").forEach(button => {
        button.classList.toggle(
            "selected",
            button.dataset.avatar === state.avatar
        );
    });

    document.getElementById("soundToggle")
            .classList.toggle("on", state.sound);
}

document.querySelectorAll(".avatar-option").forEach(button => {
    button.addEventListener("click", () => {
        state.avatar = button.dataset.avatar;
        updateProfile();
    });
});

function saveProfile() {
    state.name =
        document.getElementById("nameInput").value.trim() || "APACHI";

    saveState();
    updateProfile();

    showToast("پروفایل با موفقیت ذخیره شد! ✨");
}

function toggleSound() {
    state.sound = !state.sound;
    saveState();
    updateProfile();
}

let audioObject = null;

document.getElementById("musicFile").addEventListener("change", event => {
    const file = event.target.files[0];

    if (!file) return;

    if (audioObject) audioObject.pause();

    audioObject = new Audio(URL.createObjectURL(file));

    showToast("آهنگ انتخاب شد 🎵");
});

function playMusic() {
    if (!audioObject) {
        showToast("اول یک آهنگ انتخاب کن!");
        return;
    }

    audioObject.play().catch(() => {
        showToast("برای پخش آهنگ دوباره لمس کن.");
    });
}

function pauseMusic() {
    if (audioObject) audioObject.pause();
}

/* -----------------------------------
   رتبه‌بندی
----------------------------------- */

const samplePlayers = [
    {name: "M3D", score: 94980, avatar: "👹"},
    {name: "فضانورد 8362", score: 76890, avatar: "💎"},
    {name: "Ferguson", score: 56070, avatar: "🌟"},
    {name: "SEPEHR", score: 47710, avatar: "👹"},
    {name: "N", score: 38070, avatar: "🔺"},
    {name: "Feshari", score: 30460, avatar: "👾"},
    {name: "مبر3952", score: 26980, avatar: "👾"},
    {name: "فضانورد 6721", score: 22440, avatar: "🔺"}
];

function renderRanks() {

    const players = [
        ...samplePlayers,
        {
            name: state.name,
            score: state.points,
            avatar: state.avatar
        }
    ];

    players.sort((a, b) => b.score - a.score);

    document.getElementById("rankList").innerHTML =
        players.slice(0, 20).map((player, index) => `
            <div class="rank-row">
                <div class="rank-score">
                    ${player.score.toLocaleString("fa-IR")}
                </div>

                <div class="rank-name">
                    ${escapeHTML(player.name)}
                    <span class="rank-icon">${player.avatar}</span>
                </div>

                <div class="rank-number">
                    ${index + 1}
                </div>
            </div>
        `).join("");
}

function escapeHTML(text) {
    return String(text).replace(/[&<>"']/g, char => ({
        "&": "&amp;",
        "<": "&lt;",
        ">": "&gt;",
        '"': "&quot;",
        "'": "&#39;"
    })[char]);
}

/* -----------------------------------
   فروشگاه و تصاویر سلاح‌ها
----------------------------------- */

/*
  تصاویر سلاح‌ها با SVG طراحی شده‌اند.
  بنابراین سایت برای نمایش آن‌ها به عکس خارجی نیاز ندارد.
*/

const weapons = [
    {
        id: "laser1",
        name: "لیزر ساده",
        price: 200,
        damage: 1,
        color: "#00f5f0",
        description: "دمیج: 1 | تعداد تیر: 1",
        shape: "laser"
    },
    {
        id: "double",
        name: "شلیک دوقلو",
        price: 350,
        damage: 2,
        color: "#ffe600",
        description: "دمیج: 1 | تعداد تیر: 2",
        shape: "double"
    },
    {
        id: "redlaser",
        name: "لیزر سرخ",
        price: 500,
        damage: 2,
        color: "#ff285d",
        description: "دمیج: 2 | تعداد تیر: 1",
        shape: "red"
    },
    {
        id: "plasma",
        name: "پلاسما",
        price: 750,
        damage: 3,
        color: "#bb59ff",
        description: "دمیج: 3 | تعداد تیر: 2",
        shape: "plasma"
    },
    {
        id: "blaster",
        name: "بلاستر فضایی",
        price: 1000,
        damage: 4,
        color: "#00ff9d",
        description: "دمیج: 4 | تعداد تیر: 3",
        shape: "blaster"
    }
];

const ships = [
    {
        id: "ship1",
        name: "سفینه نئون",
        price: 300,
        color: "#00f5f0",
        shape: "ship"
    },
    {
        id: "ship2",
        name: "سفینه بنفش",
        price: 500,
        color: "#bb59ff",
        shape: "ship"
    },
    {
        id: "ship3",
        name: "سفینه طلایی",
        price: 900,
        color: "#ffe600",
        shape: "ship"
    }
];

let currentStore = "weapons";

function weaponSVG(item) {

    const color = item.color;

    if (item.shape === "ship") {
        return `
        <svg viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg">
            <defs>
                <filter id="glow">
                    <feGaussianBlur stdDeviation="4"/>
                </filter>
            </defs>

            <path d="M60 15 L82 85 L60 74 L38 85 Z"
                  fill="${color}" opacity=".25"
                  filter="url(#glow)"/>

            <path d="M60 15 L82 85 L60 74 L38 85 Z"
                  fill="${color}"/>

            <path d="M60 30 L70 72 L60 64 L50 72 Z"
                  fill="#f5ffff"/>

            <path d="M43 70 L20 94 L48 84 Z"
                  fill="${color}"/>

            <path d="M77 70 L100 94 L72 84 Z"
                  fill="${color}"/>

            <circle cx="60" cy="52" r="5" fill="#06121c"/>

            <path d="M49 89 L60 110 L71 89 Z"
                  fill="#ff7b32"/>
        </svg>`;
    }

    if (item.shape === "double") {
        return `
        <svg viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg">
            <g fill="${color}" stroke="#fff3a0" stroke-width="2">
                <rect x="25" y="28" width="19" height="65" rx="7"/>
                <rect x="76" y="28" width="19" height="65" rx="7"/>
                <rect x="29" y="15" width="11" height="25" rx="5"/>
                <rect x="80" y="15" width="11" height="25" rx="5"/>
            </g>
            <path d="M35 96 L35 108 M85 96 L85 108"
                  stroke="${color}" stroke-width="5"/>
        </svg>`;
    }

    if (item.shape === "plasma") {
        return `
        <svg viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg">
            <circle cx="60" cy="60" r="38" fill="${color}" opacity=".12"/>
            <circle cx="60" cy="60" r="27" fill="${color}" opacity=".2"/>
            <path d="M60 17 L72 46 L103 60 L72 74
                     L60 103 L48 74 L17 60 L48 46 Z"
                  fill="${color}"/>
            <circle cx="60" cy="60" r="12" fill="#fff"/>
            <circle cx="60" cy="60" r="6" fill="${color}"/>
        </svg>`;
    }

    if (item.shape === "blaster") {
        return `
        <svg viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg">
            <path d="M17 48 L103 48 L92 72 L28 72 Z"
                  fill="${color}"/>
            <rect x="48" y="30" width="24" height="58" rx="5"
                  fill="#d9fff1"/>
            <rect x="53" y="20" width="14" height="25" rx="5"
                  fill="${color}"/>
            <path d="M48 80 L35 102 L55 93 Z"
                  fill="#ff8033"/>
            <path d="M72 80 L85 102 L65 93 Z"
                  fill="#ff8033"/>
        </svg>`;
    }

    if (item.shape === "red") {
        return `
        <svg viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg">
            <path d="M60 12 L73 42 L103 60 L73 78
                     L60 108 L47 78 L17 60 L47 42 Z"
                  fill="${color}" opacity=".25"/>
            <path d="M60 22 L70 50 L98 60 L70 70
                     L60 98 L50 70 L22 60 L50 50 Z"
                  fill="${color}"/>
            <circle cx="60" cy="60" r="13" fill="#fff"/>
            <circle cx="60" cy="60" r="7" fill="${color}"/>
        </svg>`;
    }

    return `
    <svg viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg">
        <path d="M60 12 L76 83 L60 73 L44 83 Z"
              fill="${color}" opacity=".3"/>
        <path d="M60 18 L74 83 L60 73 L46 83 Z"
              fill="${color}"/>
        <rect x="54" y="31" width="12" height="33" rx="6"
              fill="#f4ffff"/>
        <path d="M46 77 L26 96 L50 88 Z"
              fill="${color}"/>
        <path d="M74 77 L94 96 L70 88 Z"
              fill="${color}"/>
        <path d="M54 87 L60 107 L66 87 Z"
              fill="#ff7937"/>
    </svg>`;
}

function renderStore() {

    const list = currentStore === "weapons" ? weapons : ships;

    document.getElementById("weaponList").innerHTML =
        list.map(item => {

            const owned = state.purchased.includes(item.id);
            const equipped = state.equipped === item.id;

            let actionText = "خرید و تجهیز";
            let actionClass = "weapon-action";

            if (equipped) {
                actionText = "✓ مجهز شده";
                actionClass += " equipped";
            } else if (owned) {
                actionText = "تجهیز کردن";
            }

            return `
                <div class="weapon-card">

                    <div class="weapon-image">
                        ${weaponSVG(item)}
                    </div>

                    <div class="weapon-info">

                        <h3>${item.name}</h3>

                        <p>
                            ${item.description || "سفینه ویژه فرمانده"}
                        </p>

                        <button class="${actionClass}"
                                onclick="buyOrEquip('${item.id}')">
                            ${actionText}
                        </button>

                        <span class="weapon-price">
                            ⚡ ${owned ? "خریداری شده" :
                                "ارتقا 💰 " + item.price + " سکه"}
                        </span>

                    </div>
                </div>
            `;
        }).join("");
}

document.querySelectorAll(".store-switch button").forEach(button => {

    button.addEventListener("click", () => {

        document.querySelectorAll(".store-switch button")
                .forEach(b => b.classList.remove("active"));

        button.classList.add("active");

        currentStore = button.dataset.store;

        renderStore();
    });
});

function buyOrEquip(id) {

    const item = [...weapons, ...ships].find(w => w.id === id);

    if (!item) return;

    const owned = state.purchased.includes(id);

    if (!owned) {

        if (state.coins < item.price) {
            showToast("سکه کافی نداری! 💰");
            return;
        }

        state.coins -= item.price;
        state.purchased.push(id);

        showToast(item.name + " خریداری شد! 🚀");
    }

    state.equipped = id;

    saveState();
    updateProfile();
    renderStore();
}

/* -----------------------------------
   بازی فضایی
----------------------------------- */

const gameArea = document.getElementById("gameArea");
const ship = document.getElementById("ship");

let gameRunning = false;
let gameScore = 0;
let shipPosition = 50;
let gameTimer = null;
let starTimer = null;

function createStars() {

    for (let i = 0; i < 60; i++) {

        const star = document.createElement("div");

        star.className = "game-star";

        star.style.left = Math.random() * 100 + "%";
        star.style.top = Math.random() * 100 + "%";

        star.style.animationDelay = Math.random() * 2 + "s";

        gameArea.appendChild(star);
    }
}

function moveShip(direction) {

    shipPosition += direction * 8;

    shipPosition = Math.max(5, Math.min(95, shipPosition));

    ship.style.left = `calc(${shipPosition}% - 32px)`;
}

function startGame() {

    if (gameRunning) return;

    gameRunning = true;
    gameScore = 0;
    shipPosition = 50;

    document.getElementById("gameScore").textContent = "0";

    document.getElementById("gameMessage").style.display = "none";

    document.getElementById("startButton").textContent = "بازی در حال اجراست";

    gameTimer = setInterval(() => {

        gameScore += 10;

        document.getElementById("gameScore").textContent =
            gameScore.toLocaleString("fa-IR");

        if (Math.random() < .3) {
            createGameTarget();
        }

    }, 700);

    starTimer = setTimeout(endGame, 20000);

    showToast("بازی شروع شد! ⭐");
}

function createGameTarget() {

    const target = document.createElement("div");

    target.textContent = "⭐";

    target.style.position = "absolute";
    target.style.left = Math.random() * 90 + "%";
    target.style.top = "0";
    target.style.fontSize = "25px";
    target.style.cursor = "pointer";
    target.style.zIndex = "2";

    gameArea.appendChild(target);

    let top = 0;

    const falling = setInterval(() => {

        if (!gameRunning) {
            clearInterval(falling);
            target.remove();
            return;
        }

        top += 3;

        target.style.top = top + "px";

        if (top > gameArea.clientHeight) {
            clearInterval(falling);
            target.remove();
        }

    }, 40);

    target.addEventListener("click", () => {

        gameScore += 100;

        document.getElementById("gameScore").textContent =
            gameScore.toLocaleString("fa-IR");

        target.remove();

        clearInterval(falling);
    });
}

function endGame() {

    gameRunning = false;

    clearInterval(gameTimer);
    clearTimeout(starTimer);

    const earnedCoins = Math.floor(gameScore / 100);

    state.points += gameScore;
    state.coins += earnedCoins;

    saveState();
    updateProfile();

    document.getElementById("gameMessage").innerHTML =
        `بازی تمام شد! 🚀<br>
         امتیاز: ${gameScore}<br>
         سکه جایزه: ${earnedCoins}`;

    document.getElementById("gameMessage").style.display = "block";

    document.getElementById("startButton").textContent =
        "دوباره بازی کن";

    renderRanks();

    showToast("امتیاز و سکه به حسابت اضافه شد!");
}

gameArea.addEventListener("touchstart", event => {

    if (!gameRunning) return;

    const touch = event.touches[0];

    const rect = gameArea.getBoundingClientRect();

    const x = touch.clientX - rect.left;

    shipPosition = Math.max(
        5,
        Math.min(95, (x / rect.width) * 100)
    );

    ship.style.left = `calc(${shipPosition}% - 32px)`;

}, {passive: true});

/* -----------------------------------
   شروع سایت
----------------------------------- */

createStars();
updateProfile();
renderStore();
renderRanks();

</script>

</body>
</html>

<!DOCTYPE html>
<html lang="uz">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Hill Climb Racing</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Bangers&family=Nunito:wght@700;900&display=swap');

:root {
  --gold: #FFD93D;
  --orange: #FF6B35;
  --red: #EF233C;
  --green: #06D6A0;
  --blue: #118AB2;
  --dark: #073B4C;
  --sky1: #87CEEB;
  --sky2: #FFF3B0;
}

* { margin:0; padding:0; box-sizing:border-box; -webkit-tap-highlight-color:transparent; }

html, body {
  width:100%; height:100%;
  overflow:hidden;
  background:#1a1a2e;
  font-family:'Nunito', sans-serif;
  touch-action:none;
  user-select:none;
}

#gameContainer {
  width:100%;
  height:100%;
  position:relative;
  display:flex;
  flex-direction:column;
}

canvas {
  width:100%;
  height:100%;
  display:block;
  image-rendering:pixelated;
}

/* ===== HUD ===== */
#hud {
  position:absolute;
  top:0; left:0; right:0;
  padding:10px 14px 0;
  display:flex;
  justify-content:space-between;
  align-items:flex-start;
  pointer-events:none;
  z-index:10;
}

.hud-panel {
  background:linear-gradient(135deg,rgba(0,0,0,0.65),rgba(0,0,0,0.4));
  border:2px solid rgba(255,255,255,0.18);
  border-radius:14px;
  padding:6px 13px;
  backdrop-filter:blur(4px);
  display:flex;
  align-items:center;
  gap:6px;
}

.hud-icon { font-size:1.1rem; }

.hud-val {
  font-family:'Bangers', cursive;
  font-size:1.25rem;
  letter-spacing:1px;
  color:#fff;
  text-shadow:0 1px 4px rgba(0,0,0,0.6);
}

.hud-val.gold { color:var(--gold); }
.hud-val.green { color:var(--green); }

/* fuel bar */
#fuelWrap {
  position:absolute;
  top:58px; left:14px;
  width:140px;
  pointer-events:none;
  z-index:10;
}

#fuelLabel {
  font-family:'Bangers',cursive;
  font-size:0.85rem;
  color:var(--green);
  letter-spacing:1px;
  margin-bottom:3px;
}

#fuelTrack {
  height:10px;
  background:rgba(0,0,0,0.45);
  border:2px solid rgba(255,255,255,0.2);
  border-radius:6px;
  overflow:hidden;
}

#fuelBar {
  height:100%;
  width:100%;
  background:linear-gradient(90deg,#06D6A0,#00F5A0);
  border-radius:4px;
  transition:width 0.08s, background 0.3s;
}

/* ===== BUTTONS ===== */
#controls {
  position:absolute;
  bottom:18px; left:0; right:0;
  display:flex;
  justify-content:space-between;
  padding:0 18px;
  z-index:10;
  pointer-events:none;
}

.ctrl-btn {
  pointer-events:all;
  width:110px;
  height:100px;
  border-radius:22px;
  border:none;
  cursor:pointer;
  display:flex;
  flex-direction:column;
  align-items:center;
  justify-content:center;
  gap:4px;
  transition:transform 0.08s;
  -webkit-user-select:none;
  touch-action:manipulation;
  position:relative;
  overflow:hidden;
}

.ctrl-btn::before {
  content:'';
  position:absolute;
  inset:0;
  border-radius:22px;
  opacity:0;
  transition:opacity 0.08s;
}

.ctrl-btn:active::before { opacity:1; }
.ctrl-btn:active { transform:scale(0.93); }

#btnBrake {
  background:linear-gradient(135deg,#EF233C,#B5152A);
  box-shadow:0 6px 22px rgba(239,35,60,0.55), inset 0 2px 0 rgba(255,255,255,0.2);
  border:2.5px solid rgba(255,100,100,0.35);
}
#btnBrake::before { background:rgba(255,255,255,0.12); }

#btnGas {
  background:linear-gradient(135deg,#06D6A0,#028A5E);
  box-shadow:0 6px 22px rgba(6,214,160,0.55), inset 0 2px 0 rgba(255,255,255,0.2);
  border:2.5px solid rgba(100,255,200,0.35);
}
#btnGas::before { background:rgba(255,255,255,0.12); }

.btn-icon { font-size:2.2rem; line-height:1; }
.btn-label {
  font-family:'Bangers',cursive;
  font-size:1.05rem;
  letter-spacing:2px;
  color:rgba(255,255,255,0.92);
  text-shadow:0 1px 4px rgba(0,0,0,0.4);
}

/* ===== SCREENS ===== */
.screen {
  position:absolute; inset:0;
  display:flex; flex-direction:column;
  align-items:center; justify-content:center;
  z-index:30;
  transition:opacity 0.3s;
}

#mainMenu {
  background:linear-gradient(160deg,#1a1a2e,#16213e,#0f3460);
}

.logo {
  font-family:'Bangers',cursive;
  font-size:clamp(2.8rem,9vw,5rem);
  line-height:1;
  text-align:center;
  margin-bottom:8px;
}
.logo span.line1 { display:block; color:var(--gold); text-shadow:0 0 30px rgba(255,217,61,0.6), 4px 4px 0 #B8860B; }
.logo span.line2 { display:block; color:#fff; text-shadow:4px 4px 0 rgba(0,0,0,0.4); }

.logo-sub {
  color:rgba(255,255,255,0.55);
  font-size:0.9rem;
  margin-bottom:40px;
  letter-spacing:2px;
  text-transform:uppercase;
}

/* vehicle select */
#vehicleSelect {
  display:flex;
  gap:16px;
  margin-bottom:32px;
  flex-wrap:wrap;
  justify-content:center;
  padding:0 16px;
}

.vehicle-card {
  background:rgba(255,255,255,0.07);
  border:2.5px solid rgba(255,255,255,0.15);
  border-radius:18px;
  padding:14px 18px;
  cursor:pointer;
  transition:all 0.2s;
  text-align:center;
  min-width:90px;
}

.vehicle-card:hover, .vehicle-card.selected {
  border-color:var(--gold);
  background:rgba(255,217,61,0.15);
  transform:translateY(-4px);
  box-shadow:0 8px 24px rgba(255,217,61,0.3);
}

.vehicle-card .v-icon { font-size:2.5rem; display:block; margin-bottom:6px; }
.vehicle-card .v-name {
  font-family:'Bangers',cursive;
  font-size:1rem;
  color:#fff;
  letter-spacing:1px;
}

/* stage select */
#stageSelect {
  display:flex;
  gap:12px;
  margin-bottom:28px;
  flex-wrap:wrap;
  justify-content:center;
  padding:0 14px;
}

.stage-card {
  border-radius:14px;
  padding:10px 16px;
  cursor:pointer;
  border:2.5px solid rgba(255,255,255,0.12);
  background:rgba(255,255,255,0.06);
  text-align:center;
  transition:all 0.2s;
  min-width:80px;
}

.stage-card.selected, .stage-card:hover {
  border-color:var(--orange);
  background:rgba(255,107,53,0.2);
  transform:scale(1.06);
}

.stage-card .s-icon { font-size:1.6rem; }
.stage-card .s-name {
  font-family:'Bangers',cursive;
  color:#fff;
  font-size:0.88rem;
  letter-spacing:1px;
  margin-top:3px;
}

/* section titles */
.section-title {
  font-family:'Bangers',cursive;
  font-size:1.1rem;
  color:rgba(255,255,255,0.5);
  letter-spacing:3px;
  text-transform:uppercase;
  margin-bottom:12px;
  text-align:center;
}

.play-btn {
  background:linear-gradient(135deg,var(--gold),#FF9500);
  color:#1a1a2e;
  font-family:'Bangers',cursive;
  font-size:1.6rem;
  letter-spacing:3px;
  padding:16px 56px;
  border:none;
  border-radius:50px;
  cursor:pointer;
  box-shadow:0 6px 28px rgba(255,217,61,0.55), inset 0 2px 0 rgba(255,255,255,0.3);
  transition:transform 0.12s, box-shadow 0.12s;
}
.play-btn:hover { transform:scale(1.05); box-shadow:0 10px 36px rgba(255,217,61,0.7); }
.play-btn:active { transform:scale(0.96); }

/* Pause */
#pauseScreen {
  background:rgba(0,0,0,0.78);
  backdrop-filter:blur(8px);
}

#pauseScreen h2 {
  font-family:'Bangers',cursive;
  font-size:3.5rem;
  color:var(--gold);
  text-shadow:0 0 20px rgba(255,217,61,0.5);
  margin-bottom:30px;
}

.menu-btn {
  font-family:'Bangers',cursive;
  font-size:1.3rem;
  letter-spacing:2px;
  padding:13px 42px;
  border-radius:50px;
  border:none;
  cursor:pointer;
  margin:7px;
  transition:transform 0.1s;
}
.menu-btn:active { transform:scale(0.95); }

.btn-resume { background:var(--green); color:#073B4C; box-shadow:0 4px 18px rgba(6,214,160,0.4); }
.btn-home { background:rgba(255,255,255,0.12); color:#fff; border:2px solid rgba(255,255,255,0.2); }

/* Game Over */
#gameOver {
  background:rgba(0,0,0,0.82);
  backdrop-filter:blur(10px);
}

#gameOver h2 {
  font-family:'Bangers',cursive;
  font-size:4rem;
  color:var(--red);
  text-shadow:0 0 30px rgba(239,35,60,0.6);
  margin-bottom:6px;
}

.go-stat-row {
  display:flex;
  gap:20px;
  margin:18px 0 28px;
  flex-wrap:wrap;
  justify-content:center;
}

.go-stat {
  background:rgba(255,255,255,0.08);
  border:2px solid rgba(255,255,255,0.14);
  border-radius:16px;
  padding:12px 22px;
  text-align:center;
}

.go-stat .gs-icon { font-size:1.8rem; }
.go-stat .gs-val {
  font-family:'Bangers',cursive;
  font-size:1.8rem;
  color:var(--gold);
  display:block;
}
.go-stat .gs-label {
  font-size:0.78rem;
  color:rgba(255,255,255,0.5);
  text-transform:uppercase;
  letter-spacing:1px;
}

.btn-retry { background:linear-gradient(135deg,var(--orange),#C94F1E); color:#fff; box-shadow:0 4px 18px rgba(255,107,53,0.45); }

/* Pause btn top right */
#pauseBtn {
  position:absolute;
  top:10px; right:14px;
  width:40px; height:40px;
  background:rgba(0,0,0,0.5);
  border:2px solid rgba(255,255,255,0.2);
  border-radius:10px;
  color:#fff;
  font-size:1.3rem;
  cursor:pointer;
  z-index:11;
  display:none;
  align-items:center;
  justify-content:center;
}

/* coin pop anim */
@keyframes coinPop {
  0%   { opacity:1; transform:translateY(0) scale(1); }
  100% { opacity:0; transform:translateY(-50px) scale(1.5); }
}
.coin-pop {
  position:absolute;
  font-family:'Bangers',cursive;
  font-size:1.2rem;
  color:var(--gold);
  text-shadow:0 0 8px rgba(255,217,61,0.8);
  pointer-events:none;
  z-index:20;
  animation:coinPop 0.7s ease-out forwards;
}

/* hidden */
.hidden { display:none !important; }
</style>
</head>
<body>

<div id="gameContainer">
  <canvas id="c"></canvas>

  <!-- HUD -->
  <div id="hud" class="hidden">
    <div class="hud-panel">
      <span class="hud-icon">📍</span>
      <span class="hud-val" id="hudDist">0 m</span>
    </div>
    <div class="hud-panel">
      <span class="hud-icon">🪙</span>
      <span class="hud-val gold" id="hudCoins">0</span>
    </div>
    <div class="hud-panel">
      <span class="hud-icon">💨</span>
      <span class="hud-val green" id="hudSpeed">0</span>
    </div>
  </div>

  <div id="fuelWrap" class="hidden">
    <div id="fuelLabel">⛽ YONILG'I</div>
    <div id="fuelTrack"><div id="fuelBar"></div></div>
  </div>

  <button id="pauseBtn">⏸</button>

  <!-- Controls -->
  <div id="controls" style="display:none">
    <button class="ctrl-btn" id="btnBrake">
      <span class="btn-icon">◀</span>
      <span class="btn-label">TORMOZ</span>
    </button>
    <button class="ctrl-btn" id="btnGas">
      <span class="btn-icon">▶</span>
      <span class="btn-label">GAZ</span>
    </button>
  </div>

  <!-- Main Menu -->
  <div class="screen" id="mainMenu">
    <div class="logo">
      <span class="line1">HILL CLIMB</span>
      <span class="line2">RACING</span>
    </div>
    <p class="logo-sub">Tepalikda poyga!</p>

    <p class="section-title">🚗 Mashina tanlang</p>
    <div id="vehicleSelect">
      <div class="vehicle-card selected" data-v="jeep">
        <span class="v-icon">🚙</span>
        <span class="v-name">JEEP</span>
      </div>
      <div class="vehicle-card" data-v="truck">
        <span class="v-icon">🚛</span>
        <span class="v-name">YUK</span>
      </div>
      <div class="vehicle-card" data-v="racing">
        <span class="v-icon">🏎️</span>
        <span class="v-name">SPORT</span>
      </div>
      <div class="vehicle-card" data-v="monster">
        <span class="v-icon">🚜</span>
        <span class="v-name">MONSTER</span>
      </div>
    </div>

    <p class="section-title">🌍 Maydon tanlang</p>
    <div id="stageSelect">
      <div class="stage-card selected" data-s="country">
        <div class="s-icon">🌄</div>
        <div class="s-name">TEPALIK</div>
      </div>
      <div class="stage-card" data-s="desert">
        <div class="s-icon">🏜️</div>
        <div class="s-name">CHO'L</div>
      </div>
      <div class="stage-card" data-s="arctic">
        <div class="s-icon">❄️</div>
        <div class="s-name">ARKTIKA</div>
      </div>
      <div class="stage-card" data-s="moon">
        <div class="s-icon">🌙</div>
        <div class="s-name">OY</div>
      </div>
    </div>

    <button class="play-btn" id="playBtn">▶ O'YINNI BOSHLASH</button>
  </div>

  <!-- Pause Screen -->
  <div class="screen hidden" id="pauseScreen">
    <h2>⏸ PAUZA</h2>
    <div class="go-stat-row" id="pauseStats"></div>
    <button class="menu-btn btn-resume" id="resumeBtn">▶ DAVOM ETISH</button>
    <br>
    <button class="menu-btn btn-home" id="pauseHomeBtn">🏠 BOSH MENYU</button>
  </div>

  <!-- Game Over Screen -->
  <div class="screen hidden" id="gameOver">
    <h2 id="goTitle">💥 O'YIN TUGADI</h2>
    <p style="color:rgba(255,255,255,0.5); font-size:0.9rem; margin-bottom:4px; letter-spacing:1px;">NATIJANGIZ</p>
    <div class="go-stat-row">
      <div class="go-stat">
        <div class="gs-icon">📍</div>
        <span class="gs-val" id="goDistVal">0</span>
        <span class="gs-label">Masofa (m)</span>
      </div>
      <div class="go-stat">
        <div class="gs-icon">🪙</div>
        <span class="gs-val" id="goCoinVal">0</span>
        <span class="gs-label">Tangalar</span>
      </div>
      <div class="go-stat">
        <div class="gs-icon">⏱️</div>
        <span class="gs-val" id="goTimeVal">0:00</span>
        <span class="gs-label">Vaqt</span>
      </div>
      <div class="go-stat">
        <div class="gs-icon">🏆</div>
        <span class="gs-val" id="goBestVal">0</span>
        <span class="gs-label">Rekord (m)</span>
      </div>
    </div>
    <button class="menu-btn btn-retry" id="retryBtn">🔄 QAYTA O'YNASH</button>
    <br>
    <button class="menu-btn btn-home" id="goHomeBtn">🏠 BOSH MENYU</button>
  </div>
</div>

<script>
// ==================== SETUP ====================
const canvas = document.getElementById('c');
const ctx = canvas.getContext('2d');
let W, H;

function resize() {
  W = canvas.width = canvas.offsetWidth;
  H = canvas.height = canvas.offsetHeight;
}
resize();
window.addEventListener('resize', () => { resize(); });

// ==================== GAME CONFIG ====================
const STAGES = {
  country: { sky1:'#87CEEB', sky2:'#FFF9C4', ground1:'#4CAF50', ground2:'#8B6F47', ground3:'#5D4037', horizon:'#81C784', clouds:true, gravity:0.45, particles:'#8BC34A' },
  desert:  { sky1:'#FF8C42', sky2:'#FFD580', ground1:'#DEB887', ground2:'#A0522D', ground3:'#8B4513', horizon:'#F4A460', clouds:false, gravity:0.45, particles:'#DAA520' },
  arctic:  { sky1:'#B0E0E6', sky2:'#E0F7FA', ground1:'#E8F5FF', ground2:'#90CAF9', ground3:'#1976D2', horizon:'#E3F2FD', clouds:true, gravity:0.4, particles:'#BBDEFB' },
  moon:    { sky1:'#0d0d1a', sky2:'#0d0d2e', ground1:'#888', ground2:'#555', ground3:'#333', horizon:'#666', clouds:false, gravity:0.25, particles:'#aaa' },
};

const VEHICLES = {
  jeep:    { name:'Jeep',          width:100, height:36, wheelR:16, color:'#E74C3C', color2:'#C0392B', topColor:'#E74C3C', power:1.0,  fuelRate:0.055, tireW:9,  mass:1.0,  cmX:2,  cmH:20, flipLimit:1.3  },
  truck:   { name:'Yuk mashinasi', width:130, height:48, wheelR:21, color:'#3498DB', color2:'#1A6FA0', topColor:'#2E86C1', power:0.7,  fuelRate:0.075, tireW:13, mass:2.2,  cmX:5,  cmH:30, flipLimit:1.5  },
  racing:  { name:'Sport',         width:88,  height:24, wheelR:14, color:'#F39C12', color2:'#D68910', topColor:'#F39C12', power:1.45, fuelRate:0.042, tireW:7,  mass:0.75, cmX:0,  cmH:13, flipLimit:1.2  },
  monster: { name:'Monster',       width:115, height:50, wheelR:26, color:'#8E44AD', color2:'#6C3483', topColor:'#9B59B6', power:0.88, fuelRate:0.09,  tireW:15, mass:1.8,  cmX:-2, cmH:36, flipLimit:1.45 },
};

// ==================== STATE ====================
let selectedVehicle = 'jeep';
let selectedStage = 'country';
let gameState = 'menu'; // menu | playing | paused | gameover
let keys = {};
let gasBtn = false, brakeBtn = false;
let score = { dist:0, coins:0, time:0, best:0 };
let fuel = 100;
let frameCount = 0;
let startTime = 0;
let gameTimer = 0;

// ==================== TERRAIN ====================
const SEG = 7;
let terrain = [];
let cameraX = 0;

function terrainY(arr, wx) {
  let idx = Math.floor(wx / SEG);
  idx = Math.max(0, Math.min(arr.length - 2, idx));
  let a = arr[idx], b = arr[idx+1];
  if (!a || !b) return H * 0.65;
  let t = (wx - a.x) / SEG;
  return a.y + (b.y - a.y) * t;
}

function terrainAngle(arr, wx) {
  let idx = Math.floor(wx / SEG);
  idx = Math.max(0, Math.min(arr.length - 2, idx));
  let a = arr[idx], b = arr[idx+1];
  if (!a || !b) return 0;
  return Math.atan2(b.y - a.y, SEG);
}

function buildTerrain() {
  terrain = [];
  let x = 0;
  let y = H * 0.58;
  // flat start
  for (let i = 0; i < 25; i++) { terrain.push({x, y}); x += SEG; }
  // random hills
  let waveAmp = 60, waveFrq = 0.018;
  for (let i = 0; i < 2000; i++) {
    let dy = Math.sin(x * waveFrq) * waveAmp + (Math.random()-0.48)*18;
    y = Math.max(H*0.22, Math.min(H*0.82, y + dy * 0.25 + (Math.random()-0.5)*22));
    terrain.push({x, y});
    x += SEG;
    // vary waves
    if (i % 80 === 0) { waveAmp = 40 + Math.random()*80; waveFrq = 0.01 + Math.random()*0.025; }
  }
}

function extendTerrain() {
  let last = terrain[terrain.length-1];
  if (!last) return;
  if (last.x - cameraX > W + 400) return;
  let x = last.x + SEG;
  let y = last.y;
  for (let i = 0; i < 300; i++) {
    let dy = (Math.random()-0.48)*28;
    y = Math.max(H*0.22, Math.min(H*0.82, y+dy));
    terrain.push({x, y});
    x += SEG;
  }
}

// ==================== COINS ====================
let coins = [];
function spawnCoins() {
  coins = [];
  for (let i = 30; i < terrain.length; i += 18) {
    let seg = terrain[i];
    if (!seg) continue;
    coins.push({ wx:seg.x, wy:seg.y - 38, r:10, collected:false });
  }
}

function spawnMoreCoins(fromIdx) {
  for (let i = fromIdx; i < terrain.length; i += 18) {
    let seg = terrain[i];
    if (!seg) continue;
    // avoid duplicates
    if (!coins.find(c => Math.abs(c.wx - seg.x) < 5)) {
      coins.push({ wx:seg.x, wy:seg.y - 38, r:10, collected:false });
    }
  }
}

// ==================== CAR ====================
let car = null;

function initCar() {
  const vd = VEHICLES[selectedVehicle];
  // Boshlang'ich Y ni terrain dan topamiz
  const startY = terrainY(terrain, 140);
  car = {
    worldX: 140,
    screenY: startY - vd.wheelR - 2,
    vy: 0,
    vel: 0,
    angle: 0,
    angularVel: 0,
    w1rot: 0,
    w2rot: 0,
    airborne: false,
    dead: false,
    vd,
  };
}

// ==================== PARTICLES ====================
let particles = [];

function addParticle(x, y, type) {
  if (type === 'exhaust') {
    particles.push({ x, y, vx:-2-Math.random()*2, vy:(Math.random()-0.5)*1.5, life:1, maxLife:1, r:4+Math.random()*4, type });
  } else if (type === 'dirt') {
    for (let i = 0; i < 6; i++) {
      particles.push({ x, y, vx:(Math.random()-0.5)*4, vy:-2-Math.random()*3, life:1, maxLife:1, r:2+Math.random()*3, type });
    }
  } else if (type === 'crash') {
    for (let i = 0; i < 20; i++) {
      particles.push({ x, y, vx:(Math.random()-0.5)*8, vy:-4-Math.random()*5, life:1, maxLife:1, r:3+Math.random()*5, type });
    }
  }
}

function updateParticles() {
  const stg = STAGES[selectedStage];
  for (let p of particles) {
    p.x += p.vx; p.y += p.vy;
    p.vy += 0.18;
    p.life -= 0.03;
  }
  particles = particles.filter(p => p.life > 0);
}

function drawParticles() {
  const stg = STAGES[selectedStage];
  for (let p of particles) {
    ctx.save();
    ctx.globalAlpha = p.life * 0.8;
    if (p.type === 'exhaust') ctx.fillStyle = 'rgba(200,200,200,0.7)';
    else if (p.type === 'dirt') ctx.fillStyle = stg.particles;
    else if (p.type === 'crash') ctx.fillStyle = '#FF6B35';
    ctx.beginPath();
    ctx.arc(p.x - cameraX + car.worldX - car.worldX, p.y, p.r, 0, Math.PI*2);
    ctx.fill();
    ctx.restore();
  }
}

// ==================== CLOUDS ====================
let clouds = [];
function initClouds() {
  clouds = [];
  for (let i = 0; i < 12; i++) {
    clouds.push({ x: Math.random()*W*3, y: 30+Math.random()*H*0.25, r1:30+Math.random()*30, r2:20+Math.random()*20, r3:25+Math.random()*25, speed:0.3+Math.random()*0.5 });
  }
}
initClouds();

// ==================== GAME LOGIC ====================
function updateCar() {
  if (!car || car.dead) return;
  const vd = car.vd;
  const stg = STAGES[selectedStage];
  const G = stg.gravity;
  const MAX_V = 5.5 * vd.power;
  const GAS_F = 0.16 * vd.power;
  const BRK_F = 0.22;

  const gasOn = gasBtn || keys['ArrowRight'] || keys['KeyD'];
  const brkOn = brakeBtn || keys['ArrowLeft'] || keys['KeyA'];

  // === YONILG'I ===
  if (gasOn && fuel > 0) fuel = Math.max(0, fuel - vd.fuelRate);
  fuel = Math.min(100, fuel + 0.011);

  // === TEZLIK ===
  if (gasOn && fuel > 0) {
    car.vel = Math.min(car.vel + GAS_F, MAX_V);
  } else if (brkOn) {
    if (car.vel > 0) {
      car.vel = Math.max(0, car.vel - BRK_F);
    } else {
      car.vel = Math.max(car.vel - BRK_F * 0.35, -MAX_V * 0.28);
    }
  } else {
    car.vel *= 0.976;
  }

  car.worldX += car.vel;
  car.worldX = Math.max(0, car.worldX);
  cameraX = car.worldX - W * 0.28;
  if (cameraX < 0) cameraX = 0;

  // === IKKI G'ILDIRAK KONTAKTI ===
  // Orqa va old g'ildirak world pozitsiyalari
  const rearWX  = car.worldX - vd.width * 0.42;
  const frontWX = car.worldX + vd.width * 0.42;
  const rearGY   = terrainY(terrain, rearWX);
  const frontGY  = terrainY(terrain, frontWX);
  const wheelBase = vd.width * 0.84;

  // === BURCHAK (faqat er yuzasidan) ===
  // Yer yuzasi burchagi — 2 g'ildirak orasidagi balandlik farqi
  const targetAngle = Math.atan2(frontGY - rearGY, wheelBase);

  // Og'irlik massasiga qarab silliqlik — og'ir mashina = sekinroq
  const smoothFactor = 1.0 / (vd.mass * 3.5 + 2.0);

  // === TUPROQDA / HAVODA ===
  if (!car.airborne) {
    // Ikki g'ildirak ham er bilan kontaktda — burchak faqat erdan
    car.angularVel = 0;
    car.angle += (targetAngle - car.angle) * smoothFactor * 4.5;

    // Vertikal pozitsiya: g'ildiraklar er ustida
    // Og'irlik markazi balandligi hisobga olinadi
    const cmOffset = vd.cmX; // old/orqaga og'ish
    const rearY  = rearGY  - vd.wheelR;
    const frontY = frontGY - vd.wheelR;
    // Interpolatsiya (0.5 = markaz, cmX offset bilan)
    const t = 0.5 + cmOffset / wheelBase;
    car.screenY = rearY + (frontY - rearY) * t - 2;
    car.vy = 0;

    // Airborne bo'lib ketdimi?
    // Agar mashina havoga ko'tarilsa — ikki g'ildirak ham erdan uziladi
    const rearContact  = car.screenY + vd.wheelR >= rearGY  - 4;
    const frontContact = car.screenY + vd.wheelR >= frontGY - 4;
    if (!rearContact && !frontContact) {
      car.airborne = true;
    }
  } else {
    // === HAVODA ===
    car.vy += G;
    car.screenY += car.vy;

    // Havoda: faqat og'irlik va havo qarshiligi burchakni o'zgartiradi
    // Gaz/tormoz burchakni O'ZGARTIRMAYDI — faqat tezlik
    // Burilish faqat g'ildirak orqali — yerga tegsa
    // Og'ir mashina havoda kamroq aylanadi
    const airRotation = (car.vel * 0.006) / vd.mass;
    car.angle += airRotation;

    // Yerga tushish
    const midGY = (rearGY + frontGY) / 2;
    if (car.screenY + vd.wheelR >= midGY) {
      car.airborne = false;
      car.vy = 0;
      car.screenY = midGY - vd.wheelR - 2;
      // Qattiq tushish — chiqindi
      addParticle(car.worldX - cameraX - vd.width*0.35, midGY, 'dirt');
      addParticle(car.worldX - cameraX + vd.width*0.35, midGY, 'dirt');
    }
  }

  // === G'ILDIRAK AYLANISHI ===
  car.w1rot += car.vel / vd.wheelR;
  car.w2rot += car.vel / vd.wheelR;

  // === ZARRALAR ===
  if (gasOn && fuel > 0 && frameCount % 2 === 0) {
    // Chiqindi gaz — orqa g'ildirak orqasidan
    const ex = car.worldX - cameraX - vd.width * 0.48;
    const ey = car.screenY - vd.height * 0.1;
    addParticle(ex, ey, 'exhaust');
  }
  if (brkOn && !car.airborne && car.vel > 1.0 && frameCount % 3 === 0) {
    // Tormoz changi — old g'ildirakdan
    addParticle(car.worldX - cameraX + vd.width*0.4, car.screenY + vd.wheelR - 4, 'dirt');
  }

  // === AG'DARISH TEKSHIRUVI ===
  if (Math.abs(car.angle) > vd.flipLimit) {
    car.dead = true;
    addParticle(car.worldX - cameraX, car.screenY, 'crash');
    setTimeout(() => showGameOver('flip'), 900);
  }

  // === EKRANDAN CHIQISH ===
  if (car.screenY > H + 150) {
    car.dead = true;
    setTimeout(() => showGameOver('fall'), 300);
  }

  // === HISOB ===
  score.dist = Math.max(score.dist, Math.floor((car.worldX - 140) / 35));
  gameTimer = Date.now() - startTime;

  // === TANGALAR ===
  const sx = car.worldX - cameraX;
  for (let coin of coins) {
    if (coin.collected) continue;
    const cx = coin.wx - cameraX;
    if (Math.abs(cx - sx) > 65) continue;
    if (Math.hypot(cx - sx, coin.wy - car.screenY) < 36) {
      coin.collected = true;
      score.coins++;
      showCoinPop(cx, coin.wy);
    }
  }

  extendTerrain();
}

function showCoinPop(x, y) {
  const el = document.createElement('div');
  el.className = 'coin-pop';
  el.textContent = '+🪙';
  el.style.left = x + 'px';
  el.style.top = y + 'px';
  document.getElementById('gameContainer').appendChild(el);
  setTimeout(() => el.remove(), 700);
}

// ==================== DRAWING ====================
function drawBackground() {
  const stg = STAGES[selectedStage];
  // sky gradient
  const grad = ctx.createLinearGradient(0, 0, 0, H*0.75);
  grad.addColorStop(0, stg.sky1);
  grad.addColorStop(1, stg.sky2);
  ctx.fillStyle = grad;
  ctx.fillRect(0, 0, W, H);

  // moon or sun
  if (selectedStage === 'moon') {
    ctx.save();
    ctx.shadowColor='#aaa'; ctx.shadowBlur=30;
    ctx.fillStyle='#ddd';
    ctx.beginPath(); ctx.arc(W*0.15 - cameraX*0.005, 60, 36, 0, Math.PI*2); ctx.fill();
    ctx.shadowBlur=0; ctx.restore();
    // stars
    ctx.fillStyle='rgba(255,255,255,0.85)';
    for (let i=0;i<80;i++) {
      let sx=(i*113.7+cameraX*0.02)%W;
      let sy=(i*71.3)%(H*0.55);
      let sr=Math.sin(frameCount*0.04+i)*0.4+0.8;
      ctx.beginPath(); ctx.arc(sx,sy,sr,0,Math.PI*2); ctx.fill();
    }
  } else if (selectedStage === 'arctic') {
    ctx.fillStyle='rgba(255,255,255,0.85)';
    ctx.shadowColor='#FFD700'; ctx.shadowBlur=40;
    ctx.beginPath(); ctx.arc(W*0.85 - cameraX*0.005, 55, 28, 0, Math.PI*2); ctx.fill();
    ctx.shadowBlur=0;
  } else if (selectedStage === 'desert') {
    ctx.save();
    ctx.shadowColor='#FF8C00'; ctx.shadowBlur=50;
    ctx.fillStyle='#FFE066';
    ctx.beginPath(); ctx.arc(W*0.8 - cameraX*0.005, 55, 34, 0, Math.PI*2); ctx.fill();
    ctx.shadowBlur=0; ctx.restore();
  } else {
    ctx.save();
    ctx.shadowColor='rgba(255,255,200,0.5)'; ctx.shadowBlur=30;
    ctx.fillStyle='#FFF9C4';
    ctx.beginPath(); ctx.arc(W*0.18 - cameraX*0.005, 60, 28, 0, Math.PI*2); ctx.fill();
    ctx.shadowBlur=0; ctx.restore();
  }

  // clouds
  if (stg.clouds) {
    ctx.fillStyle='rgba(255,255,255,0.82)';
    for (let cl of clouds) {
      let cx = ((cl.x - cameraX * cl.speed * 0.3) % (W * 3) + W*3) % (W*3) - W;
      ctx.save();
      ctx.translate(cx, cl.y);
      ctx.beginPath(); ctx.arc(0, 0, cl.r1, 0, Math.PI*2);
      ctx.arc(cl.r1*0.7, -8, cl.r2, 0, Math.PI*2);
      ctx.arc(-cl.r1*0.5, -5, cl.r3*0.7, 0, Math.PI*2);
      ctx.fill();
      ctx.restore();
    }
  }

  // distant mountains
  ctx.fillStyle = selectedStage==='moon' ? 'rgba(50,50,60,0.7)' : 'rgba(100,140,100,0.4)';
  ctx.beginPath();
  ctx.moveTo(0, H);
  const mpts = [0,H*0.5, W*0.15,H*0.32, W*0.3,H*0.48, W*0.45,H*0.28, W*0.6,H*0.42, W*0.75,H*0.25, W*0.9,H*0.4, W,H*0.35, W,H];
  for (let i=0;i<mpts.length;i+=2) {
    ctx.lineTo(mpts[i] - (cameraX*0.12)%W, mpts[i+1]);
  }
  ctx.closePath(); ctx.fill();
}

function drawTerrain() {
  const stg = STAGES[selectedStage];
  ctx.beginPath();
  ctx.moveTo(-10, H+10);
  let started = false;
  for (let seg of terrain) {
    let sx = seg.x - cameraX;
    if (sx < -SEG*3 || sx > W+SEG*3) continue;
    if (!started) { ctx.moveTo(sx, seg.y); started=true; }
    else ctx.lineTo(sx, seg.y);
  }
  ctx.lineTo(W+10, H+10);
  ctx.closePath();

  const gr = ctx.createLinearGradient(0, H*0.3, 0, H);
  gr.addColorStop(0, stg.ground1);
  gr.addColorStop(0.3, stg.ground2);
  gr.addColorStop(1, stg.ground3);
  ctx.fillStyle = gr;
  ctx.fill();

  // terrain edge line
  ctx.beginPath();
  started = false;
  for (let seg of terrain) {
    let sx = seg.x - cameraX;
    if (sx < -SEG*3 || sx > W+SEG*3) continue;
    if (!started) { ctx.moveTo(sx, seg.y); started=true; }
    else ctx.lineTo(sx, seg.y);
  }
  ctx.strokeStyle = selectedStage==='arctic' ? '#cceeff' : selectedStage==='moon' ? '#aaa' : '#5D9A52';
  ctx.lineWidth = 3;
  ctx.stroke();

  // decorations
  if (selectedStage !== 'moon') {
    for (let i=2; i<terrain.length; i+=5) {
      let seg = terrain[i];
      let sx = seg.x - cameraX;
      if (sx < -10 || sx > W+10) continue;
      if (selectedStage === 'arctic') {
        // snow bumps
        ctx.fillStyle = 'rgba(255,255,255,0.4)';
        ctx.beginPath(); ctx.arc(sx, seg.y-1, 3, Math.PI, 0); ctx.fill();
      } else if (selectedStage === 'desert') {
        // small pebbles
        if (i%15===0) {
          ctx.fillStyle='#C19A6B'; ctx.beginPath(); ctx.ellipse(sx,seg.y-2,5,3,0,0,Math.PI*2); ctx.fill();
        }
      } else {
        // grass
        if (i%3===0) {
          ctx.strokeStyle='#66BB6A'; ctx.lineWidth=1.5;
          for (let g=-1;g<=1;g++) {
            ctx.beginPath(); ctx.moveTo(sx+g*4, seg.y); ctx.lineTo(sx+g*4+(Math.random()-0.5)*3, seg.y-6-Math.random()*4); ctx.stroke();
          }
        }
      }
    }
  }
}

function drawCoins() {
  for (let coin of coins) {
    if (coin.collected) continue;
    let cx = coin.wx - cameraX;
    if (cx < -20 || cx > W+20) continue;
    ctx.save();
    ctx.translate(cx, coin.wy);
    let pulse = Math.sin(frameCount*0.12)*0.12+1;
    ctx.scale(pulse, pulse);

    ctx.shadowColor='#FFD93D'; ctx.shadowBlur=14;
    ctx.beginPath(); ctx.arc(0,0,coin.r,0,Math.PI*2);
    ctx.fillStyle='#FFD93D'; ctx.fill();
    ctx.strokeStyle='#F39C12'; ctx.lineWidth=2; ctx.stroke();
    ctx.shadowBlur=0;

    ctx.fillStyle='#B8860B';
    ctx.font=`bold ${coin.r}px Nunito`;
    ctx.textAlign='center'; ctx.textBaseline='middle';
    ctx.fillText('$',0,1);
    ctx.restore();
  }
}

function drawVehicle() {
  if (!car) return;
  const vd = car.vd;
  const sx = car.worldX - cameraX;
  const sy = car.screenY;
  const W2 = vd.width/2;

  ctx.save();
  ctx.translate(sx, sy);
  ctx.rotate(car.angle);
  if (car.dead) ctx.rotate(Math.PI);

  // body shadow
  ctx.save();
  ctx.scale(1, 0.2);
  ctx.translate(0, (vd.wheelR+8)/0.2);
  ctx.beginPath(); ctx.ellipse(0,0,W2*0.9,18,0,0,Math.PI*2);
  ctx.fillStyle='rgba(0,0,0,0.22)'; ctx.fill();
  ctx.restore();

  // JEEP / TRUCK / MONSTER type body
  if (selectedVehicle === 'racing') {
    drawRacingCar(vd);
  } else if (selectedVehicle === 'truck') {
    drawTruck(vd);
  } else if (selectedVehicle === 'monster') {
    drawMonster(vd);
  } else {
    drawJeep(vd);
  }

  ctx.restore();
}

function drawJeep(vd) {
  const W2 = vd.width/2;
  // body
  ctx.fillStyle = vd.color2;
  ctx.beginPath(); ctx.roundRect(-W2, -vd.height, vd.width, vd.height, 6); ctx.fill();
  // top cab
  ctx.fillStyle = vd.topColor;
  ctx.beginPath(); ctx.roundRect(-W2*0.55, -vd.height-26, vd.width*0.65, 28, [8,8,0,0]); ctx.fill();
  // windows
  ctx.fillStyle='rgba(173,216,230,0.8)';
  ctx.beginPath(); ctx.roundRect(-W2*0.5, -vd.height-22, 22, 18, 3); ctx.fill();
  ctx.beginPath(); ctx.roundRect(-W2*0.5+26, -vd.height-22, 20, 18, 3); ctx.fill();
  // door
  ctx.strokeStyle='rgba(0,0,0,0.15)'; ctx.lineWidth=1.5;
  ctx.strokeRect(-5, -vd.height+2, 22, vd.height-6);
  // bumpers
  ctx.fillStyle='#7F8C8D';
  ctx.fillRect(-W2-4, -vd.height+vd.height*0.3, 7, vd.height*0.5);
  ctx.fillRect(W2-3, -vd.height+vd.height*0.3, 7, vd.height*0.5);
  // headlight
  ctx.fillStyle='#FFF176'; ctx.shadowColor='#FFF176'; ctx.shadowBlur=12;
  ctx.beginPath(); ctx.arc(W2+2, -vd.height+vd.height*0.35, 5, 0, Math.PI*2); ctx.fill();
  ctx.shadowBlur=0;
  // taillight
  ctx.fillStyle='#EF233C'; ctx.shadowColor='#EF233C'; ctx.shadowBlur=8;
  ctx.beginPath(); ctx.arc(-W2-2, -vd.height+vd.height*0.35, 4, 0, Math.PI*2); ctx.fill();
  ctx.shadowBlur=0;
  // wheels
  drawWheel(-W2*0.55, 0, vd.wheelR, car.w1rot, vd.tireW, vd.color);
  drawWheel( W2*0.55, 0, vd.wheelR, car.w2rot, vd.tireW, vd.color);
}

function drawRacingCar(vd) {
  const W2 = vd.width/2;
  ctx.fillStyle = vd.color2;
  ctx.beginPath();
  ctx.moveTo(-W2, 0);
  ctx.lineTo(-W2, -vd.height*0.6);
  ctx.lineTo(-W2*0.6, -vd.height);
  ctx.lineTo( W2*0.55, -vd.height*1.1);
  ctx.lineTo( W2, -vd.height*0.4);
  ctx.lineTo( W2, 0);
  ctx.closePath(); ctx.fill();
  // cockpit
  ctx.fillStyle='rgba(100,180,255,0.75)';
  ctx.beginPath(); ctx.ellipse(0, -vd.height*0.9, W2*0.35, vd.height*0.35, 0, Math.PI, Math.PI*2); ctx.fill();
  // stripes
  ctx.strokeStyle='rgba(255,255,255,0.4)'; ctx.lineWidth=3;
  ctx.beginPath(); ctx.moveTo(-W2*0.4,-vd.height*0.1); ctx.lineTo(W2*0.4,-vd.height*0.1); ctx.stroke();
  // headlight
  ctx.fillStyle='#FFF9'; ctx.shadowColor='#FFF'; ctx.shadowBlur=16;
  ctx.beginPath(); ctx.arc(W2-2,-vd.height*0.3,5,0,Math.PI*2); ctx.fill(); ctx.shadowBlur=0;
  // spoiler
  ctx.fillStyle='#555'; ctx.fillRect(-W2,-vd.height-5,vd.width*0.3,4);
  // wheels
  drawWheel(-W2*0.6, 0, vd.wheelR, car.w1rot, vd.tireW, vd.color);
  drawWheel( W2*0.6, 0, vd.wheelR, car.w2rot, vd.tireW, vd.color);
}

function drawTruck(vd) {
  const W2 = vd.width/2;
  // === KUZOV (orqa — chap taraf) ===
  // Kuzov ramkasi
  ctx.fillStyle='#5a5a5a';
  ctx.beginPath(); ctx.roundRect(-W2, -vd.height*1.35, vd.width*0.62, vd.height*1.35, [4,4,0,0]); ctx.fill();
  // Kuzov ichki bo'sh qism
  ctx.fillStyle='#777';
  ctx.beginPath(); ctx.roundRect(-W2+3, -vd.height*1.3, vd.width*0.58, vd.height*1.05, 3); ctx.fill();
  // Yuk yog'ochlari (taxtalar)
  ctx.strokeStyle='#8B6914'; ctx.lineWidth=2;
  for (let i=0;i<4;i++) {
    let lx = -W2 + 8 + i * (vd.width*0.58/4);
    ctx.beginPath(); ctx.moveTo(lx, -vd.height*1.28); ctx.lineTo(lx, -vd.height*0.08); ctx.stroke();
  }
  // Kuzov ustki chiziq
  ctx.fillStyle='#444';
  ctx.fillRect(-W2, -vd.height*1.38, vd.width*0.62, 5);

  // === KAB (old — o'ng taraf) ===
  // Kab asosiy tanasi
  ctx.fillStyle=vd.color2;
  ctx.beginPath(); ctx.roundRect(W2*0.18, -vd.height, W2*0.88, vd.height, [10,10,0,0]); ctx.fill();
  // Kab yuqori qismi (tom)
  ctx.fillStyle=vd.color;
  ctx.beginPath(); ctx.roundRect(W2*0.22, -vd.height*1.55, W2*0.82, vd.height*0.6, [8,8,0,0]); ctx.fill();
  // Oyna — old
  ctx.fillStyle='rgba(160,220,255,0.82)';
  ctx.beginPath(); ctx.roundRect(W2*0.55, -vd.height*1.5, W2*0.44, vd.height*0.5, [4,4,0,0]); ctx.fill();
  // Oyna — yon
  ctx.fillStyle='rgba(160,220,255,0.65)';
  ctx.beginPath(); ctx.roundRect(W2*0.24, -vd.height*1.48, W2*0.28, vd.height*0.45, [3,3,0,0]); ctx.fill();
  // Oyna ramkasi
  ctx.strokeStyle='rgba(0,0,0,0.25)'; ctx.lineWidth=1.5;
  ctx.strokeRect(W2*0.55, -vd.height*1.5, W2*0.44, vd.height*0.5);
  // Eshik chiziq
  ctx.strokeStyle='rgba(0,0,0,0.2)'; ctx.lineWidth=1.2;
  ctx.beginPath();
  ctx.moveTo(W2*0.42, -vd.height*0.05);
  ctx.lineTo(W2*0.42, -vd.height*0.92);
  ctx.stroke();
  // Bampfer
  ctx.fillStyle='#333';
  ctx.beginPath(); ctx.roundRect(W2*0.9, -vd.height*0.28, W2*0.18, vd.height*0.28, [3,3,3,3]); ctx.fill();
  // Old chiroq
  ctx.fillStyle='#FFF176'; ctx.shadowColor='#FFFF00'; ctx.shadowBlur=18;
  ctx.beginPath(); ctx.arc(W2*0.98, -vd.height*0.62, 7, 0, Math.PI*2); ctx.fill();
  ctx.shadowBlur=0;
  // Orqa chiroq
  ctx.fillStyle='#FF3322'; ctx.shadowColor='#FF0000'; ctx.shadowBlur=10;
  ctx.beginPath(); ctx.arc(-W2+5, -vd.height*0.55, 5, 0, Math.PI*2); ctx.fill();
  ctx.shadowBlur=0;
  // Chiqindi truba
  ctx.fillStyle='#555';
  ctx.beginPath(); ctx.roundRect(W2*0.28, -vd.height*1.65, 6, 12, 2); ctx.fill();
  ctx.fillStyle='rgba(200,200,200,0.3)';
  ctx.beginPath(); ctx.arc(W2*0.31, -vd.height*1.66, 5, 0, Math.PI*2); ctx.fill();

  // === G'ILDIRAKLAR ===
  // Orqa g'ildiraklarini juft qilamiz (truck orqa juft g'ildirak)
  drawWheel(-W2*0.52, 0, vd.wheelR, car.w1rot, vd.tireW, vd.color);
  drawWheel(-W2*0.28, 0, vd.wheelR*0.9, car.w1rot, vd.tireW*0.85, vd.color2);
  // Old g'ildirak
  drawWheel( W2*0.62, 0, vd.wheelR, car.w2rot, vd.tireW, vd.color);
}

function drawMonster(vd) {
  const W2 = vd.width/2;
  // body
  ctx.fillStyle=vd.color2;
  ctx.beginPath(); ctx.roundRect(-W2*0.8, -vd.height, vd.width*0.9, vd.height, 8); ctx.fill();
  // flames
  if (frameCount%4<2) {
    ctx.fillStyle='rgba(255,100,0,0.7)'; ctx.shadowColor='#FF4500'; ctx.shadowBlur=14;
    ctx.beginPath(); ctx.ellipse(-W2*0.85, -vd.height*0.5, 8, 14, -0.3, 0, Math.PI*2); ctx.fill();
    ctx.beginPath(); ctx.ellipse(-W2*0.9, -vd.height*0.7, 5, 10, 0.2, 0, Math.PI*2); ctx.fill();
    ctx.shadowBlur=0;
  }
  // cab
  ctx.fillStyle=vd.color;
  ctx.beginPath(); ctx.roundRect(-W2*0.5, -vd.height-28, vd.width*0.65, 30, [10,10,0,0]); ctx.fill();
  // window
  ctx.fillStyle='rgba(100,220,255,0.7)';
  ctx.beginPath(); ctx.roundRect(-W2*0.42, -vd.height-24, vd.width*0.5, 20, 4); ctx.fill();
  // headlight
  ctx.fillStyle='#FFE100'; ctx.shadowColor='#FFE100'; ctx.shadowBlur=20;
  ctx.beginPath(); ctx.arc(W2*0.55,-vd.height*0.4,8,0,Math.PI*2); ctx.fill(); ctx.shadowBlur=0;
  // spiky bumper
  ctx.fillStyle='#444';
  for (let i=-2;i<=2;i++) {
    ctx.beginPath(); ctx.moveTo(W2+i*8,-vd.height*0.5); ctx.lineTo(W2+8+i*8,-vd.height*0.6); ctx.lineTo(W2+i*8,-vd.height*0.7); ctx.fill();
  }
  // BIG wheels
  drawWheel(-W2*0.55, 0, vd.wheelR, car.w1rot, vd.tireW, vd.color);
  drawWheel( W2*0.55, 0, vd.wheelR, car.w2rot, vd.tireW, vd.color);
}

function drawWheel(ox, oy, r, rot, tw, color) {
  ctx.save();
  ctx.translate(ox, oy+r-2);

  // outer tire
  ctx.beginPath(); ctx.arc(0,0,r,0,Math.PI*2);
  ctx.fillStyle='#1a1a1a'; ctx.fill();
  ctx.strokeStyle='#111'; ctx.lineWidth=tw*0.4; ctx.stroke();

  // tread
  ctx.strokeStyle='#333'; ctx.lineWidth=2;
  for (let i=0;i<8;i++) {
    ctx.save();
    ctx.rotate(rot + i*Math.PI/4);
    ctx.beginPath(); ctx.moveTo(0, r-3); ctx.lineTo(0, r+2);
    ctx.stroke(); ctx.restore();
  }
  // inner rim
  ctx.beginPath(); ctx.arc(0,0,r*0.58,0,Math.PI*2);
  ctx.fillStyle='#bbb'; ctx.fill();
  ctx.strokeStyle='#999'; ctx.lineWidth=1.5; ctx.stroke();
  // spokes
  ctx.strokeStyle='#888'; ctx.lineWidth=2;
  for (let i=0;i<5;i++) {
    ctx.save(); ctx.rotate(rot+i*Math.PI*2/5);
    ctx.beginPath(); ctx.moveTo(0,0); ctx.lineTo(0,r*0.56); ctx.stroke();
    ctx.restore();
  }
  // center hub
  ctx.beginPath(); ctx.arc(0,0,r*0.18,0,Math.PI*2);
  ctx.fillStyle=color; ctx.fill();
  ctx.restore();
}

function drawSpeedometer() {
  const speed = Math.abs(car ? car.vel : 0);
  const maxSpd = VEHICLES[selectedVehicle].power * 6;
  const cx = W - 56, cy = H - 56, rad = 44;

  ctx.save();
  ctx.translate(cx, cy);

  // bg circle
  ctx.beginPath(); ctx.arc(0,0,rad,0,Math.PI*2);
  ctx.fillStyle='rgba(0,0,0,0.5)'; ctx.fill();
  ctx.strokeStyle='rgba(255,255,255,0.15)'; ctx.lineWidth=3; ctx.stroke();

  // arc track
  ctx.beginPath(); ctx.arc(0,0,rad-5, Math.PI*0.65, Math.PI*2.35);
  ctx.strokeStyle='rgba(255,255,255,0.12)'; ctx.lineWidth=7; ctx.stroke();

  // speed arc
  const pct = Math.min(speed/maxSpd,1);
  const startA = Math.PI*0.65, endA = Math.PI*0.65 + pct*Math.PI*1.7;
  const spGrad = ctx.createLinearGradient(-rad,0,rad,0);
  spGrad.addColorStop(0,'#06D6A0');
  spGrad.addColorStop(0.6,'#FFD93D');
  spGrad.addColorStop(1,'#EF233C');
  ctx.beginPath(); ctx.arc(0,0,rad-5,startA,endA);
  ctx.strokeStyle=spGrad; ctx.lineWidth=7; ctx.lineCap='round'; ctx.stroke();

  // speed number
  ctx.fillStyle='#fff';
  ctx.font=`bold 14px Nunito`;
  ctx.textAlign='center'; ctx.textBaseline='middle';
  ctx.fillText(Math.round(speed*15),0,2);

  // unit
  ctx.fillStyle='rgba(255,255,255,0.5)';
  ctx.font=`bold 8px Nunito`;
  ctx.fillText('km/h',0,16);

  ctx.restore();
}

// ==================== UPDATE HUD ====================
function updateHUD() {
  document.getElementById('hudDist').textContent = score.dist + ' m';
  document.getElementById('hudCoins').textContent = score.coins;
  const ms = gameTimer;
  const sec = Math.floor(ms/1000)%60;
  const min = Math.floor(ms/60000);
  document.getElementById('hudSpeed').textContent = Math.abs(Math.round((car?.vel||0)*15));

  const pct = fuel;
  const fb = document.getElementById('fuelBar');
  fb.style.width = pct + '%';
  if (pct < 20) fb.style.background = 'linear-gradient(90deg,#EF233C,#FF6B35)';
  else if (pct < 50) fb.style.background = 'linear-gradient(90deg,#FFD93D,#FF9500)';
  else fb.style.background = 'linear-gradient(90deg,#06D6A0,#00F5A0)';
}

// ==================== MAIN LOOP ====================
function gameLoop() {
  requestAnimationFrame(gameLoop);
  frameCount++;

  if (gameState !== 'playing') {
    if (gameState === 'menu') {
      // animate menu background
      drawMenuBg();
    }
    return;
  }

  updateCar();
  updateParticles();
  updateHUD();

  ctx.clearRect(0,0,W,H);
  drawBackground();
  drawTerrain();
  drawCoins();

  // draw particles behind car
  ctx.save();
  for (let p of particles) {
    ctx.save();
    ctx.globalAlpha = p.life * 0.8;
    if (p.type==='exhaust') ctx.fillStyle='rgba(200,200,200,0.7)';
    else if (p.type==='dirt') ctx.fillStyle = STAGES[selectedStage].particles;
    else ctx.fillStyle='#FF6B35';
    ctx.beginPath(); ctx.arc(p.x, p.y, p.r, 0, Math.PI*2); ctx.fill();
    ctx.restore();
  }
  ctx.restore();

  drawVehicle();
  drawSpeedometer();
}

function drawMenuBg() {
  ctx.clearRect(0,0,W,H);
  const stg = STAGES['country'];
  const grad = ctx.createLinearGradient(0,0,0,H);
  grad.addColorStop(0,'#1a1a2e'); grad.addColorStop(1,'#16213e');
  ctx.fillStyle=grad; ctx.fillRect(0,0,W,H);
  // floating stars
  ctx.fillStyle='rgba(255,255,255,0.6)';
  for (let i=0;i<50;i++) {
    let sx=(i*137.5+frameCount*0.3)%W;
    let sy=(i*91.3)%(H*0.7);
    let sr=Math.sin(frameCount*0.05+i)*0.4+0.8;
    ctx.beginPath(); ctx.arc(sx,sy,sr,0,Math.PI*2); ctx.fill();
  }
}

// ==================== SCREENS ====================
function showMenu() {
  gameState = 'menu';
  document.getElementById('mainMenu').classList.remove('hidden');
  document.getElementById('pauseScreen').classList.add('hidden');
  document.getElementById('gameOver').classList.add('hidden');
  document.getElementById('hud').classList.add('hidden');
  document.getElementById('fuelWrap').classList.add('hidden');
  document.getElementById('controls').style.display = 'none';
  document.getElementById('pauseBtn').style.display = 'none';
}

function startGame() {
  score = { dist:0, coins:0, time:0 };
  fuel = 100;
  frameCount = 0;
  startTime = Date.now();
  gameTimer = 0;
  particles = [];
  buildTerrain();
  spawnCoins();
  initCar();
  cameraX = 0;
  gameState = 'playing';

  document.getElementById('mainMenu').classList.add('hidden');
  document.getElementById('pauseScreen').classList.add('hidden');
  document.getElementById('gameOver').classList.add('hidden');
  document.getElementById('hud').classList.remove('hidden');
  document.getElementById('fuelWrap').classList.remove('hidden');
  document.getElementById('controls').style.display = 'flex';
  document.getElementById('pauseBtn').style.display = 'flex';
}

function pauseGame() {
  if (gameState !== 'playing') return;
  gameState = 'paused';
  document.getElementById('pauseScreen').classList.remove('hidden');
  const ms = Date.now() - startTime;
  const sec = Math.floor(ms/1000)%60;
  const min = Math.floor(ms/60000);
  document.getElementById('pauseStats').innerHTML = `
    <div class="go-stat"><div class="gs-icon">📍</div><span class="gs-val">${score.dist}</span><span class="gs-label">Masofa</span></div>
    <div class="go-stat"><div class="gs-icon">🪙</div><span class="gs-val">${score.coins}</span><span class="gs-label">Tangalar</span></div>
    <div class="go-stat"><div class="gs-icon">⏱️</div><span class="gs-val">${min}:${String(sec).padStart(2,'0')}</span><span class="gs-label">Vaqt</span></div>
  `;
}

function resumeGame() {
  startTime = Date.now() - gameTimer;
  gameState = 'playing';
  document.getElementById('pauseScreen').classList.add('hidden');
}

function showGameOver(reason) {
  gameState = 'gameover';
  const best = Math.max(score.dist, parseInt(localStorage.getItem('hcr_best')||'0'));
  localStorage.setItem('hcr_best', best);
  score.best = best;

  const ms = gameTimer;
  const sec = Math.floor(ms/1000)%60;
  const min = Math.floor(ms/60000);

  document.getElementById('goTitle').textContent = reason==='flip' ? '💥 AG\'DARILDI!' : '💥 O\'YIN TUGADI!';
  document.getElementById('goDistVal').textContent = score.dist;
  document.getElementById('goCoinVal').textContent = score.coins;
  document.getElementById('goTimeVal').textContent = min+':'+String(sec).padStart(2,'0');
  document.getElementById('goBestVal').textContent = best;
  document.getElementById('gameOver').classList.remove('hidden');
}

// ==================== EVENTS ====================
document.getElementById('playBtn').addEventListener('click', startGame);
document.getElementById('resumeBtn').addEventListener('click', resumeGame);
document.getElementById('retryBtn').addEventListener('click', startGame);
document.getElementById('pauseHomeBtn').addEventListener('click', () => { document.getElementById('pauseScreen').classList.add('hidden'); showMenu(); });
document.getElementById('goHomeBtn').addEventListener('click', () => { document.getElementById('gameOver').classList.add('hidden'); showMenu(); });
document.getElementById('pauseBtn').addEventListener('click', pauseGame);

// Vehicle select
document.querySelectorAll('.vehicle-card').forEach(card => {
  card.addEventListener('click', () => {
    document.querySelectorAll('.vehicle-card').forEach(c=>c.classList.remove('selected'));
    card.classList.add('selected');
    selectedVehicle = card.dataset.v;
  });
});

// Stage select
document.querySelectorAll('.stage-card').forEach(card => {
  card.addEventListener('click', () => {
    document.querySelectorAll('.stage-card').forEach(c=>c.classList.remove('selected'));
    card.classList.add('selected');
    selectedStage = card.dataset.s;
  });
});

// Keyboard
document.addEventListener('keydown', e => {
  keys[e.code] = true;
  if (e.code==='Escape' || e.code==='KeyP') {
    if (gameState==='playing') pauseGame();
    else if (gameState==='paused') resumeGame();
  }
});
document.addEventListener('keyup', e => { keys[e.code] = false; });

// Touch buttons
const btnGas = document.getElementById('btnGas');
const btnBrake = document.getElementById('btnBrake');

function addHold(btn, setFn) {
  btn.addEventListener('pointerdown', e => { e.preventDefault(); setFn(true); });
  btn.addEventListener('pointerup', e => { e.preventDefault(); setFn(false); });
  btn.addEventListener('pointercancel', e => { setFn(false); });
  btn.addEventListener('pointerleave', e => { setFn(false); });
}
addHold(btnGas, v => gasBtn=v);
addHold(btnBrake, v => brakeBtn=v);

// ==================== INIT ====================
showMenu();
gameLoop();
</script>
</body>
</html>

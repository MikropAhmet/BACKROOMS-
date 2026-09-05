<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Backrooms</title>
<style>
  * { margin:0; padding:0; box-sizing:border-box; }
  html, body {
    width:100%; height:100%;
    overflow:hidden;
    background:#020202;
    font-family: 'Courier New', Courier, monospace;
    color:#d9d2a3;
  }
  #gameCanvas { display:block; width:100%; height:100%; }

  .overlay {
    position:fixed; inset:0;
    display:flex; flex-direction:column;
    align-items:center; justify-content:center;
    text-align:center;
    background: radial-gradient(ellipse at center, rgba(10,9,6,0.72), rgba(2,2,1,0.94));
    z-index:20;
    overflow-y:auto;
    padding:24px 16px;
  }
  .hidden { display:none !important; }

  .overlay h1 {
    font-size: 46px;
    letter-spacing: 6px;
    color:#c7c05a;
    text-shadow: 0 0 18px rgba(199,192,90,0.5), 0 0 40px rgba(150,140,20,0.3);
    margin-bottom: 6px;
  }
  .overlay .flicker { animation: flicker 3.5s infinite; }
  @keyframes flicker {
    0%,19%,21%,23%,80%,100% { opacity:1; }
    20%,22%,79% { opacity:0.55; }
  }
  .overlay p { font-size: 14px; color:#a89e6b; margin: 4px 0; line-height:1.6; }
  .overlay .rule { width:220px; height:1px; background:linear-gradient(90deg,transparent,#7a7340,transparent); margin:14px 0; }
  .overlay .keys { display:flex; gap:22px; margin: 14px 0 18px; flex-wrap:wrap; justify-content:center; }
  .overlay .key-item { font-size:12px; color:#9c936a; }
  .overlay .key-item b { display:block; color:#e4dc8f; font-size:15px; border:1px solid #55502c; border-radius:4px; padding:4px 10px; margin-bottom:4px; background:rgba(255,255,255,0.03); }
  button.startBtn {
    background:#5a5522; color:#f1ecc3; border:1px solid #a49a4c;
    padding:14px 34px; font-size:15px; letter-spacing:2px; cursor:pointer;
    font-family:inherit; margin-top:6px; transition: all 0.2s;
    text-transform:uppercase;
  }
  button.startBtn:hover { background:#7a7330; box-shadow:0 0 20px rgba(180,170,80,0.4); }

  /* ---- Ana menü (glitch başlık + sade liste, referans görsele göre) ---- */
  #startOverlay.overlay { justify-content:flex-start; align-items:stretch; padding:0; background:radial-gradient(ellipse at center, rgba(6,5,3,0.35), rgba(2,2,1,0.72)); }
  #menuHome {
    width:100%; height:100%; align-self:stretch;
    display:flex; flex-direction:column; justify-content:space-between; align-items:flex-start;
    text-align:left; padding:7vh 7vw;
  }
  .glitchTitle {
    font-size:clamp(40px,6vw,64px); letter-spacing:4px; font-weight:bold; color:#fff;
    text-shadow: 2px 0 rgba(255,40,40,0.75), -2px 0 rgba(50,255,110,0.75), 0 0 22px rgba(255,255,255,0.35);
    margin-bottom:4px;
  }
  .menuSub { font-size:13px; color:#c9c29a; letter-spacing:1px; margin-bottom:0; text-shadow:0 1px 4px rgba(0,0,0,0.8); }
  .menuList { display:flex; flex-direction:column; gap:14px; margin:0; width:auto; }
  .menuItem {
    background:none; border:none; color:#fff;
    padding:2px 0; font-family:inherit; font-size:22px; letter-spacing:0.5px;
    cursor:pointer; text-align:left; text-transform:none;
    display:flex; align-items:center; gap:2px;
    text-shadow:0 2px 6px rgba(0,0,0,0.85);
    transition: color .15s;
  }
  .menuItem:hover, .menuItem:focus-visible { color:#fff; outline:none; }
  .menuItem .cursor-arrow { opacity:0; color:#fff; transition:opacity .12s; }
  .menuItem:hover .cursor-arrow, .menuItem:focus-visible .cursor-arrow { opacity:1; }
  .menuItem span.diffval { color:#f2c85b; text-transform:none; letter-spacing:0.5px; }
  .menu-keys-row { display:flex; gap:22px; flex-wrap:wrap; }
  .menu-hint { opacity:0.65; margin-top:4px; font-size:11px; color:#c9c29a; }

  /* Diğer panellerin (bölümler/zorluk/vb) merkezi klasik overlay hizası kalsın */
  #menuLevelsPanel, #menuDifficultyPanel, #menuMultiplayerPanel, #menuOptionsPanel {
    max-width:640px; margin:0 auto; align-self:center;
  }
  button.startBtn {
    background:#5a5522; color:#f1ecc3; border:1px solid #a49a4c;
    padding:14px 34px; font-size:15px; letter-spacing:2px; cursor:pointer;
    font-family:inherit; margin-top:6px; transition: all 0.2s;
    text-transform:uppercase;
  }
  button.startBtn:hover { background:#7a7330; box-shadow:0 0 20px rgba(180,170,80,0.4); }
  .backBtn { margin-top:16px; background:rgba(255,255,255,0.04); border:1px solid #55502c; color:#e4dc8f; padding:11px 20px; font-family:inherit; font-size:14px; cursor:pointer; border-radius:3px; }
  .backBtn:hover { background:#5a5522; }
  .panelTitle {
    font-size:22px; letter-spacing:4px; color:#c7c05a;
    text-shadow:0 0 14px rgba(199,192,90,0.4); margin-bottom:14px;
  }

  .levelGrid {
    display:grid; grid-template-columns:repeat(3, 1fr); gap:10px;
    max-width:520px; margin:6px auto 6px;
  }
  .levelBtn {
    position:relative; background:rgba(255,255,255,0.04); border:1px solid #55502c;
    color:#e4dc8f; padding:14px 8px; font-family:inherit; cursor:pointer;
    border-radius:4px; display:flex; flex-direction:column; align-items:center; gap:5px;
    transition:all .2s; min-width:135px; min-height:70px; justify-content:center;
  }
  .levelBtn:hover:not(:disabled) { background:#5a5522; box-shadow:0 0 14px rgba(200,190,90,.3); }
  .levelBtn:disabled, .levelBtn.locked { opacity:0.38; cursor:not-allowed; color:#8a8158; }
  .levelBtn.completed { border-color:#8fae3f; }
  .levelIdx { font-size:10px; letter-spacing:2px; opacity:0.75; }
  #roomNameInput {
    font-family:'Courier New',monospace; font-size:15px; padding:10px 14px; text-align:center;
    width:220px; background:rgba(255,255,255,0.06); border:1px solid #55502c; color:#e4dc8f;
    border-radius:3px; letter-spacing:1px;
  }
  #roomNameInput:focus { outline:none; border-color:var(--sign,#c7c05a); }
  #mpStatus { min-height:18px; color:#f2c85b; font-size:13px; margin:4px 0 6px; }
  #volumeSlider { width:220px; accent-color:#c7c05a; }
  .levelName { font-size:13px; font-weight:bold; }
  .levelCheck { position:absolute; top:4px; right:6px; color:#9fe870; font-size:12px; }

  .diffRow { display:flex; flex-direction:column; align-items:center; gap:8px; margin:10px 0 6px; }
  .diffButtons { display:flex; gap:8px; }
  .diffBtn {
    background:rgba(255,255,255,0.05); color:#c9c08a; border:1px solid #55502c;
    padding:10px 24px; font-family:inherit; font-size:14px; letter-spacing:1px;
    cursor:pointer; border-radius:3px; text-transform:uppercase; transition:all .2s;
  }
  .diffBtn:hover { background:#5a5522; }
  .diffBtn.active { background:#7a7330; color:#f5efc4; border-color:#c9c05a; box-shadow:0 0 12px rgba(200,190,90,.4); }

  #hud {
    position:fixed; inset:0; pointer-events:none; z-index:10;
  }
  #levelLabel {
    position:absolute; top:18px; left:18px;
    font-size:11px; letter-spacing:2px; color:#9c936a;
    background:rgba(10,9,4,0.5); padding:5px 10px; border-radius:3px;
    border:1px solid rgba(180,170,90,0.25);
  }
  #coconutCounter {
    position:absolute; top:18px; left:50%; transform:translateX(-50%);
    background: rgba(10,9,4,0.55);
    border:1px solid rgba(180,170,90,0.3);
    padding:8px 22px;
    border-radius:3px;
    font-size:20px;
    letter-spacing:2px;
    color:#e8e0a0;
    text-shadow:0 0 8px rgba(200,190,90,0.4);
  }
  #coconutCounter span.num { color:#f2c85b; font-weight:bold; }

  #staminaWrap {
    position:absolute; left:18px; bottom:22px;
    width:180px;
  }
  #staminaLabel {
    font-size:10px; letter-spacing:2px; color:#9c936a; margin-bottom:4px;
  }
  #staminaBarBg {
    width:100%; height:10px; background:rgba(255,255,255,0.08);
    border:1px solid rgba(180,170,90,0.35); border-radius:2px; overflow:hidden;
  }
  #staminaBarFill {
    height:100%; width:100%;
    background: linear-gradient(90deg,#8fae3f,#c7d95a);
    transition: width 0.08s linear, background 0.3s;
  }
  #staminaBarFill.low { background: linear-gradient(90deg,#7a1f1f,#c0392b); }

  #healthWrap {
    position:absolute; left:18px; bottom:60px;
    width:180px;
  }
  #healthLabel {
    font-size:10px; letter-spacing:2px; color:#9c936a; margin-bottom:4px;
  }
  #healthBarBg {
    width:100%; height:10px; background:rgba(255,255,255,0.08);
    border:1px solid rgba(180,90,90,0.35); border-radius:2px; overflow:hidden;
  }
  #healthBarFill {
    height:100%; width:100%;
    background: linear-gradient(90deg,#7a1f1f,#e0473a);
    transition: width 0.15s linear;
  }
  #healthBarFill.low { animation: healthPulse 0.7s infinite; }
  @keyframes healthPulse { 0%,100%{opacity:1;} 50%{opacity:0.35;} }

  #flashHint {
    position:absolute; right:18px; bottom:22px;
    font-size:10px; letter-spacing:1px; color:#8a8158;
  }

  #crosshair {
    position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);
    width:6px; height:6px; border-radius:50%;
    background:rgba(230,220,150,0.55);
  }

  #dangerVignette {
    position:fixed; inset:0; pointer-events:none; z-index:9;
    box-shadow: inset 0 0 0 0 rgba(150,10,10,0);
    background: radial-gradient(ellipse at center, rgba(120,0,0,0) 40%, rgba(120,0,0,0) 100%);
    opacity:0;
    transition: opacity 0.15s;
  }

  #staminaVignette {
    position:fixed; inset:0; pointer-events:none; z-index:9;
    background: radial-gradient(ellipse at center, rgba(0,0,0,0) 15%, rgba(0,0,0,0.9) 100%);
    opacity:0;
  }

  #hitFlash {
    position:fixed; inset:0; pointer-events:none; z-index:9;
    background:#ff1111; opacity:0;
  }

  #warningToast {
    position:absolute; top:66px; left:50%; transform:translateX(-50%);
    font-size:13px; letter-spacing:3px; color:#ff5a5a;
    text-shadow:0 0 12px rgba(255,60,60,0.7);
    opacity:0; transition:opacity 0.4s;
  }

  #scanlines {
    position:fixed; inset:0; pointer-events:none; z-index:8;
    background: repeating-linear-gradient(0deg, rgba(0,0,0,0.12) 0px, rgba(0,0,0,0.12) 1px, transparent 2px, transparent 3px);
    opacity:0.35;
    mix-blend-mode: multiply;
  }

  #pauseOverlay p { cursor:pointer; }

  #resultOverlay h1.caught { color:#c0392b; text-shadow:0 0 22px rgba(200,30,30,0.6); }
  #resultOverlay h1.won { color:#9fe870; text-shadow:0 0 22px rgba(140,230,90,0.5); }
  .btnRow { display:flex; gap:14px; margin-top:10px; flex-wrap:wrap; justify-content:center; }
</style>
</head>
<body>

<canvas id="gameCanvas"></canvas>

<div id="hud" class="hidden">
  <div id="levelLabel">BÖLÜM 0 · LOBİ</div>
  <div id="coconutCounter"><span id="coconutIcon">🥥</span> <span class="num" id="coconutNum">0</span><span id="coconutTargetWrap"> / 8</span></div>
  <div id="healthWrap">
    <div id="healthLabel">CAN</div>
    <div id="healthBarBg"><div id="healthBarFill"></div></div>
  </div>
  <div id="staminaWrap">
    <div id="staminaLabel">STAMINA</div>
    <div id="staminaBarBg"><div id="staminaBarFill"></div></div>
  </div>
  <div id="flashHint">[F] FENER</div>
  <div id="crosshair"></div>
  <div id="warningToast"></div>
</div>

<div id="dangerVignette"></div>
<div id="staminaVignette"></div>
<div id="hitFlash"></div>
<div id="scanlines"></div>

<div class="overlay" id="startOverlay">

  <div id="menuHome">
    <div>
      <h1 class="glitchTitle flicker">BACKROOMS</h1>
      <p class="menuSub">Yalnız değilsin — Kaptan Clark da burada, seni arıyor.</p>
    </div>
    <div>
      <div class="menuList">
        <button class="menuItem" id="menuStartBtn">Başlat<span class="cursor-arrow">&lt;</span></button>
        <button class="menuItem" id="menuSurvivalBtn">Hayatta Kalma Modu<span class="cursor-arrow">&lt;</span></button>
        <button class="menuItem" id="menuLevelsBtn">Bölümler<span class="cursor-arrow">&lt;</span></button>
        <button class="menuItem" id="menuDiffBtn">Zorluk: <span class="diffval" id="menuDiffLabel">Orta</span><span class="cursor-arrow">&lt;</span></button>
        <button class="menuItem" id="menuMultiplayerBtn">Çok Oyunculu<span class="cursor-arrow">&lt;</span></button>
        <button class="menuItem" id="menuOptionsBtn">Ayarlar<span class="cursor-arrow">&lt;</span></button>
        <button class="menuItem" id="menuQuitBtn">Çıkış<span class="cursor-arrow">&lt;</span></button>
      </div>
      <div class="menu-keys-row" style="margin-top:22px;">
        <div class="key-item"><b>WASD</b>Hareket</div>
        <div class="key-item"><b>SHIFT</b>Koş</div>
        <div class="key-item"><b>F</b>Fener</div>
        <div class="key-item"><b>FARE</b>Bak (kilit yoksa sürükle)</div>
      </div>
      <p class="menu-hint">(Fare kilidi gerektirdiği için masaüstünde oynanması önerilir)</p>
    </div>
  </div>

  <div id="menuLevelsPanel" class="hidden">
    <h2 class="panelTitle">BÖLÜMLER</h2>
    <div class="levelGrid" id="levelGrid"></div>
    <button class="backBtn" id="levelsBackBtn">‹ Geri</button>
  </div>

  <div id="menuDifficultyPanel" class="hidden">
    <h2 class="panelTitle">ZORLUK</h2>
    <div class="diffRow">
      <div class="diffButtons">
        <button class="diffBtn" data-diff="easy">Kolay</button>
        <button class="diffBtn" data-diff="medium">Orta</button>
        <button class="diffBtn" data-diff="hard">Zor</button>
      </div>
    </div>
    <button class="backBtn" id="diffBackBtn">‹ Geri</button>
  </div>

  <div id="menuOptionsPanel" class="hidden">
    <h2 class="panelTitle">AYARLAR</h2>
    <div style="margin:10px 0 26px; display:flex; flex-direction:column; align-items:center; gap:8px;">
      <label style="font-size:12px; color:#9c936a; letter-spacing:2px;">SES SEVİYESİ</label>
      <input type="range" id="volumeSlider" min="0" max="100" value="85">
    </div>
    <button class="menuItem" id="fullscreenBtn" style="justify-content:center; margin-bottom:10px;">Tam Ekran Aç / Kapat</button>
    <button class="backBtn" id="optionsBackBtn">‹ Geri</button>
  </div>

  <div id="menuMultiplayerPanel" class="hidden">
    <h2 class="panelTitle">ÇOK OYUNCULU</h2>
    <p style="max-width:360px; margin:0 auto 16px;">Bir oda adı yaz. Aynı adı yazan iki kişi eşleşir — bilgisayarınızı sunucu olarak kullanmayan, ücretsiz ortak bir eşleştirme servisi üzerinden doğrudan bağlanırsınız.</p>
    <input type="text" id="roomNameInput" placeholder="oda adı" maxlength="20" autocomplete="off">
    <div class="diffButtons" style="margin:16px 0 6px;">
      <button class="diffBtn" id="createRoomBtn">Oda Oluştur</button>
      <button class="diffBtn" id="joinRoomBtn">Odaya Katıl</button>
    </div>
    <p id="mpStatus"></p>
    <div id="mpHostControls" class="hidden">
      <button class="menuItem" id="mpHostStartBtn" style="justify-content:center; margin-bottom:10px;">Oyunu Başlat</button>
    </div>
    <button class="backBtn" id="mpBackBtn">‹ Geri</button>
  </div>

</div>

<div class="overlay hidden" id="pauseOverlay">
  <h1 style="font-size:28px;">DURAKLATILDI</h1>
  <p id="resumeText">Devam etmek için tıkla</p>
</div>

<div class="overlay hidden" id="resultOverlay">
  <h1 id="resultTitle"></h1>
  <p id="resultText"></p>
  <div class="btnRow">
    <button class="startBtn" id="retryBtn">Tekrar Dene</button>
    <button class="startBtn" id="menuBtn">Ana Menüye Dön</button>
  </div>
</div>

<div class="overlay hidden" id="achievementOverlay">
  <div style="font-size:44px; margin-bottom:8px;">🏆</div>
  <p style="font-size:11px; letter-spacing:3px; color:#8a8158;">BAŞARIM KAZANILDI</p>
  <h1 style="font-size:26px; color:#e8d15a; text-shadow:0 0 16px rgba(230,210,90,0.5); margin:6px 0 14px;">(HİLE Mİ?)</h1>
  <p style="max-width:360px;">Bitirme kapısına dosdoğru, hiç dolanmadan ulaştın. Kaptan Clark bile şaşırdı. Normal yoldan mı, kısayoldan mı — o senin bileceğin iş.</p>
  <button class="startBtn" id="achievementContinueBtn">Devam Et</button>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/peerjs/1.5.4/peerjs.min.js" onerror="this.onerror=null;this.src='https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js';"></script>
<script>
/* ============================= CONFIG ============================= */
const CELL = 4;
const WALL_H = 3.2;
const MAZE_W = 19, MAZE_H = 19; // must be odd
const PLAYER_RADIUS = 0.4;
const MONSTER_RADIUS = 0.4;
const PLAYER_EYE_H = 1.65;
const WALK_SPEED = 3.2;
const SPRINT_SPEED = 6.0;
const STAMINA_MAX = 100;
const STAMINA_DRAIN = 30;
const STAMINA_REGEN = 16;
const STAMINA_SPRINT_UNLOCK = 20;
const DETECT_RADIUS = 14;
const LOSE_RADIUS = 20;
const MONSTER_SPEED_CHASE = 3.35;
const MONSTER_SPEED_WANDER = 1.5;
const CATCH_RADIUS = 1.0;
const PICKUP_RADIUS = 1.3;
const TOTAL_COCONUTS = 8;

/* Can / hasar sistemi */
const PLAYER_MAX_HEALTH = 100;
const DAMAGE_PER_HIT = 25;
const HIT_INVULN_MS = 1100;

/* Zorluk modları */
const DIFFICULTY_PRESETS = {
  easy:   { label:'Kolay', speedMul:0.78, detectMul:0.80, loseMul:0.85, staminaDrainMul:0.75, staminaRegenMul:1.25 },
  medium: { label:'Orta',  speedMul:1.00, detectMul:1.00, loseMul:1.00, staminaDrainMul:1.00, staminaRegenMul:1.00 },
  hard:   { label:'Zor',   speedMul:1.28, detectMul:1.30, loseMul:1.15, staminaDrainMul:1.25, staminaRegenMul:0.80 },
};
let difficultyKey = 'medium';

/* Bölümler */
const LEVEL_NAMES = [
  'Lobi',
  'Yaşanabilir Bölge',
  'Boru Rüyaları',
  'Elektrik İstasyonu',
  'Terk Edilmiş Ofis',
  'Terör Oteli',
  'Işıklar Söndü',
];
const LEVEL_COUNT = LEVEL_NAMES.length;

const PROGRESS_KEY = 'backrooms_progress_v2';
let progress = { unlocked: [0], completed: [], difficulty: 'medium' };
function loadProgress() {
  try {
    const raw = localStorage.getItem(PROGRESS_KEY);
    if (raw) {
      const p = JSON.parse(raw);
      if (p && Array.isArray(p.unlocked)) progress = p;
    }
  } catch (e) { /* localStorage kullanılamıyor, bellek-içi ilerleme ile devam edilir */ }
}
function saveProgress() {
  try { localStorage.setItem(PROGRESS_KEY, JSON.stringify(progress)); } catch (e) {}
}
loadProgress();
if (progress.difficulty && DIFFICULTY_PRESETS[progress.difficulty]) difficultyKey = progress.difficulty;
let currentLevel = 0;

/* ============================= AUDIO ============================= */
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
const masterGain = audioCtx.createGain();
masterGain.gain.value = 0.85;

/* Boş koridor yankısı: algoritmik impulse response ile convolver reverb */
function createImpulseResponse(duration, decay) {
  const sampleRate = audioCtx.sampleRate;
  const length = Math.floor(sampleRate * duration);
  const impulse = audioCtx.createBuffer(2, length, sampleRate);
  for (let ch = 0; ch < 2; ch++) {
    const data = impulse.getChannelData(ch);
    for (let i = 0; i < length; i++) {
      data[i] = (Math.random()*2-1) * Math.pow(1 - i/length, decay);
    }
  }
  return impulse;
}
const reverbConvolver = audioCtx.createConvolver();
reverbConvolver.buffer = createImpulseResponse(2.4, 2.6);
const reverbDelay = audioCtx.createDelay(1.0);
reverbDelay.delayTime.value = 0.045;

const dryGain = audioCtx.createGain();
dryGain.gain.value = 0.72;
const wetGain = audioCtx.createGain();
wetGain.gain.value = 0.62;

masterGain.connect(dryGain).connect(audioCtx.destination);
masterGain.connect(reverbDelay).connect(reverbConvolver).connect(wetGain).connect(audioCtx.destination);

let droneNodes = null;
function startAmbientDrone() {
  if (droneNodes) return;
  const o1 = audioCtx.createOscillator();
  const o2 = audioCtx.createOscillator();
  const g = audioCtx.createGain();
  o1.type = 'sine'; o1.frequency.value = 48;
  o2.type = 'sine'; o2.frequency.value = 51.5;
  g.gain.value = 0.05;
  const lfo = audioCtx.createOscillator();
  const lfoGain = audioCtx.createGain();
  lfo.frequency.value = 0.08;
  lfoGain.gain.value = 0.02;
  lfo.connect(lfoGain).connect(g.gain);
  o1.connect(g); o2.connect(g); g.connect(masterGain);
  o1.start(); o2.start(); lfo.start();
  droneNodes = { o1, o2, lfo, g };
}

function playThump(volume=0.6, freq=80) {
  const t = audioCtx.currentTime;
  const osc = audioCtx.createOscillator();
  const g = audioCtx.createGain();
  osc.type = 'sine';
  osc.frequency.setValueAtTime(freq, t);
  osc.fre
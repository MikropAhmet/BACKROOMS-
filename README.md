<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Backrooms — Ücretsiz Tarayıcı Korku Oyunu (7 Bölüm)</title>
<meta name="description" content="Backrooms temalı ücretsiz 3D korku oyunu. Sarı koridorlarda 7 bölüm, örümcek canavar Kaptan Clark, can ve stamina sistemi. Kurulum gerekmez, tarayıcında oyna veya indir.">
<meta name="keywords" content="backrooms oyunu, backrooms game, tarayıcı korku oyunu, ücretsiz korku oyunu, 3d horror game, html5 oyun">
<meta name="robots" content="index, follow">
<meta name="author" content="Ahmet Yüksel Çınar">

<meta property="og:type" content="website">
<meta property="og:title" content="Backrooms — Ücretsiz Tarayıcı Korku Oyunu">
<meta property="og:description" content="Sarı koridorlarda 7 bölüm, örümcek canavar Kaptan Clark, can ve stamina sistemi. Kurulum gerekmez, tarayıcında oyna.">
<meta property="og:locale" content="tr_TR">

<meta name="twitter:card" content="summary">
<meta name="twitter:title" content="Backrooms — Ücretsiz Tarayıcı Korku Oyunu">
<meta name="twitter:description" content="Sarı koridorlarda 7 bölüm, örümcek canavar Kaptan Clark. Tarayıcında ücretsiz oyna.">
<link rel="icon" href="data:,">
<style>
  :root{
    --void:#0b0904;
    --wall:#c3b78d;
    --carpet:#301a16;
    --sign:#c7c05a;
    --sign-bright:#f2c85b;
    --danger:#c0392b;
    --ink:#d9d2a3;
    --ink-dim:#9c936a;
    --mono: 'Courier New', Courier, monospace;
    --sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  }
  * { margin:0; padding:0; box-sizing:border-box; }
  html { scroll-behavior:smooth; }
  body{
    background:var(--void); color:var(--ink); font-family:var(--sans);
    line-height:1.6;
  }
  a { color:inherit; }
  img,video{max-width:100%;display:block;}

  /* ---------------- HERO / KORİDOR ---------------- */
  .hero{
    position:relative; height:100svh; min-height:560px;
    display:flex; flex-direction:column; align-items:center; justify-content:center;
    overflow:hidden; text-align:center; padding:24px;
  }
  .corridor{ position:absolute; inset:0; }
  .frame{
    position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);
    border:2px solid rgba(199,192,90,0.16);
    background:rgba(195,183,141,0.03);
  }
  .f1{ width:96%; height:82%; border-color:rgba(199,192,90,0.12); }
  .f2{ width:74%; height:62%; border-color:rgba(199,192,90,0.20); }
  .f3{ width:50%; height:42%; border-color:rgba(199,192,90,0.30); }
  .f4{ width:26%; height:22%; border-color:rgba(199,192,90,0.42); }
  .f5{
    width:8%; height:7%; background:rgba(255,240,205,0.22);
    border-color:rgba(255,240,210,0.65);
    box-shadow:0 0 70px 26px rgba(255,225,170,0.18);
    animation: pulse 4s ease-in-out infinite;
  }
  @keyframes pulse{ 0%,100%{opacity:1;} 50%{opacity:0.55;} }
  .fogtop{ position:absolute; inset:0; background:radial-gradient(ellipse at center, transparent 8%, var(--void) 78%); }
  .scan{
    position:absolute; inset:0; pointer-events:none; opacity:0.25; mix-blend-mode:multiply;
    background:repeating-linear-gradient(0deg, rgba(0,0,0,0.35) 0px, rgba(0,0,0,0.35) 1px, transparent 2px, transparent 3px);
  }

  .hero-content{ position:relative; z-index:2; max-width:720px; }
  .glitch{
    font-family:var(--mono); font-weight:bold; letter-spacing:6px;
    font-size:clamp(42px, 9vw, 92px); color:#fff; line-height:1;
    text-shadow: 3px 0 rgba(255,40,40,0.7), -3px 0 rgba(50,255,110,0.7), 0 0 30px rgba(255,255,255,0.3);
  }
  .tagline{
    font-family:var(--mono); color:var(--sign); letter-spacing:2px; font-size:15px;
    margin-top:14px;
  }
  .sub{
    color:var(--ink-dim); font-size:15px; max-width:480px; margin:16px auto 0;
  }
  .hero-ctas{ display:flex; gap:14px; justify-content:center; margin-top:34px; flex-wrap:wrap; }
  .btn{
    font-family:var(--mono); letter-spacing:2px; text-transform:uppercase; font-size:14px;
    padding:15px 30px; border-radius:2px; cursor:pointer; text-decoration:none;
    display:inline-block; transition:transform .15s, box-shadow .2s, background .2s;
    border:1px solid var(--sign);
  }
  .btn-primary{ background:var(--sign); color:#231f0c; }
  .btn-primary:hover{ background:var(--sign-bright); box-shadow:0 0 26px rgba(242,200,91,0.45); transform:translateY(-1px); }
  .btn-ghost{ background:transparent; color:var(--sign); border-color:rgba(199,192,90,0.4); }
  .btn-ghost:hover{ border-color:var(--sign); background:rgba(199,192,90,0.08); }
  .scroll-hint{
    position:absolute; bottom:26px; left:50%; transform:translateX(-50%);
    font-family:var(--mono); font-size:11px; color:var(--ink-dim); letter-spacing:3px;
    animation: bob 2.2s ease-in-out infinite;
  }
  @keyframes bob{ 0%,100%{ transform:translate(-50%,0); opacity:0.6;} 50%{ transform:translate(-50%,6px); opacity:1;} }

  /* ---------------- OYNA (iframe) ---------------- */
  section{ padding:90px 20px; max-width:1000px; margin:0 auto; }
  .eyebrow-free-title{
    font-family:var(--mono); font-size:clamp(24px,3.4vw,32px); color:var(--ink); letter-spacing:1px;
    margin-bottom:10px;
  }
  .section-note{ color:var(--ink-dim); font-size:14px; max-width:560px; margin-bottom:30px; }

  .player-frame{
    position:relative; width:100%; aspect-ratio:16/9; background:#000;
    border:1px solid rgba(199,192,90,0.25); border-radius:2px; overflow:hidden;
    box-shadow:0 30px 80px rgba(0,0,0,0.55);
  }
  .player-frame iframe{ width:100%; height:100%; border:0; display:block; }
  .player-toolbar{
    display:flex; justify-content:space-between; align-items:center;
    margin-top:12px; font-family:var(--mono); font-size:12px; color:var(--ink-dim);
  }
  .fs-btn{
    font-family:var(--mono); font-size:12px; letter-spacing:1px; color:var(--sign);
    background:none; border:1px solid rgba(199,192,90,0.35); padding:7px 14px;
    border-radius:2px; cursor:pointer;
  }
  .fs-btn:hover{ background:rgba(199,192,90,0.1); }

  /* ---------------- TESİS DİZİNİ (özellikler) ---------------- */
  .directory{ border-top:1px solid rgba(199,192,90,0.18); }
  .dir-row{
    display:grid; grid-template-columns:220px 1fr; gap:24px;
    padding:26px 0; border-bottom:1px solid rgba(199,192,90,0.18);
    align-items:baseline;
  }
  .dir-plate{
    font-family:var(--mono); font-size:14px; letter-spacing:2px; color:var(--sign-bright);
    text-transform:uppercase;
  }
  .dir-desc{ color:var(--ink); font-size:15px; }
  .dir-desc b{ color:var(--sign); font-weight:normal; }

  /* ---------------- TALİMAT NOTU (nasıl oynanır) ---------------- */
  .note-wrap{ display:flex; justify-content:center; padding:20px 0 40px; }
  .taped-note{
    position:relative; background:#efe6c2; color:#241f10; padding:34px 38px;
    max-width:420px; transform:rotate(-1.2deg);
    box-shadow:0 18px 40px rgba(0,0,0,0.5);
    font-family:var(--mono);
  }
  .taped-note::before, .taped-note::after{
    content:''; position:absolute; width:70px; height:22px;
    background:rgba(255,255,255,0.35); border:1px solid rgba(255,255,255,0.5);
    top:-12px; transform:rotate(-6deg);
  }
  .taped-note::before{ left:-18px; }
  .taped-note::after{ right:-18px; transform:rotate(7deg); }
  .note-title{ font-size:13px; letter-spacing:2px; color:#5a4e1f; margin-bottom:16px; }
  .note-row{ display:flex; gap:14px; padding:8px 0; border-top:1px dashed rgba(90,78,31,0.35); font-size:14px; }
  .note-row:first-of-type{ border-top:none; }
  .note-key{ font-weight:bold; min-width:64px; }

  /* ---------------- ALT BİLGİ ---------------- */
  footer{
    border-top:1px solid rgba(199,192,90,0.18); padding:50px 20px 40px;
    text-align:center;
  }
  .footer-download{ margin-bottom:22px; }
  .credits{ font-family:var(--mono); font-size:12px; color:var(--ink-dim); letter-spacing:1px; }
  .credits b{ color:var(--sign); font-weight:normal; }
  .fine{ margin-top:10px; font-size:11px; color:#5c5638; max-width:460px; margin-left:auto; margin-right:auto; }

  @media (max-width: 620px){
    .dir-row{ grid-template-columns:1fr; gap:6px; }
  }
</style>
</head>
<body>

<section class="hero">
  <div class="corridor">
    <div class="fogtop"></div>
    <div class="frame f1"></div>
    <div class="frame f2"></div>
    <div class="frame f3"></div>
    <div class="frame f4"></div>
    <div class="frame f5"></div>
    <div class="scan"></div>
  </div>
  <div class="hero-content">
    <div class="glitch">BACKROOMS</div>
    <div class="tagline">Sarı koridorlar. Sonsuz halı kokusu. Ve yalnız değilsin.</div>
    <p class="sub">Yedi bölüm, sekiz hindistan cevizi, bir de karanlıkta dolaşan Kaptan Clark. Fenerini yak ve kaybol.</p>
    <div class="hero-ctas">
      <a href="#oyna" class="btn btn-primary">Oyna</a>
      <a href="backrooms.html" download class="btn btn-ghost">İndir (.html)</a>
    </div>
  </div>
  <div class="scroll-hint">AŞAĞI KAYDIR</div>
</section>

<section id="oyna">
  <h2 class="eyebrow-free-title">Doğrudan burada oyna</h2>
  <p class="section-note">İndirmene gerek yok — oyun bu sayfanın içinde, tarayıcında çalışıyor. Sesi açık oynamanı öneririz.</p>
  <div class="player-frame">
    <iframe id="gameFrame" src="backrooms.html" allow="fullscreen; pointer-lock; autoplay" allowfullscreen></iframe>
  </div>
  <div class="player-toolbar">
    <span>Fare kilidi masaüstünde en iyi çalışır.</span>
    <button class="fs-btn" id="fsBtn">Tam Ekran</button>
  </div>
</section>

<section class="directory">
  <h2 class="eyebrow-free-title">Tesis dizini</h2>
  <p class="section-note">Bu binada yedi alan var. Hepsi kilitli başlar, ilki hariç.</p>

  <div class="dir-row">
    <div class="dir-plate">Bölüm 0–6</div>
    <div class="dir-desc">Lobi'den <b>Işıklar Söndü</b>'ye kadar yedi ayrı bölüm — her biri kendi rastgele üretilmiş labirentinde geçiyor. Bir bölümü bitirmek bir sonrakinin kilidini açar.</div>
  </div>
  <div class="dir-row">
    <div class="dir-plate">Kaptan Clark</div>
    <div class="dir-desc">Karanlıkta gezinen örümcek biçimli bir varlık. Kendi ışığı yok — onu görmek istiyorsan fenerini üzerine tutman gerekecek.</div>
  </div>
  <div class="dir-row">
    <div class="dir-plate">Can &amp; Stamina</div>
    <div class="dir-desc">Her darbe canının <b>%25'ini</b> götürür. Koşmak stamina yakar; stamina biterse nefesin kesilir, ekranın kararır.</div>
  </div>
  <div class="dir-row">
    <div class="dir-plate">Zorluk</div>
    <div class="dir-desc">Kolay, Orta, Zor. Canavarın hızını, seni fark etme mesafesini ve stamina tüketimini değiştirir.</div>
  </div>
  <div class="dir-row">
    <div class="dir-plate">8 Hindistan Cevizi</div>
    <div class="dir-desc">Her bölümde sekiz tane var. Hepsini topla, bölüm biter.</div>
  </div>
</section>

<div class="note-wrap">
  <div class="taped-note">
    <div class="note-title">— DUVARA TUTTURULMUŞ TALİMAT —</div>
    <div class="note-row"><span class="note-key">WASD</span><span>Hareket et</span></div>
    <div class="note-row"><span class="note-key">SHIFT</span><span>Koş (stamina harcar)</span></div>
    <div class="note-row"><span class="note-key">F</span><span>Feneri aç / kapat</span></div>
    <div class="note-row"><span class="note-key">FARE</span><span>Etrafına bak</span></div>
  </div>
</div>

<footer>
  <div class="footer-download">
    <a href="backrooms.html" download class="btn btn-primary">Oyunu İndir</a>
  </div>
  <div class="credits">Yapımcı ve Tasarımcı: <b>Ahmet Yüksel Çınar</b></div>
  <p class="fine">Tek bir HTML dosyası, sunucu gerektirmez. Bu sayfa ve oyun dosyasını (backrooms.html) aynı klasörde tutarsan indirme ve oynatma bağlantıları çalışmaya devam eder.</p>
</footer>

<script>
  document.getElementById('fsBtn').addEventListener('click', () => {
    const frame = document.getElementById('gameFrame');
    if (frame.requestFullscreen) frame.requestFullscreen();
    else if (frame.webkitRequestFullscreen) frame.webkitRequestFullscreen();
  });
</script>
</body>
</html>
  

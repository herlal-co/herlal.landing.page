<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Wanderlust — Coming Soon</title>
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=Telegraf:wght@400&family=DM+Sans:wght@300;400&display=swap" rel="stylesheet" />
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --sand:    #f0e8d8;
      --clay:    #c4a882;
      --deep:    #0d1b2a;
      --ocean:   #1b4965;
      --horizon: #62b6cb;
      --gold:    #d4a84b;
      --white:   #faf8f4;
    }

    html, body {
      height: 100%;
      overflow: hidden;
    }

    body {
      font-family: 'DM Sans', sans-serif;
      background: var(--deep);
      color: var(--sand);
      cursor: none;
    }

    /* ── Custom cursor ── */
    .cursor {
      position: fixed; top: 0; left: 0;
      width: 12px; height: 12px;
      background: var(--gold);
      border-radius: 50%;
      pointer-events: none;
      transform: translate(-50%, -50%);
      transition: transform .12s ease, width .2s ease, height .2s ease, opacity .2s ease;
      z-index: 9999;
      mix-blend-mode: difference;
    }
    .cursor-ring {
      position: fixed; top: 0; left: 0;
      width: 40px; height: 40px;
      border: 1px solid rgba(212,168,75,.45);
      border-radius: 50%;
      pointer-events: none;
      transform: translate(-50%, -50%);
      transition: transform .35s ease, width .3s ease, height .3s ease, opacity .3s ease;
      z-index: 9998;
    }
    body:hover .cursor { opacity: 1; }

    /* ── Canvas background ── */
    #bg-canvas {
      position: fixed; inset: 0;
      width: 100%; height: 100%;
      z-index: 0;
    }

    /* ── Grain overlay ── */
    .grain {
      position: fixed; inset: -50%;
      width: 200%; height: 200%;
      background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.04'/%3E%3C/svg%3E");
      pointer-events: none;
      z-index: 1;
      opacity: .55;
      animation: grain-shift 8s steps(2) infinite;
    }
    @keyframes grain-shift {
      0%  { transform: translate(0,0); }
      25% { transform: translate(-2%,-2%); }
      50% { transform: translate(2%,-1%); }
      75% { transform: translate(-1%,2%); }
    }

    /* ── Layout ── */
    .stage {
      position: relative; z-index: 2;
      width: 100vw; height: 100vh;
      display: grid;
      grid-template-rows: auto 1fr auto;
      padding: 2.5rem 3.5rem;
      overflow: hidden;
    }

    /* ── Header ── */
    header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      opacity: 0;
      animation: fade-up .8s .3s forwards;
    }
    .logo {
      font-family: 'Cormorant Garamond', serif;
      font-size: 1.35rem;
      font-weight: 300;
      letter-spacing: .3em;
      text-transform: uppercase;
      color: var(--clay);
    }
    .logo span { color: var(--gold); }
    nav { display: flex; gap: 2rem; align-items: center; }
    nav a {
      font-size: .72rem;
      letter-spacing: .18em;
      text-transform: uppercase;
      color: var(--sand);
      text-decoration: none;
      opacity: .6;
      transition: opacity .3s;
    }
    nav a:hover { opacity: 1; }
    .nav-cta {
      border: 1px solid rgba(212,168,75,.45);
      padding: .45rem 1.2rem;
      border-radius: 2rem;
      opacity: .85 !important;
      color: var(--gold) !important;
      transition: background .3s, opacity .3s !important;
    }
    .nav-cta:hover { background: rgba(212,168,75,.12); }

    /* ── Nav popup cards ── */
    .nav-item {
      position: relative;
    }
    .nav-popup {
      position: absolute;
      top: calc(100% + 1.2rem);
      left: 50%;
      transform: translateX(-50%) translateY(-10px);
      width: 224px;
      background: rgba(10, 20, 32, 0.75);
      border: 1px solid rgba(196,168,130,.18);
      border-radius: 14px;
      padding: 1.4rem 1.5rem;
      backdrop-filter: blur(28px) saturate(1.5);
      -webkit-backdrop-filter: blur(28px) saturate(1.5);
      pointer-events: none;
      opacity: 0;
      transition: opacity .38s cubic-bezier(.16,1,.3,1),
                  transform .38s cubic-bezier(.16,1,.3,1);
      z-index: 100;
      box-shadow: 0 28px 64px rgba(0,0,0,.5), 0 0 0 1px rgba(255,255,255,.04) inset;
    }
    .nav-popup::before {
      content: '';
      position: absolute;
      top: -5px;
      left: 50%;
      transform: translateX(-50%) rotate(45deg);
      width: 9px; height: 9px;
      background: rgba(10,20,32,.9);
      border-left: 1px solid rgba(196,168,130,.18);
      border-top: 1px solid rgba(196,168,130,.18);
    }
    .nav-item.open .nav-popup {
      opacity: 1;
      transform: translateX(-50%) translateY(0);
      pointer-events: auto;
    }
    .nav-item.open > a { opacity: 1; }

    /* ── Dim backdrop ── */
    .nav-backdrop {
      position: fixed; inset: 0;
      background: rgba(5, 12, 20, 0.6);
      backdrop-filter: blur(3px);
      -webkit-backdrop-filter: blur(3px);
      z-index: 50;
      opacity: 0;
      pointer-events: none;
      transition: opacity .38s ease;
    }
    .nav-backdrop.visible {
      opacity: 1;
      pointer-events: auto;
    }

    /* ── Email highlight pulse ── */
    @keyframes field-pulse {
      0%   { box-shadow: 0 0 0 0px rgba(212,168,75,0);   border-color: rgba(212,168,75,.5); }
      45%  { box-shadow: 0 0 0 7px rgba(212,168,75,.22); border-color: rgba(212,168,75,1);  }
      100% { box-shadow: 0 0 0 14px rgba(212,168,75,0);  border-color: rgba(212,168,75,.6); }
    }
    .notify-form input.highlight {
      border-color: rgba(212,168,75,.9) !important;
      animation: field-pulse .75s ease forwards;
    }
    .nav-popup-icon {
      font-size: 1.45rem;
      margin-bottom: .85rem;
      display: block;
      line-height: 1;
    }
    .nav-popup-label {
      font-size: .57rem;
      letter-spacing: .3em;
      text-transform: uppercase;
      color: var(--gold);
      margin-bottom: .5rem;
      display: flex;
      align-items: center;
      gap: .5rem;
    }
    .nav-popup-label::after {
      content: '';
      flex: 1;
      height: 1px;
      background: rgba(212,168,75,.22);
    }
    .nav-popup-title {
      font-family: 'Cormorant Garamond', serif;
      font-size: 1.15rem;
      font-weight: 300;
      color: var(--white);
      line-height: 1.25;
      margin-bottom: .55rem;
    }
    .nav-popup-body {
      font-size: .71rem;
      font-weight: 300;
      color: rgba(240,232,216,.42);
      line-height: 1.7;
    }
    .nav-popup-divider {
      height: 1px;
      background: rgba(255,255,255,.06);
      margin: .9rem 0;
    }
    .nav-popup-status {
      display: flex;
      align-items: center;
      gap: .5rem;
      font-size: .6rem;
      letter-spacing: .14em;
      text-transform: uppercase;
      color: rgba(98,182,203,.65);
    }
    .nav-popup-dot {
      width: 5px; height: 5px;
      border-radius: 50%;
      background: var(--horizon);
      animation: pulse-dot 2s infinite;
      flex-shrink: 0;
    }

    /* ── Hero ── */
    .hero {
      display: flex;
      flex-direction: column;
      justify-content: center;
      position: relative;
    }

    .hero-tag {
      font-size: .68rem;
      letter-spacing: .35em;
      text-transform: uppercase;
      color: var(--horizon);
      margin-bottom: 1.6rem;
      opacity: 0;
      animation: fade-up .7s .6s forwards;
      display: flex;
      align-items: center;
      gap: .8rem;
    }
    .hero-tag::before {
      content: '';
      width: 2.5rem; height: 1px;
      background: var(--horizon);
    }

    h1 {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: clamp(4rem, 9vw, 9rem);
      line-height: .92;
      letter-spacing: -.01em;
      max-width: 14ch;
      opacity: 0;
      animation: fade-up .9s .8s forwards;
    }
    h1 em {
      font-style: italic;
      color: var(--clay);
      display: block;
    }
    h1 strong {
      font-weight: 600;
      color: var(--gold);
    }

    .tagline {
      margin-top: 2.2rem;
      font-size: clamp(.85rem, 1.2vw, 1rem);
      font-weight: 300;
      color: rgba(240,232,216,.55);
      max-width: 38ch;
      line-height: 1.7;
      opacity: 0;
      animation: fade-up .7s 1.1s forwards;
    }

    /* ── Countdown ── */
    .countdown-wrap {
      margin-top: 3.5rem;
      display: flex;
      gap: 2.5rem;
      opacity: 0;
      animation: fade-up .7s 1.3s forwards;
    }
    .tick {
      text-align: center;
    }
    .tick-num {
      font-family: 'Cormorant Garamond', serif;
      font-size: clamp(2.2rem, 4vw, 3.8rem);
      font-weight: 300;
      line-height: 1;
      color: var(--white);
      position: relative;
    }
    .tick-num::after {
      content: '';
      position: absolute;
      bottom: -4px; left: 0; right: 0;
      height: 1px;
      background: linear-gradient(90deg, transparent, var(--gold), transparent);
    }
    .tick-label {
      font-size: .6rem;
      letter-spacing: .25em;
      text-transform: uppercase;
      color: rgba(240,232,216,.4);
      margin-top: .5rem;
    }
    .tick-sep {
      font-family: 'Cormorant Garamond', serif;
      font-size: 2.5rem;
      color: rgba(212,168,75,.3);
      align-self: flex-start;
      padding-top: .3rem;
    }

    /* ── Email form ── */
    .notify-wrap {
      margin-top: 3rem;
      opacity: 0;
      animation: fade-up .7s 1.5s forwards;
    }
    .notify-label {
      font-size: .68rem;
      letter-spacing: .25em;
      text-transform: uppercase;
      color: rgba(240,232,216,.4);
      margin-bottom: .8rem;
    }
    .notify-form {
      display: flex;
      gap: 0;
      max-width: 440px;
      position: relative;
    }
    .notify-form input {
      flex: 1;
      background: rgba(255,255,255,.05);
      border: 1px solid rgba(196,168,130,.25);
      border-right: none;
      border-radius: 3px 0 0 3px;
      padding: .85rem 1.2rem;
      color: var(--sand);
      font-family: 'DM Sans', sans-serif;
      font-size: .85rem;
      outline: none;
      transition: border-color .3s, background .3s;
    }
    .notify-form input::placeholder { color: rgba(240,232,216,.28); }
    .notify-form input:focus {
      border-color: rgba(212,168,75,.5);
      background: rgba(255,255,255,.08);
    }
    .notify-form button {
      background: var(--gold);
      border: none;
      border-radius: 0 3px 3px 0;
      padding: .85rem 1.6rem;
      font-family: 'DM Sans', sans-serif;
      font-size: .72rem;
      font-weight: 400;
      letter-spacing: .15em;
      text-transform: uppercase;
      color: var(--deep);
      cursor: none;
      transition: background .3s, transform .15s;
      white-space: nowrap;
    }
    .notify-form button:hover { background: #e0b55c; }
    .notify-form button:active { transform: scale(.97); }
    .success-msg {
      display: none;
      margin-top: .7rem;
      font-size: .78rem;
      color: var(--horizon);
      letter-spacing: .05em;
    }

    /* ── Decorative coords ── */
    .coords {
      position: absolute;
      right: 0;
      top: 50%;
      transform: translateY(-50%) rotate(90deg);
      transform-origin: center center;
      font-size: .62rem;
      letter-spacing: .3em;
      color: rgba(98,182,203,.3);
      text-transform: uppercase;
      white-space: nowrap;
      opacity: 0;
      animation: fade-in 1s 2s forwards;
    }

    /* ── Floating destination pills ── */
    .destinations {
      position: absolute;
      right: 5rem;
      top: 50%;
      transform: translateY(-50%);
      display: flex;
      flex-direction: column;
      gap: .7rem;
      opacity: 0;
      animation: fade-left .8s 1.8s forwards;
    }
    .dest-pill {
      display: flex;
      align-items: center;
      gap: .7rem;
      background: rgba(27,73,101,.35);
      border: 1px solid rgba(98,182,203,.15);
      border-radius: 2rem;
      padding: .5rem 1rem .5rem .6rem;
      backdrop-filter: blur(12px);
      cursor: none;
      transition: background .3s, border-color .3s, transform .3s;
    }
    .dest-pill:hover {
      background: rgba(27,73,101,.6);
      border-color: rgba(98,182,203,.4);
      transform: translateX(-4px);
    }
    .dest-flag {
      font-size: 1.2rem;
      line-height: 1;
    }
    .dest-info { display: flex; flex-direction: column; }
    .dest-name {
      font-size: .78rem;
      font-weight: 400;
      color: var(--sand);
      letter-spacing: .04em;
    }
    .dest-region {
      font-size: .62rem;
      color: rgba(240,232,216,.4);
      letter-spacing: .1em;
      text-transform: uppercase;
    }
    .dest-dot {
      width: 6px; height: 6px;
      border-radius: 50%;
      background: var(--horizon);
      margin-left: auto;
      animation: pulse-dot 2s infinite;
    }
    @keyframes pulse-dot {
      0%, 100% { opacity: 1; transform: scale(1); }
      50% { opacity: .4; transform: scale(.7); }
    }

    /* ── Footer ── */
    footer {
      display: flex;
      justify-content: space-between;
      align-items: flex-end;
      opacity: 0;
      animation: fade-up .6s 2s forwards;
    }
    .footer-copy {
      font-size: .65rem;
      letter-spacing: .12em;
      color: rgba(240,232,216,.25);
      text-transform: uppercase;
    }
    .socials { display: flex; gap: 1.2rem; }
    .socials a {
      font-size: .65rem;
      letter-spacing: .18em;
      text-transform: uppercase;
      color: rgba(240,232,216,.3);
      text-decoration: none;
      transition: color .3s;
    }
    .socials a:hover { color: var(--clay); }

    .progress-line {
      position: absolute;
      bottom: 0; left: 0;
      height: 1px;
      background: linear-gradient(90deg, var(--ocean), var(--gold), var(--horizon));
      animation: grow-line 2.5s 2.2s ease forwards;
      width: 0;
    }
    @keyframes grow-line {
      to { width: 100%; }
    }

    /* ── Keyframes ── */
    @keyframes fade-up {
      from { opacity: 0; transform: translateY(22px); }
      to   { opacity: 1; transform: translateY(0); }
    }
    @keyframes fade-in {
      from { opacity: 0; }
      to   { opacity: 1; }
    }
    @keyframes fade-left {
      from { opacity: 0; transform: translateY(-50%) translateX(20px); }
      to   { opacity: 1; transform: translateY(-50%) translateX(0); }
    }

    /* ── Responsive ── */
    @media (max-width: 768px) {
      .stage { padding: 1.8rem; }
      nav .hide-mobile { display: none; }
      .destinations { display: none; }
      .coords { display: none; }
      h1 { font-size: clamp(3rem, 12vw, 5rem); }
      .countdown-wrap { gap: 1.5rem; }
      .tick-sep { display: none; }
    }
  </style>
</head>
<body>

<div class="cursor" id="cursor"></div>
<div class="cursor-ring" id="cursor-ring"></div>
<div class="grain"></div>

<canvas id="bg-canvas"></canvas>

<div class="nav-backdrop" id="nav-backdrop"></div>
<div class="stage">
  <!-- Header -->
  <header>
    <div class="logo">Wander<span>lust</span></div>
    <nav>
      <div class="nav-item hide-mobile">
        <a href="#">About</a>
        <div class="nav-popup">
          <span class="nav-popup-icon">✦</span>
          <div class="nav-popup-label">Our Story</div>
          <div class="nav-popup-title">A new kind of travel awaits</div>
          <div class="nav-popup-body">We're a team of wanderers building something the world hasn't seen yet. Every detail, crafted with care.</div>
          <div class="nav-popup-divider"></div>
          <div class="nav-popup-status"><span class="nav-popup-dot"></span>Page launching soon</div>
        </div>
      </div>
      <div class="nav-item hide-mobile">
        <a href="#">Destinations</a>
        <div class="nav-popup">
          <span class="nav-popup-icon">🌍</span>
          <div class="nav-popup-label">Explore</div>
          <div class="nav-popup-title">195 countries. Infinite stories.</div>
          <div class="nav-popup-body">Curated routes, hidden escapes, and local secrets — all in one place. The map is filling up.</div>
          <div class="nav-popup-divider"></div>
          <div class="nav-popup-status"><span class="nav-popup-dot"></span>Coming with launch</div>
        </div>
      </div>
      <a href="#" class="nav-cta">Notify Me</a>
    </nav>
  </header>

  <!-- Hero -->
  <section class="hero">
    <div class="hero-tag">Launching 2025 — Curated Travel</div>

    <h1>
      Every <em>journey</em>
      <strong>begins here.</strong>
    </h1>

    <p class="tagline">
      We're crafting the world's most immersive travel platform — bespoke itineraries, hidden gems, and stories worth telling.
    </p>

    <div class="countdown-wrap">
      <div class="tick">
        <div class="tick-num" id="days">00</div>
        <div class="tick-label">Days</div>
      </div>
      <div class="tick-sep">·</div>
      <div class="tick">
        <div class="tick-num" id="hours">00</div>
        <div class="tick-label">Hours</div>
      </div>
      <div class="tick-sep">·</div>
      <div class="tick">
        <div class="tick-num" id="mins">00</div>
        <div class="tick-label">Minutes</div>
      </div>
      <div class="tick-sep">·</div>
      <div class="tick">
        <div class="tick-num" id="secs">00</div>
        <div class="tick-label">Seconds</div>
      </div>
    </div>

    <div class="notify-wrap">
      <div class="notify-label">Be the first to explore</div>
      <div class="notify-form">
        <input type="email" id="email-input" placeholder="your@email.com" />
        <button onclick="handleNotify()">Notify Me</button>
      </div>
      <div class="success-msg" id="success-msg">✦ You're on the list — adventures await.</div>
    </div>

    <!-- Decorative -->
    <div class="coords">4°10′N 73°30′E · Wanderlust HQ · Est. 2025</div>

    <!-- Floating destinations -->
    <div class="destinations">
      <div class="dest-pill">
        <div class="dest-flag">🇮🇸</div>
        <div class="dest-info">
          <span class="dest-name">Reykjavík</span>
          <span class="dest-region">Iceland</span>
        </div>
        <div class="dest-dot"></div>
      </div>
      <div class="dest-pill">
        <div class="dest-flag">🇯🇵</div>
        <div class="dest-info">
          <span class="dest-name">Kyoto</span>
          <span class="dest-region">Japan</span>
        </div>
        <div class="dest-dot" style="animation-delay:.5s"></div>
      </div>
      <div class="dest-pill">
        <div class="dest-flag">🇲🇦</div>
        <div class="dest-info">
          <span class="dest-name">Marrakech</span>
          <span class="dest-region">Morocco</span>
        </div>
        <div class="dest-dot" style="animation-delay:1s"></div>
      </div>
      <div class="dest-pill">
        <div class="dest-flag">🇳🇿</div>
        <div class="dest-info">
          <span class="dest-name">Queenstown</span>
          <span class="dest-region">New Zealand</span>
        </div>
        <div class="dest-dot" style="animation-delay:1.5s"></div>
      </div>
    </div>
  </section>

  <!-- Footer -->
  <footer>
    <div class="footer-copy">© 2025 Wanderlust — All rights reserved</div>
    <nav class="socials">
      <a href="#">Instagram</a>
      <a href="#">Twitter</a>
      <a href="#">LinkedIn</a>
    </nav>
  </footer>

  <div class="progress-line"></div>
</div>

<script>
  /* ── Cursor ── */
  const cursor = document.getElementById('cursor');
  const ring   = document.getElementById('cursor-ring');
  document.addEventListener('mousemove', e => {
    cursor.style.left = e.clientX + 'px';
    cursor.style.top  = e.clientY + 'px';
    setTimeout(() => {
      ring.style.left = e.clientX + 'px';
      ring.style.top  = e.clientY + 'px';
    }, 80);
  });
  document.querySelectorAll('a, button, input, .dest-pill').forEach(el => {
    el.addEventListener('mouseenter', () => {
      cursor.style.width = '20px';
      cursor.style.height = '20px';
      ring.style.width = '60px';
      ring.style.height = '60px';
    });
    el.addEventListener('mouseleave', () => {
      cursor.style.width = '12px';
      cursor.style.height = '12px';
      ring.style.width = '40px';
      ring.style.height = '40px';
    });
  });

  /* ── Animated canvas background ── */
  const canvas = document.getElementById('bg-canvas');
  const ctx = canvas.getContext('2d');
  let W, H, particles = [], time = 0;

  function resize() {
    W = canvas.width  = window.innerWidth;
    H = canvas.height = window.innerHeight;
  }
  resize();
  window.addEventListener('resize', resize);

  /* Horizon gradient */
  function drawGradient() {
    const g = ctx.createLinearGradient(0, 0, 0, H);
    g.addColorStop(0,   '#0d1b2a');
    g.addColorStop(0.4, '#0d1f35');
    g.addColorStop(0.7, '#102840');
    g.addColorStop(1,   '#0a1520');
    ctx.fillStyle = g;
    ctx.fillRect(0, 0, W, H);
  }

  /* Aurora-like waves
     t = elapsed seconds.
     speed = rad/s. Full cycle = 2π / speed.
     0.55 rad/s → cycle ~11s  (gentle ocean swell)
     0.45 rad/s → cycle ~14s
     0.65 rad/s → cycle ~10s
  */
  function drawAurora(t) {
    const waves = [
      { y: H * .55, amp: 36, freq: .0018, speed: 0.55, phase: 0.0, alpha: .07, color: '27,73,101'  },
      { y: H * .61, amp: 26, freq: .0014, speed: 0.40, phase: 1.2, alpha: .05, color: '98,182,203' },
      { y: H * .67, amp: 30, freq: .0011, speed: 0.48, phase: 2.5, alpha: .06, color: '212,168,75' },
    ];
    waves.forEach(w => {
      ctx.beginPath();
      ctx.moveTo(0, H);
      for (let x = 0; x <= W; x += 4) {
        const y = w.y
          + Math.sin(x * w.freq + t * w.speed + w.phase) * w.amp
          + Math.sin(x * w.freq * 1.5 + t * w.speed * 0.6 + w.phase) * (w.amp * .28);
        ctx.lineTo(x, y);
      }
      ctx.lineTo(W, H);
      ctx.closePath();
      const grad = ctx.createLinearGradient(0, w.y - w.amp, 0, H);
      grad.addColorStop(0, `rgba(${w.color},${w.alpha})`);
      grad.addColorStop(1, `rgba(${w.color},0)`);
      ctx.fillStyle = grad;
      ctx.fill();
    });
  }

  /* Stars — twinkle speed: 1.2–2.0 rad/s → one shimmer cycle every ~3–5s */
  function initStars() {
    particles = [];
    const count = Math.floor(W * H / 8000);
    for (let i = 0; i < count; i++) {
      particles.push({
        x: Math.random() * W,
        y: Math.random() * H * .7,
        r: Math.random() * 1.2 + .2,
        a: Math.random(),
        speed: Math.random() * 0.8 + 1.2,
        phase: Math.random() * Math.PI * 2,
      });
    }
  }
  initStars();
  window.addEventListener('resize', initStars);

  function drawStars(t) {
    particles.forEach(p => {
      const alpha = .3 + .7 * Math.abs(Math.sin(p.phase + t * p.speed));
      ctx.beginPath();
      ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
      ctx.fillStyle = `rgba(240,232,216,${alpha * p.a})`;
      ctx.fill();
    });
  }

  /* Glow orbs — slow drift, cycle ~25–30s */
  function drawOrbs(t) {
    const orbs = [
      { x: W * .75, y: H * .35, r: 250, color: '27,73,101', a: .18, sp: 0.22 },
      { x: W * .15, y: H * .6,  r: 180, color: '98,182,203', a: .1,  sp: 0.17 },
      { x: W * .5,  y: H * .8,  r: 300, color: '13,27,42',  a: .4,  sp: 0.19 },
    ];
    orbs.forEach(o => {
      const x = o.x + Math.sin(t * o.sp) * 22;
      const y = o.y + Math.cos(t * o.sp * 0.7) * 14;
      const g = ctx.createRadialGradient(x, y, 0, x, y, o.r);
      g.addColorStop(0, `rgba(${o.color},${o.a})`);
      g.addColorStop(1, 'rgba(0,0,0,0)');
      ctx.fillStyle = g;
      ctx.beginPath();
      ctx.arc(x, y, o.r, 0, Math.PI * 2);
      ctx.fill();
    });
  }

  let startTs = null;
  function animate(ts) {
    if (!startTs) startTs = ts;
    const t = (ts - startTs) / 1000; // seconds elapsed
    drawGradient();
    drawOrbs(t);
    drawAurora(t);
    drawStars(t);
    requestAnimationFrame(animate);
  }
  requestAnimationFrame(animate);

  /* ── Countdown ── */
  const launch = new Date('2025-12-01T00:00:00').getTime();
  function pad(n) { return String(n).padStart(2, '0'); }
  function tick() {
    const diff = launch - Date.now();
    if (diff <= 0) {
      document.getElementById('days').textContent  = '00';
      document.getElementById('hours').textContent = '00';
      document.getElementById('mins').textContent  = '00';
      document.getElementById('secs').textContent  = '00';
      return;
    }
    document.getElementById('days').textContent  = pad(Math.floor(diff / 86400000));
    document.getElementById('hours').textContent = pad(Math.floor(diff % 86400000 / 3600000));
    document.getElementById('mins').textContent  = pad(Math.floor(diff % 3600000 / 60000));
    document.getElementById('secs').textContent  = pad(Math.floor(diff % 60000 / 1000));
  }
  tick(); setInterval(tick, 1000);

  /* ── Nav popup (click-based) ── */
  const backdrop = document.getElementById('nav-backdrop');
  const navItems = document.querySelectorAll('.nav-item');

  function closeAllPopups() {
    navItems.forEach(i => i.classList.remove('open'));
    backdrop.classList.remove('visible');
  }

  navItems.forEach(item => {
    const link = item.querySelector('a');
    link.addEventListener('click', e => {
      e.preventDefault();
      const isOpen = item.classList.contains('open');
      closeAllPopups();
      if (!isOpen) {
        item.classList.add('open');
        backdrop.classList.add('visible');
      }
    });
  });

  // Close when clicking backdrop or pressing Escape
  backdrop.addEventListener('click', closeAllPopups);
  document.addEventListener('keydown', e => { if (e.key === 'Escape') closeAllPopups(); });

  /* ── Notify Me nav button — highlight email field ── */
  document.querySelector('.nav-cta').addEventListener('click', e => {
    e.preventDefault();
    closeAllPopups();
    const input = document.getElementById('email-input');
    const isMobile = window.innerWidth <= 768;

    if (isMobile) {
      // On mobile: scroll into view smoothly, then pulse
      input.scrollIntoView({ behavior: 'smooth', block: 'center' });
      setTimeout(() => {
        input.classList.remove('highlight');
        void input.offsetWidth; // reflow to restart animation
        input.classList.add('highlight');
        input.focus();
        setTimeout(() => input.classList.remove('highlight'), 900);
      }, 600);
    } else {
      // Desktop: just pulse and focus
      input.classList.remove('highlight');
      void input.offsetWidth;
      input.classList.add('highlight');
      input.focus();
      setTimeout(() => input.classList.remove('highlight'), 900);
    }
  });

  /* ── Notify form submit ── */
  function handleNotify() {
    const val = document.getElementById('email-input').value.trim();
    const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!re.test(val)) {
      document.getElementById('email-input').style.borderColor = 'rgba(203,98,98,.6)';
      setTimeout(() => document.getElementById('email-input').style.borderColor = '', 1500);
      return;
    }
    document.querySelector('.notify-form').style.opacity = '.4';
    document.querySelector('.notify-form').style.pointerEvents = 'none';
    const msg = document.getElementById('success-msg');
    msg.style.display = 'block';
    setTimeout(() => { msg.style.opacity = '1'; }, 10);
  }

  /* Enter key for email */
  document.getElementById('email-input').addEventListener('keydown', e => {
    if (e.key === 'Enter') handleNotify();
  });

  /* Parallax on destination pills */
  document.addEventListener('mousemove', e => {
    const dx = (e.clientX / window.innerWidth  - .5) * 12;
    const dy = (e.clientY / window.innerHeight - .5) * 8;
    document.querySelector('.destinations').style.transform =
      `translateY(-50%) translate(${-dx}px,${-dy}px)`;
  });
</script>
</body>
</html>

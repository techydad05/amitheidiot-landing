<script>
  import { onMount } from 'svelte';

  onMount(() => {
    /* ================= THEME TOGGLE ================= */
    const themeBtn = document.getElementById('themeToggle');
    function applyTheme() {
      const dark = document.documentElement.classList.contains('dark');
      if (themeBtn) themeBtn.textContent = dark ? '☀️' : '🌙';
      themeBtn?.setAttribute('aria-label', dark ? 'Switch to light mode' : 'Switch to dark mode');
    }
    if (themeBtn) {
      themeBtn.addEventListener('click', () => {
        const dark = document.documentElement.classList.contains('dark');
        document.documentElement.classList.toggle('dark', !dark);
        localStorage.setItem('amitheidiot-theme', !dark ? 'dark' : 'light');
        applyTheme();
      });
    }
    applyTheme();

    /* ================= COUNTDOWN ================= */
    const LAUNCH = new Date('2026-09-17T09:00:00-04:00'); // Constitution Day, 9am ET
    function tickClock() {
      const countdown = document.getElementById('countdown');
      if (!countdown) return;
      const now = new Date();
      let diff = LAUNCH - now;
      if (diff <= 0) {
        countdown.innerHTML =
          '<div class="cd-cell" style="min-width:auto;padding:.6rem 1.2rem"><b style="font-size:1.3rem">THE TEST IS LIVE. GOOD LUCK, ἰΔΙΩΤΑ.</b></div>';
        return;
      }
      const d = Math.floor(diff / 864e5), h = Math.floor(diff / 36e5) % 24,
        m = Math.floor(diff / 6e4) % 60, s = Math.floor(diff / 1e3) % 60;
      const el = (id) => document.getElementById(id);
      el('cd-d').textContent = d;
      el('cd-h').textContent = String(h).padStart(2, '0');
      el('cd-m').textContent = String(m).padStart(2, '0');
      el('cd-s').textContent = String(s).padStart(2, '0');
    }
    tickClock();
    const clockTimer = setInterval(tickClock, 1000);

    /* ================= QUIZ CARDS ================= */
    const QUESTIONS = [
      {
        q: "What is the supreme law of the land?",
        a: "The Constitution.",
        roast: "People applying to become American memorize this. You, born into it, had to flip the card."
      },
      {
        q: "How many amendments does the Constitution have?",
        a: "Twenty-seven.",
        roast: "Not ten. The Bill of Rights is just the opening act. Sit down."
      },
      {
        q: "Name ONE right from the First Amendment.",
        a: "Speech, religion, assembly, press, petition.",
        roast: "You use one of these daily to post nonsense online. The LEAST you could do is name it."
      },
      {
        q: "What year was the Constitution ratified?",
        a: "1788.",
        roast: "Only 13% of Americans knew this. Statistically, you just confirmed the study."
      }
    ];
    const cardsEl = document.getElementById('cards');
    QUESTIONS.forEach((item, i) => {
      const el = document.createElement('div');
      el.className = 'flip';
      el.innerHTML = `
    <div class="flip-inner">
      <div class="face front">
        <div class="q-num">QUESTION ${i + 1} OF MANY</div>
        <div class="q">${item.q}</div>
        <div class="hint">Tap to reveal your failure →</div>
      </div>
      <div class="face back">
        <div class="a">${item.a}</div>
        <div class="roast">${item.roast}</div>
        <div class="verdict">
          <button class="mini-btn knew" type="button">😤 I knew it</button>
          <button class="mini-btn" type="button">💀 I had no idea</button>
        </div>
      </div>
    </div>`;
      el.addEventListener('click', () => el.classList.toggle('flipped'));
      el.querySelectorAll('.mini-btn').forEach((b) => {
        b.addEventListener('click', (e) => {
          e.stopPropagation();
          if (b.classList.contains('knew')) {
            bumpShame(-1);
            showToast("Sure you did. We'll see on the real test.");
          } else {
            bumpShame(1);
            showToast("Thank you for your honesty. The Shame-O-Meter™ thanks you.");
          }
          el.classList.remove('flipped');
        });
      });
      cardsEl.appendChild(el);
    });

    /* ================= SHAME-O-METER™ ================= */
    let shame = 74000000 + Math.floor(Math.random() * 900000);
    const shameEl = document.getElementById('shameNum');
    function renderShame() { shameEl.textContent = shame.toLocaleString('en-US'); }
    function bumpShame(n) { shame += n; renderShame(); }
    renderShame();
    // ambient drip so it feels alive
    const shameTimer = setInterval(() => { shame += Math.floor(Math.random() * 7) + 1; renderShame(); }, 2500);

    /* ================= ROAST GENERATOR ================= */
    const ROASTS = [
      "You're the reason the Founding Fathers drank.",
      "The Constitution is 4,543 words. That's shorter than your group-chat rants.",
      "Somewhere, Athena's owl just flew into a window.",
      "Your senator knows who YOU are. You don't know who your senator is.",
      "The bar is on the floor and you brought a shovel.",
      "Immigrants study for months to pass a test you'd fail in minutes. Think about it.",
      "The Greeks had a word for you: ἰδιώτης. We just made it your username.",
      "If civics knowledge were a gym membership, yours expired in middle school.",
      "27 amendments. You know zero. We counted.",
      "You've been 'registered to vote' in spirit since 2016.",
      "Democracy dies in the dark. Yours never got off the couch.",
      "A quill pen from 1787 understands government better than you do."
    ];
    const toast = document.getElementById('roastToast');
    let toastTimer;
    function showToast(msg) {
      toast.textContent = msg;
      toast.classList.add('show');
      clearTimeout(toastTimer);
      toastTimer = setTimeout(() => toast.classList.remove('show'), 4200);
    }
    document.getElementById('shameBtn').addEventListener('click', () => {
      showToast(ROASTS[Math.floor(Math.random() * ROASTS.length)]);
    });
    // click the Greek word for a bonus history lesson
    document.getElementById('greekWord').addEventListener('click', () => {
      showToast("ἰδιώτης: 'a private person.' The Athenians thought that was the saddest thing a citizen could be.");
    });

    /* ================= SIGNUP FORM ================= */
    const form = document.getElementById('signupForm');
    const msg = document.getElementById('formMsg');
    form.addEventListener('submit', (e) => {
      e.preventDefault();
      const email = document.getElementById('emailInput').value.trim();
      const valid = /^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/.test(email);
      if (!valid) {
        msg.className = 'form-msg err';
        msg.textContent = "That's not an email address. See, THIS is the problem.";
        return;
      }
      msg.className = 'form-msg ok';
      msg.textContent = "✓ Consider it done. That's more civic action than most people take all year. See you September 17 — bring shame.";
      form.reset();
    });

    /* ================= TICKER: duplicate content for seamless loop ================= */
    const track = document.getElementById('tickerTrack');
    if (track) track.innerHTML += track.innerHTML;

    return () => {
      clearInterval(clockTimer);
      clearInterval(shameTimer);
      clearTimeout(toastTimer);
    };
  });
</script>

<svelte:head>
  <title>amitheidiot.com — Are You Smarter Than An Idiot?</title>
  <meta
    name="description"
    content="The ancient Greeks had a word for people who checked out of their country. On Constitution Day, you're going to hear it."
  />
  <link rel="icon"
    href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🏛️</text></svg>" />
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link
    href="https://fonts.googleapis.com/css2?family=Anton&family=Space+Grotesk:wght@400;500;700&family=GFS+Didot&display=swap"
    rel="stylesheet" />
  <style>
    :root {
      --paper: #F1ECE0;
      --ink: #16130E;
      --aegean: #1F4FD8;
      --blood: #D8232A;
      --gold: #C9A227;
      --shadow: 8px 8px 0 var(--ink);
    }

    html.dark {
      --paper: #16130E;
      --ink: #F1ECE0;
      --aegean: #6C8CFF;
      --blood: #FF5A5F;
      --gold: #E5C158;
      --shadow: 8px 8px 0 rgba(0, 0, 0, .55);
    }

    /* THEME TOGGLE BUTTON */
    .theme-toggle {
      position: fixed;
      top: 1rem;
      right: 1rem;
      z-index: 200;
      width: 44px;
      height: 44px;
      border: 3px solid var(--ink);
      background: var(--paper);
      color: var(--ink);
      font-size: 1.15rem;
      cursor: pointer;
      box-shadow: 4px 4px 0 var(--ink);
      transition: transform .12s, box-shadow .12s, background .2s, color .2s;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .theme-toggle:hover {
      transform: translate(2px, 2px);
      box-shadow: 2px 2px 0 var(--ink);
    }
    .theme-toggle:active {
      transform: translate(4px, 4px);
      box-shadow: 0 0 0 var(--ink);
    }

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box
    }

    html {
      scroll-behavior: smooth
    }

    body {
      background: var(--paper);
      color: var(--ink);
      font-family: 'Space Grotesk', sans-serif;
      overflow-x: hidden;
      background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='200' height='200'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2'/%3E%3CfeColorMatrix values='0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0.04 0'/%3E%3C/filter%3E%3Crect width='200' height='200' filter='url(%23n)'/%3E%3C/svg%3E");
    }

    .display {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      letter-spacing: .01em
    }

    .greek {
      font-family: 'GFS Didot', serif
    }

    /* ============ TICKER ============ */
    .ticker {
      background: var(--ink);
      color: var(--paper);
      border-bottom: 3px solid var(--ink);
      overflow: hidden;
      white-space: nowrap;
      font-weight: 700;
      font-size: .85rem;
      letter-spacing: .12em;
      text-transform: uppercase;
    }

    .ticker-track {
      display: inline-block;
      padding: .6rem 0;
      animation: tick 28s linear infinite
    }

    .ticker span {
      margin: 0 1.6rem
    }

    .ticker .red {
      color: #FF5A5F
    }

    @keyframes tick {
      from { transform: translateX(0) }
      to { transform: translateX(-50%) }
    }

    /* ============ LAYOUT HELPERS ============ */
    .wrap {
      max-width: 1150px;
      margin: 0 auto;
      padding: 0 1.25rem
    }

    .kicker {
      display: inline-block;
      font-size: .78rem;
      font-weight: 700;
      letter-spacing: .22em;
      text-transform: uppercase;
      background: var(--ink);
      color: var(--paper);
      padding: .35rem .8rem;
      transform: rotate(-1deg);
    }

    /* ============ HERO ============ */
    .hero {
      padding: 4.5rem 0 3.5rem;
      position: relative
    }

    .hero .greek-word {
      font-family: 'GFS Didot', serif;
      font-size: clamp(4rem, 14vw, 10.5rem);
      line-height: .95;
      color: var(--aegean);
      user-select: none;
      cursor: default;
    }

    .hero .pronounce {
      font-size: 1rem;
      font-weight: 500;
      letter-spacing: .15em;
      color: var(--blood);
      margin-top: .4rem
    }

    .hero h1 {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: clamp(2.6rem, 7.5vw, 6rem);
      line-height: .98;
      margin: 2.2rem 0 1.4rem;
    }

    .hero h1 .outline {
      color: transparent;
      -webkit-text-stroke: 2.5px var(--ink);
    }

    .hero h1 .red {
      color: var(--blood)
    }

    .hero .lede {
      max-width: 640px;
      font-size: 1.15rem;
      line-height: 1.55;
      font-weight: 500
    }

    .hero .lede strong {
      background: linear-gradient(transparent 60%, var(--gold) 60%)
    }

    .stamp {
      position: absolute;
      top: 3rem;
      right: 2rem;
      width: 130px;
      height: 130px;
      border: 3px solid var(--blood);
      border-radius: 50%;
      color: var(--blood);
      display: flex;
      align-items: center;
      justify-content: center;
      text-align: center;
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: .95rem;
      line-height: 1.2;
      transform: rotate(12deg);
      padding: .8rem;
      background: rgba(241, 236, 224, .7);
    }

    .cta-row {
      display: flex;
      gap: 1rem;
      flex-wrap: wrap;
      margin-top: 2.2rem
    }

    .btn {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: 1.05rem;
      letter-spacing: .04em;
      padding: 1rem 1.6rem;
      border: 3px solid var(--ink);
      background: var(--paper);
      color: var(--ink);
      cursor: pointer;
      text-decoration: none;
      box-shadow: var(--shadow);
      transition: transform .12s, box-shadow .12s;
      display: inline-block;
    }

    .btn:hover {
      transform: translate(3px, 3px);
      box-shadow: 4px 4px 0 var(--ink)
    }

    .btn:active {
      transform: translate(8px, 8px);
      box-shadow: 0 0 0 var(--ink)
    }

    .btn-primary {
      background: var(--blood);
      color: var(--paper)
    }

    .btn-blue {
      background: var(--aegean);
      color: var(--paper)
    }

    /* ============ COUNTDOWN ============ */
    .countdown-strip {
      background: var(--aegean);
      color: var(--paper);
      border-top: 3px solid var(--ink);
      border-bottom: 3px solid var(--ink);
      padding: 1.4rem 0;
    }

    .countdown-strip .wrap {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 1.5rem;
      flex-wrap: wrap
    }

    .countdown-strip .label {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: 1.3rem;
      letter-spacing: .05em
    }

    .countdown {
      display: flex;
      gap: .6rem
    }

    .cd-cell {
      background: var(--paper);
      color: var(--ink);
      border: 3px solid var(--ink);
      box-shadow: 4px 4px 0 var(--ink);
      min-width: 74px;
      padding: .5rem .4rem;
      text-align: center;
    }

    .cd-cell b {
      font-family: 'Anton', sans-serif;
      font-size: 1.9rem;
      display: block;
      font-weight: 400
    }

    .cd-cell small {
      font-size: .62rem;
      font-weight: 700;
      letter-spacing: .18em;
      text-transform: uppercase
    }

    /* ============ DEFINITION CARD ============ */
    .definition {
      padding: 5rem 0
    }

    .def-card {
      background: #fff;
      border: 3px solid var(--ink);
      box-shadow: var(--shadow);
      padding: 2.5rem;
      max-width: 760px;
      margin: 2rem auto 0;
      transform: rotate(-.6deg);
      position: relative;
    }

    .def-card::before {
      content: "EXHIBIT A";
      position: absolute;
      top: -14px;
      left: 24px;
      background: var(--gold);
      border: 3px solid var(--ink);
      font-weight: 700;
      font-size: .7rem;
      letter-spacing: .2em;
      padding: .25rem .7rem;
    }

    .def-word {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: 2.2rem
    }

    .def-word span {
      color: var(--blood)
    }

    .def-pos {
      font-style: italic;
      color: #6b6357;
      margin: .2rem 0 1rem
    }

    .def-body {
      font-size: 1.12rem;
      line-height: 1.6
    }

    .def-body .greek {
      font-size: 1.3em;
      color: var(--aegean)
    }

    /* ============ QUIZ ============ */
    .quiz {
      padding: 4.5rem 0;
      border-top: 3px solid var(--ink)
    }

    .section-head {
      margin-bottom: 2.5rem
    }

    .section-head h2 {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: clamp(2rem, 5vw, 3.4rem);
      line-height: 1;
      margin-top: .9rem
    }

    .cards {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 1.4rem
    }

    .flip {
      perspective: 1000px;
      height: 300px;
      cursor: pointer
    }

    .flip-inner {
      position: relative;
      width: 100%;
      height: 100%;
      transition: transform .55s;
      transform-style: preserve-3d
    }

    .flip.flipped .flip-inner {
      transform: rotateY(180deg)
    }

    .face {
      position: absolute;
      inset: 0;
      backface-visibility: hidden;
      border: 3px solid var(--ink);
      box-shadow: var(--shadow);
      padding: 1.4rem;
      display: flex;
      flex-direction: column;
    }

    .face.front {
      background: var(--paper)
    }

    .face.front .q-num {
      font-family: 'Anton', sans-serif;
      color: var(--blood);
      font-size: 1rem;
      letter-spacing: .1em
    }

    .face.front .q {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: 1.45rem;
      line-height: 1.15;
      margin-top: .8rem
    }

    .face.front .hint {
      margin-top: auto;
      font-size: .8rem;
      font-weight: 700;
      letter-spacing: .15em;
      text-transform: uppercase;
      color: #6b6357
    }

    .face.back {
      background: var(--ink);
      color: var(--paper);
      transform: rotateY(180deg)
    }

    .face.back .a {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: 1.5rem;
      color: var(--gold);
      line-height: 1.1
    }

    .face.back .roast {
      font-size: .92rem;
      line-height: 1.5;
      margin-top: .9rem;
      color: #d8d2c4
    }

    .face.back .verdict {
      margin-top: auto;
      display: flex;
      gap: .5rem
    }

    .mini-btn {
      flex: 1;
      font-family: 'Space Grotesk', sans-serif;
      font-weight: 700;
      font-size: .75rem;
      letter-spacing: .08em;
      text-transform: uppercase;
      padding: .55rem .3rem;
      cursor: pointer;
      border: 2px solid var(--paper);
      background: transparent;
      color: var(--paper);
    }

    .mini-btn:hover {
      background: var(--paper);
      color: var(--ink)
    }

    .mini-btn.knew {
      border-color: var(--gold);
      color: var(--gold)
    }

    .mini-btn.knew:hover {
      background: var(--gold);
      color: var(--ink)
    }

    /* shame meter */
    .shame-meter {
      margin-top: 2.8rem;
      background: var(--blood);
      color: var(--paper);
      border: 3px solid var(--ink);
      box-shadow: var(--shadow);
      padding: 1.6rem 2rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 1.5rem;
      flex-wrap: wrap;
    }

    .shame-meter .sh-label {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: 1.15rem;
      letter-spacing: .05em;
      line-height: 1.25
    }

    .shame-meter .sh-num {
      font-family: 'Anton', sans-serif;
      font-size: clamp(1.8rem, 4vw, 2.8rem);
      font-variant-numeric: tabular-nums
    }

    .shame-meter small {
      display: block;
      font-family: 'Space Grotesk', sans-serif;
      font-size: .72rem;
      letter-spacing: .1em;
      opacity: .85;
      text-transform: none
    }

    /* ============ WALL OF SHAME ============ */
    .wall {
      padding: 5rem 0;
      border-top: 3px solid var(--ink);
      background: var(--ink);
      color: var(--paper)
    }

    .wall .kicker {
      background: var(--blood)
    }

    .wall h2 {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: clamp(2rem, 5vw, 3.4rem);
      margin: .9rem 0 2.5rem
    }

    .stats {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
      gap: 1.4rem
    }

    .stat {
      border: 3px solid var(--paper);
      padding: 1.8rem;
      position: relative;
      transition: transform .15s
    }

    .stat:hover {
      transform: translateY(-6px) rotate(-.5deg)
    }

    .stat .big {
      font-family: 'Anton', sans-serif;
      font-size: clamp(3.2rem, 7vw, 5rem);
      line-height: 1;
      color: var(--gold)
    }

    .stat:nth-child(2) .big {
      color: #FF5A5F
    }

    .stat:nth-child(3) .big {
      color: #7C9CFF
    }

    .stat p {
      margin-top: .8rem;
      font-size: .98rem;
      line-height: 1.5
    }

    .stat .src {
      display: block;
      margin-top: .9rem;
      font-size: .7rem;
      letter-spacing: .12em;
      text-transform: uppercase;
      opacity: .6
    }

    /* ============ MANIFESTO ============ */
    .manifesto {
      padding: 5rem 0
    }

    .theses {
      margin-top: 2.5rem;
      border-top: 3px solid var(--ink)
    }

    .thesis {
      display: grid;
      grid-template-columns: 110px 1fr;
      gap: 1.5rem;
      padding: 1.6rem 0;
      border-bottom: 3px solid var(--ink);
      align-items: start;
    }

    .thesis .num {
      font-family: 'Anton', sans-serif;
      font-size: 2.4rem;
      color: var(--aegean)
    }

    .thesis p {
      font-size: 1.15rem;
      line-height: 1.55;
      max-width: 720px
    }

    .thesis p b {
      background: linear-gradient(transparent 60%, var(--gold) 60%)
    }

    /* ============ SIGNUP ============ */
    .signup {
      padding: 5rem 0;
      background: var(--aegean);
      border-top: 3px solid var(--ink);
      color: var(--paper)
    }

    .signup h2 {
      font-family: 'Anton', sans-serif;
      text-transform: uppercase;
      font-size: clamp(2.2rem, 6vw, 4rem);
      line-height: 1;
      margin: .9rem 0 1rem
    }

    .signup p.sub {
      max-width: 560px;
      font-size: 1.1rem;
      line-height: 1.55
    }

    .signup-form {
      display: flex;
      gap: .9rem;
      margin-top: 2rem;
      flex-wrap: wrap
    }

    .signup-form input {
      flex: 1;
      min-width: 260px;
      font-family: 'Space Grotesk', sans-serif;
      font-size: 1.05rem;
      font-weight: 500;
      padding: 1rem 1.2rem;
      border: 3px solid var(--ink);
      background: var(--paper);
      color: var(--ink);
      box-shadow: var(--shadow);
    }

    .signup-form input:focus {
      outline: 4px solid var(--gold)
    }

    .form-msg {
      margin-top: 1rem;
      font-weight: 700;
      font-size: .95rem;
      min-height: 1.4em
    }

    .form-msg.ok {
      color: #B6F26A
    }

    .form-msg.err {
      color: #FFB4B7
    }

    .fine-print {
      margin-top: 2.5rem;
      font-size: .75rem;
      opacity: .75;
      max-width: 640px;
      line-height: 1.6
    }

    /* ============ FOOTER ============ */
    footer {
      background: var(--ink);
      color: #8f887a;
      padding: 2.2rem 0;
      font-size: .82rem
    }

    footer .wrap {
      display: flex;
      justify-content: space-between;
      gap: 1rem;
      flex-wrap: wrap;
      align-items: center
    }

    footer .greek {
      color: var(--paper);
      font-size: 1.05rem
    }

    /* ============ ROAST TOAST ============ */
    .roast-toast {
      position: fixed;
      bottom: 1.6rem;
      left: 50%;
      transform: translateX(-50%) rotate(-2deg) translateY(140%);
      background: var(--gold);
      color: var(--ink);
      border: 3px solid var(--ink);
      box-shadow: var(--shadow);
      padding: 1.1rem 1.6rem;
      font-weight: 700;
      font-size: 1rem;
      max-width: 520px;
      text-align: center;
      z-index: 99;
      transition: transform .35s cubic-bezier(.2, 1.4, .4, 1);
      pointer-events: none;
    }

    .roast-toast.show {
      transform: translateX(-50%) rotate(-2deg) translateY(0)
    }

    .roast-toast::before {
      content: "⚡ VERDICT ⚡";
      display: block;
      font-size: .65rem;
      letter-spacing: .25em;
      margin-bottom: .4rem;
      color: var(--blood)
    }

    /* ============ DARK MODE OVERRIDES ============ */
    html.dark .ticker,
    html.dark .kicker,
    html.dark .wall,
    html.dark .face.back,
    html.dark footer {
      background: #100D08;
      color: #F1ECE0;
      border-color: #100D08;
    }

    html.dark .def-card {
      background: #211C12;
      color: #F1ECE0;
      border-color: #F1ECE0;
    }
    html.dark .def-card .def-pos {
      color: #B9AE9C;
    }
    html.dark .def-card::before {
      color: #16130E;
    }

    html.dark .face.front {
      background: #211C12;
      color: #F1ECE0;
    }
    html.dark .face.front .hint {
      color: #B9AE9C;
    }
    html.dark .face.back .a {
      color: #E5C158;
    }
    html.dark .face.back .roast {
      color: #E6DFCE;
    }
    html.dark .mini-btn {
      border-color: #F1ECE0;
      color: #F1ECE0;
    }
    html.dark .mini-btn.knew {
      border-color: #E5C158;
      color: #E5C158;
    }
    html.dark .mini-btn:hover {
      background: #F1ECE0;
      color: #16130E;
    }
    html.dark .mini-btn.knew:hover {
      background: #E5C158;
      color: #16130E;
    }

    html.dark .btn {
      background: #211C12;
      color: #F1ECE0;
    }

    html.dark .roast-toast {
      color: #16130E;
    }
    html.dark .roast-toast::before {
      color: #7B1216;
    }

    html.dark .signup-form input {
      background: #211C12;
      color: #F1ECE0;
    }

    html.dark .wall .stat {
      border-color: #F1ECE0;
    }
    html.dark .wall .stat .big {
      color: #E5C158;
    }
    html.dark .wall .stat .src {
      opacity: .75;
    }

    html.dark .stamp {
      background: rgba(16, 13, 8, .75);
    }
    html.dark .hero .lede,
    html.dark .hero h1,
    html.dark .manifesto .thesis p,
    html.dark .definition .def-body {
      color: #F1ECE0;
    }
    html.dark .hero .pronounce {
      color: #FF5A5F;
    }
    html.dark footer {
      color: #A99F8C;
    }

    @media (max-width:720px) {
      .stamp { display: none }
      .thesis { grid-template-columns: 60px 1fr }
      .thesis .num { font-size: 1.7rem }
      .countdown-strip .wrap { justify-content: center; text-align: center }
    }

    @media (prefers-reduced-motion:reduce) {
      .ticker-track { animation: none }
      .flip-inner { transition: none }
      * { scroll-behavior: auto }
    }
  </style>
</svelte:head>

<!-- THEME TOGGLE -->
<button id="themeToggle" class="theme-toggle" type="button" aria-label="Toggle dark mode">☀️</button>

<!-- TICKER -->
<div class="ticker" aria-hidden="true">
  <div class="ticker-track" id="tickerTrack">
    <span class="red">⚠ ONLY 1 IN 3 AMERICANS CAN PASS THE CITIZENSHIP TEST</span>
    <span>🏛 THE GREEKS SAW THIS COMING</span>
    <span class="red">⚠ SHAME IS THE FIRST STEP</span>
    <span>ἰδιώτης · n. — you, probably</span>
    <span class="red">⚠ CONSTITUTION DAY · SEPT 17</span>
    <span>🦉 ATHENA IS WATCHING</span>
  </div>
</div>

<!-- HERO -->
<section class="hero">
  <div class="wrap">
    <div class="stamp">COMING<br />SEPT 17<br />2026</div>
    <span class="kicker">A civic humiliation experiment · est. 2026</span>
    <div class="greek-word" id="greekWord" title="Go ahead, say it out loud.">ἰδιώτης</div>
    <div class="pronounce">id-ee-OH-tees · noun · Ancient Greek</div>
    <h1>Are you smarter<br />than <span class="outline">an</span> <span class="red">idiot?</span></h1>
    <p class="lede">
      2,500 years ago the Greeks coined a word for a citizen who tuned out of public life —
      who had <strong>zero interest in how their own country worked</strong>. It wasn't a joke.
      It was a diagnosis. On Constitution Day, you're getting tested. The U.S. Citizenship Test.
      The bar is on the floor. Immigrants study <em>months</em> to clear it. Let's see what you do.
    </p>
    <div class="cta-row">
      <a href="#signup" class="btn btn-primary">Notify me on launch day ↓</a>
      <button class="btn btn-blue" id="shameBtn" type="button">Shame me right now</button>
    </div>
  </div>
</section>

<!-- COUNTDOWN -->
<div class="countdown-strip">
  <div class="wrap">
    <div class="label">Launching on<br />Constitution Day</div>
    <div class="countdown" id="countdown" role="timer" aria-live="polite">
      <div class="cd-cell"><b id="cd-d">--</b><small>days</small></div>
      <div class="cd-cell"><b id="cd-h">--</b><small>hours</small></div>
      <div class="cd-cell"><b id="cd-m">--</b><small>min</small></div>
      <div class="cd-cell"><b id="cd-s">--</b><small>sec</small></div>
    </div>
  </div>
</div>

<!-- DEFINITION -->
<section class="definition">
  <div class="wrap">
    <div class="section-head" style="text-align:center">
      <span class="kicker">What the word actually means</span>
    </div>
    <div class="def-card">
      <div class="def-word">id·i·ot <span>— noun</span></div>
      <div class="def-pos">from Ancient Greek <span class="greek">ἰδιώτης</span> (idiṓtēs)</div>
      <p class="def-body">
        A private person. One who withdrew from public life and had no interest in how their
        country was run. To the Athenians — the people who <em>invented democracy</em> — that
        wasn't funny. It was the mark of a useless citizen.<br /><br />
        Over 2,500 years we softened it into "silly person." We're bringing back the original.
        It hits harder, and <b>you're about to find out why.</b>
      </p>
    </div>
  </div>
</section>

<!-- QUIZ -->
<section class="quiz" id="quiz">
  <div class="wrap">
    <div class="section-head">
      <span class="kicker">Warm-up round</span>
      <h2>Try not to cry.</h2>
    </div>
    <div class="cards" id="cards"></div>

    <div class="shame-meter">
      <div class="sh-label">🔥 National Shame-O-Meter™<small>units of shame released so far (live-ish)</small>
      </div>
      <div class="sh-num" id="shameNum">74,000,000</div>
    </div>
  </div>
</section>

<!-- WALL OF SHAME -->
<section class="wall">
  <div class="wrap">
    <span class="kicker">The wall of shame</span>
    <h2>Real numbers. Verified. Unfortunately.</h2>
    <div class="stats">
      <div class="stat">
        <div class="big">36%</div>
        <p>of Americans can pass a version of the citizenship test. That's it. Two out of three of you are
          failing a test designed for people who <em>want</em> to be American.</p>
        <span class="src">Woodrow Wilson National Fellowship Foundation, 2018</span>
      </div>
      <div class="stat">
        <div class="big">13%</div>
        <p>of Americans knew when the Constitution was ratified. It's 1788. It's been 1788 the entire time
          you've been alive.</p>
        <span class="src">Same study. Yes, really.</span>
      </div>
      <div class="stat">
        <div class="big">1 in 3</div>
        <p>can't name all three branches of government. Legislative. Executive. Judicial. You learned this
          in 5th grade and then simply... let it go.</p>
        <span class="src">Annenberg Constitution Day Civics Survey</span>
      </div>
    </div>
  </div>
</section>

<!-- MANIFESTO -->
<section class="manifesto">
  <div class="wrap">
    <div class="section-head">
      <span class="kicker">The amitheidiot manifesto</span>
      <h2>Why we're doing this to you</h2>
    </div>
    <div class="theses">
      <div class="thesis">
        <div class="num">01</div>
        <p><b>Shame is a tool.</b> Nobody ever changed because they felt great about themselves. You're
          about to feel a little less great. Good. That's the point.</p>
      </div>
      <div class="thesis">
        <div class="num">02</div>
        <p><b>Ignorance is a choice</b> — and the receipts say you keep making it. The test is public. The
          answers are free. You just... didn't.</p>
      </div>
      <div class="thesis">
        <div class="num">03</div>
        <p><b>The bar is on the floor.</b> This is the test immigrants study for, in a second language,
          while working two jobs. If they can out-civics you, you owe them a nod and an apology.</p>
      </div>
      <div class="thesis">
        <div class="num">04</div>
        <p><b>The country isn't broken because of politicians.</b> It's broken because the people who elect
          them can't pass a 10-question quiz. You're not the victim here. You're the variable.</p>
      </div>
      <div class="thesis">
        <div class="num">05</div>
        <p><b>Do better.</b> Read the thing. Learn the thing. Vote in the boring local ones. Then come back
          and retake the test — and wipe that look off your face.</p>
      </div>
    </div>
  </div>
</section>

<!-- SIGNUP -->
<section class="signup" id="signup">
  <div class="wrap">
    <span class="kicker" style="background:var(--ink)">Be there when the bar hits the floor</span>
    <h2>Find out on<br />September 17.</h2>
    <p class="sub">Drop your email and we'll send the test the moment it's live. Be first in line to discover
      what you don't know. Bring a helmet.</p>
    <form class="signup-form" id="signupForm" novalidate>
      <input type="email" id="emailInput" placeholder="your.email@probably-an-idiot.com"
        aria-label="Email address" required />
      <button type="submit" class="btn btn-primary" style="box-shadow:6px 6px 0 var(--ink)">I volunteer for
        judgment</button>
    </form>
    <div class="form-msg" id="formMsg" aria-live="polite"></div>
    <p class="fine-print">No spam. One launch email, maybe one follow-up if you chicken out. No immigrants were
      harmed in the making of this site — they all passed. amitheidiot.com is not affiliated with USCIS, the
      Greek government, or anyone with actual authority.</p>
  </div>
</section>

<footer>
  <div class="wrap">
    <div><span class="greek">ἰδιώτης</span> &nbsp;·&nbsp; amitheidiot.com © 2026 — a project of civic
      humiliation, lovingly.</div>
    <div>The Greeks started it. We're just finishing the job. 🦉</div>
  </div>
</footer>

<div class="roast-toast" id="roastToast" role="status"></div>

<script>
  import { onMount } from 'svelte';

  onMount(() => {
        /* ================= THEME TOGGLE ================= */
        const themeBtn = document.getElementById('themeToggle');
        function applyTheme() {
            const dark = document.documentElement.classList.contains('dark');
            if (themeBtn) themeBtn.textContent = dark ? '🌙' : '☀️';
        }
        if (themeBtn) {
            themeBtn.addEventListener('click', () => {
                const dark = document.documentElement.classList.toggle('dark');
                try { localStorage.setItem('amitheidiot-theme', dark ? 'dark' : 'light'); } catch (e) {}
                applyTheme();
            });
        }
        applyTheme();
/* ================= COUNTDOWN ================= */
        const LAUNCH = new Date('2026-09-17T09:00:00-04:00'); // Constitution Day
        function tickClock() {
            let diff = LAUNCH - new Date();
            if (diff <= 0) {
                document.getElementById('countdown').innerHTML =
                    '<div class="cd-cell" style="min-width:auto;padding:.6rem 1.2rem"><b style="font-size:1.2rem">THE TEST IS LIVE. GOOD LUCK, ἸΔΙΩΤΑ.</b></div>';
                return;
            }
            document.getElementById('cd-d').textContent = Math.floor(diff / 864e5);
            document.getElementById('cd-h').textContent = String(Math.floor(diff / 36e5) % 24).padStart(2, '0');
            document.getElementById('cd-m').textContent = String(Math.floor(diff / 6e4) % 60).padStart(2, '0');
            document.getElementById('cd-s').textContent = String(Math.floor(diff / 1e3) % 60).padStart(2, '0');
        }
        tickClock(); setInterval(tickClock, 1000);

        /* ================= QUIZ ================= */
        const QUIZ = [
            {
                q: "What is the supreme law of the land?",
                opts: ["The Declaration of Independence", "The Constitution", "The Articles of Confederation", "The Bill of Rights"], correct: 1,
                good: "Correct. The bare minimum — but you cleared it. Barely.",
                bad: "It's the Constitution. The supreme law of the land. You live under it and didn't know its name."
            },
            {
                q: "How many amendments does the Constitution have?",
                opts: ["10", "27", "50", "7"], correct: 1,
                good: "27. Not 10. You knew that? Suspicious. Keep going.",
                bad: "It's 27. The Bill of Rights is only the first 10. You stopped reading in middle school."
            },
            {
                q: "What is ONE right from the First Amendment?",
                opts: ["Right to bear arms", "Freedom of speech", "Right to a jury trial", "Freedom from searches"], correct: 1,
                good: "Freedom of speech — the one you use to post nonsense. At least you can name it.",
                bad: "It's freedom of speech. The right you exercise constantly but couldn't name. Ironic."
            },
            {
                q: "What is the capital of the United States?",
                opts: ["New York City", "Washington, D.C.", "Philadelphia", "Boston"], correct: 1,
                good: "Washington, D.C. — not New York. Congratulations, you passed 5th grade.",
                bad: "It's Washington, D.C. Not New York. Not Philly. You've had a whole life to get this."
            },
            {
                q: "Who was the first President of the United States?",
                opts: ["Thomas Jefferson", "Abraham Lincoln", "George Washington", "Benjamin Franklin"], correct: 2,
                good: "George Washington. The man on the dollar bill — the one you've been holding this whole time.",
                bad: "It's George Washington. The first president. On the one-dollar bill. The one you've spent."
            }
        ];
        let qi = 0, score = 0;
        const qText = document.getElementById('qText'), qOpts = document.getElementById('qOpts'),
            qFeedback = document.getElementById('qFeedback'), qNext = document.getElementById('qNext'),
            qCount = document.getElementById('qCount'), qFill = document.getElementById('qFill');

        function renderQ() {
            const item = QUIZ[qi];
            qCount.textContent = `Q ${qi + 1} / ${QUIZ.length}`;
            qFill.style.width = (qi / QUIZ.length * 100) + '%';
            qText.textContent = item.q;
            qFeedback.innerHTML = ''; qNext.classList.remove('show');
            qOpts.innerHTML = '';
            item.opts.forEach((opt, i) => {
                const b = document.createElement('button');
                b.className = 'opt'; b.type = 'button'; b.textContent = String.fromCharCode(65 + i) + '.  ' + opt;
                b.addEventListener('click', () => answer(i, b));
                qOpts.appendChild(b);
            });
        }
        function answer(i, btn) {
            const item = QUIZ[qi];
            [...qOpts.children].forEach((b, idx) => {
                b.disabled = true;
                if (idx === item.correct) b.classList.add('correct');
            });
            if (i === item.correct) { score++; qFeedback.innerHTML = '<div class="good"><b>✔ Technically correct</b>' + item.good + '</div>'; }
            else { btn.classList.add('wrong'); qFeedback.innerHTML = '<div class="bad"><b>✘ Oof.</b>' + item.bad + '</div>'; }
            qNext.classList.add('show');
        }
        qNext.addEventListener('click', () => {
            qi++;
            if (qi < QUIZ.length) { renderQ(); }
            else showResults();
        });
        function showResults() {
            qFill.style.width = '100%';
            document.getElementById('quizPlay').style.display = 'none';
            const res = document.getElementById('quizResults'); res.classList.add('show');
            document.getElementById('resScore').textContent = score + '/' + QUIZ.length;
            const levels = [
                ["A certified ἰδιώτης.", "Zero. The Greeks literally invented this word for you. Frame this score and hang it next to your participation trophy."],
                ["Barely sentient.", "One correct. Probably a lucky click. The Founders are rotating in their graves at a moderate speed."],
                ["Statistically average.", "Which means statistically embarrassing. You're the median American problem."],
                ["You passed. Congratulations.", "That's the bar. You cleared the floor. Don't celebrate — this is the minimum for citizenship you already have."],
                ["Suspiciously competent.", "Four out of five. Either you actually pay attention, or you're an immigrant. Either way, carry the team."],
                ["Certified non-idiot.", "Perfect score. Are you sure you're American? Take the real test Sept 17 and prove it wasn't a fluke."]
            ];
            document.getElementById('resLevel').textContent = levels[score][0];
            document.getElementById('resVerdict').textContent = levels[score][1];
        }
        document.getElementById('retakeBtn').addEventListener('click', () => {
            qi = 0; score = 0;
            document.getElementById('quizResults').classList.remove('show');
            document.getElementById('quizPlay').style.display = 'block';
            renderQ();
        });
        document.getElementById('shareBtn').addEventListener('click', () => {
            const level = document.getElementById('resLevel').textContent;
            const text = `I scored ${score}/${QUIZ.length} on the amitheidiot.com citizenship warm-up. My idiocy level: "${level}". Are YOU smarter than an idiot? Find out Sept 17. amitheidiot.com`;
            if (navigator.share) { navigator.share({ title: 'amitheidiot.com', text, url: 'https://amitheidiot.com' }).catch(() => { }); }
            else if (navigator.clipboard) { navigator.clipboard.writeText(text).then(() => showToast("Copied. Now paste it and accept your shame publicly.")); }
            else { window.open('https://twitter.com/intent/tweet?text=' + encodeURIComponent(text), '_blank'); }
        });
        renderQ();

        /* ================= SHAME METER (count-up + reveal) ================= */
        const SHAME_TARGET = 77080038; // Trump 2024 popular vote — the record
        const shameNumEl = document.getElementById('shameNum');
        let shameStarted = false;
        function countUp() {
            if (shameStarted) return; shameStarted = true;
            const dur = 2600, start = performance.now();
            function step(now) {
                const p = Math.min((now - start) / dur, 1);
                const eased = 1 - Math.pow(1 - p, 3);
                shameNumEl.textContent = Math.floor(eased * SHAME_TARGET).toLocaleString('en-US');
                if (p < 1) requestAnimationFrame(step); else shameNumEl.textContent = SHAME_TARGET.toLocaleString('en-US');
            }
            requestAnimationFrame(step);
        }
        const meter = document.getElementById('shameMeter');
        function toggleShame() { meter.classList.toggle('revealed'); if (meter.classList.contains('revealed')) showToast("You figured it out. Now everyone you voted for is someone you can't name."); }
        meter.addEventListener('click', toggleShame);
        meter.addEventListener('keydown', e => { if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); toggleShame(); } });

        /* ================= SHAME ME NOW MODAL ================= */
        const overlay = document.getElementById('modalOverlay');
        document.getElementById('shameBtn').addEventListener('click', () => { overlay.classList.add('open'); document.body.style.overflow = 'hidden'; });
        function closeModal() { overlay.classList.remove('open'); document.body.style.overflow = ''; }
        document.getElementById('modalClose').addEventListener('click', closeModal);
        overlay.addEventListener('click', e => { if (e.target === overlay) closeModal(); });
        document.addEventListener('keydown', e => { if (e.key === 'Escape') closeModal(); });

        /* ================= TOAST ================= */
        const toast = document.getElementById('roastToast'); let toastTimer;
        function showToast(msg) { toast.textContent = msg; toast.classList.add('show'); clearTimeout(toastTimer); toastTimer = setTimeout(() => toast.classList.remove('show'), 4200); }
        document.getElementById('greekWord').addEventListener('click', () => showToast("ἰδιώτης: 'a private person.' The Athenians thought that was the saddest thing a citizen could be."));

        /* ================= SIGNUP ================= */
        const form = document.getElementById('signupForm'), msg = document.getElementById('formMsg');
        form.addEventListener('submit', e => {
            e.preventDefault();
            const email = document.getElementById('emailInput').value.trim();
            if (!/^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/.test(email)) {
                msg.className = 'form-msg err'; msg.textContent = "That's not an email address. See, THIS is the problem."; return;
            }
            // TODO: POST to Formspree/Mailchimp/your backend here.
            msg.className = 'form-msg ok'; msg.textContent = "✓ Consider it done. That's more civic action than most people take all year. See you September 17 — bring shame.";
            form.reset();
        });

        /* ================= LINE-DRAW ANIMATIONS ================= */
        function initDraw() {
            document.querySelectorAll('.draw path, .draw ellipse').forEach(el => {
                const len = el.getTotalLength();
                el.style.strokeDasharray = len; el.style.strokeDashoffset = len;
                el.style.transition = 'stroke-dashoffset 1.4s ease';
            });
        }
        function drawIn(el) { el.querySelectorAll('path,ellipse').forEach(p => p.style.strokeDashoffset = '0'); }
        initDraw();

        /* ================= SCROLL REVEALS ================= */
        const io = new IntersectionObserver(entries => {
            entries.forEach(en => {
                if (!en.isIntersecting) return;
                en.target.classList.add('visible');
                en.target.querySelectorAll('.draw').forEach(drawIn);
                if (en.target.classList.contains('shame-section')) countUp();
                io.unobserve(en.target);
            });
        }, { threshold: .2 });
        document.querySelectorAll('.reveal').forEach(el => io.observe(el));
        // draw the hero scribble on load
        window.addEventListener('load', () => { document.querySelectorAll('.hero .draw').forEach(drawIn); });

        /* ================= TICKER LOOP ================= */
        const track = document.getElementById('tickerTrack'); track.innerHTML += track.innerHTML;
    return () => {
      // cleanup intervals/observers
    };
  });
</script>

<svelte:head>
  <title>amitheidiot.com — Are You Smarter Than An Idiot?</title>
  <meta name="description" content="The ancient Greeks had a word for people who checked out of their country. On Constitution Day, you're going to hear it." />
  <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🏛️</text></svg>" />
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Anton&family=Space+Grotesk:wght@400;500;700&family=GFS+Didot&display=swap" rel="stylesheet" />
  <style>

:root {
    --paper: #F1ECE0;
    --ink: #16130E;
    --aegean: #1F4FD8;
    --blood: #D8232A;
    --gold: #C9A227;
    --shadow: 8px 8px 0 var(--ink);
}

/* ============ DARK MODE (default) ============ */
html.dark {
    --paper: #16130E;
    --ink: #F1ECE0;
    --aegean: #6C8CFF;
    --blood: #FF5A5F;
    --gold: #E5C158;
    --shadow: 8px 8px 0 rgba(0, 0, 0, .55);
}

html.dark .def-card,
html.dark .quiz-box,
html.dark .stat,
html.dark .mstat {
    background: #211C12;
    color: var(--ink);
}

html.dark .def-pos {
    color: #B9AE9C;
}

html.dark .def-card::before {
    color: #16130E;
}

html.dark .opt:hover {
    background: #211C12;
}

html.dark .thesis p b {
    background: linear-gradient(transparent 60%, var(--gold) 60%);
}

/* theme toggle button */
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

.greek {
    font-family: 'GFS Didot', serif
}

::selection {
    background: var(--blood);
    color: var(--paper)
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
    text-transform: uppercase
}

.ticker-track {
    display: inline-block;
    padding: .6rem 0;
    animation: tick 30s linear infinite
}

.ticker span {
    margin: 0 1.6rem
}

.ticker .red {
    color: #FF5A5F
}

.ticker .gold {
    color: var(--gold)
}

@keyframes tick {
    from {
        transform: translateX(0)
    }

    to {
        transform: translateX(-50%)
    }
}

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
    transform: rotate(-1deg)
}

/* scroll reveal */
.reveal {
    opacity: 0;
    transform: translateY(28px);
    transition: opacity .7s ease, transform .7s ease
}

.reveal.visible {
    opacity: 1;
    transform: none
}

/* ============ HERO ============ */
.hero {
    padding: 4.5rem 0 4rem;
    position: relative
}

.float-letter {
    position: absolute;
    font-family: 'GFS Didot', serif;
    color: var(--aegean);
    opacity: .09;
    user-select: none;
    pointer-events: none;
    animation: floaty 14s ease-in-out infinite
}

@keyframes floaty {

    0%,
    100% {
        transform: translateY(0) rotate(0deg)
    }

    50% {
        transform: translateY(-26px) rotate(4deg)
    }
}

.hero .greek-word {
    font-family: 'GFS Didot', serif;
    font-size: clamp(4rem, 14vw, 10.5rem);
    line-height: .95;
    color: var(--aegean);
    user-select: none;
    cursor: default;
    position: relative;
    z-index: 2
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
    margin: 2.2rem 0 .4rem;
    position: relative;
    z-index: 2
}

.hero h1 .outline {
    color: transparent;
    -webkit-text-stroke: 2.5px var(--ink)
}

.hero h1 .red {
    color: var(--blood)
}

.scribble-wrap {
    position: relative;
    display: inline-block
}

.scribble {
    position: absolute;
    left: -4%;
    bottom: -12px;
    width: 108%;
    height: 26px;
    overflow: visible
}

.hero .lede {
    max-width: 660px;
    font-size: 1.15rem;
    line-height: 1.55;
    font-weight: 500;
    margin-top: 1.4rem
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
    z-index: 3
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
    display: inline-block
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
    padding: 1.4rem 0
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
    text-align: center
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

/* meander divider */
.meander {
    border: none;
    border-top: 3px solid var(--ink);
    position: relative;
    margin: 0
}

.meander::after {
    content: "⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗ ⌗";
    position: absolute;
    top: -14px;
    left: 0;
    right: 0;
    text-align: center;
    color: var(--aegean);
    letter-spacing: .4em;
    font-size: 1rem;
    overflow: hidden;
    white-space: nowrap
}

/* ============ DEFINITION ============ */
.definition {
    padding: 5rem 0
}

.def-card {
    background: #fff;
    border: 3px solid var(--ink);
    box-shadow: var(--shadow);
    padding: 2.5rem;
    max-width: 780px;
    margin: 2rem auto 0;
    transform: rotate(-.6deg);
    position: relative
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
    padding: .25rem .7rem
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
    font-size: 1.1rem;
    line-height: 1.6
}

.def-body .greek {
    font-size: 1.3em;
    color: var(--aegean)
}

.def-cols {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.2rem;
    margin-top: 1.4rem
}

.def-col {
    border: 2px solid var(--ink);
    padding: 1rem 1.2rem
}

.def-col h4 {
    font-family: 'Anton', sans-serif;
    text-transform: uppercase;
    font-size: .9rem;
    letter-spacing: .1em;
    margin-bottom: .5rem
}

.def-col.then h4 {
    color: var(--aegean)
}

.def-col.now h4 {
    color: var(--blood)
}

.def-col p {
    font-size: .95rem;
    line-height: 1.5
}

@media(max-width:600px) {
    .def-cols {
        grid-template-columns: 1fr
    }
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

.quiz-box {
    background: #fff;
    border: 3px solid var(--ink);
    box-shadow: var(--shadow);
    padding: 2rem;
    max-width: 780px;
    margin: 0 auto;
    position: relative
}

.quiz-progress {
    display: flex;
    align-items: center;
    gap: 1rem;
    margin-bottom: 1.6rem
}

.quiz-progress .bar {
    flex: 1;
    height: 14px;
    border: 2px solid var(--ink);
    background: var(--paper);
    overflow: hidden
}

.quiz-progress .fill {
    height: 100%;
    background: var(--aegean);
    width: 0%;
    transition: width .4s ease
}

.quiz-progress .qcount {
    font-family: 'Anton', sans-serif;
    text-transform: uppercase;
    font-size: .9rem;
    letter-spacing: .1em;
    white-space: nowrap
}

.quiz-q {
    font-family: 'Anton', sans-serif;
    text-transform: uppercase;
    font-size: clamp(1.3rem, 3vw, 1.9rem);
    line-height: 1.15;
    margin-bottom: 1.4rem;
    min-height: 2.3em
}

.quiz-opts {
    display: grid;
    gap: .8rem
}

.opt {
    font-family: 'Space Grotesk', sans-serif;
    font-weight: 700;
    font-size: 1rem;
    text-align: left;
    padding: 1rem 1.2rem;
    border: 3px solid var(--ink);
    background: var(--paper);
    cursor: pointer;
    transition: transform .1s, background .15s;
    position: relative
}

.opt:hover {
    transform: translateX(6px);
    background: #fff
}

.opt.correct {
    background: #2E7D32;
    color: #fff;
    border-color: #1b4d1f
}

.opt.wrong {
    background: var(--blood);
    color: #fff;
    border-color: #8f1418
}

.opt:disabled {
    cursor: default;
    opacity: .95
}

.quiz-feedback {
    margin-top: 1.3rem;
    font-size: 1rem;
    line-height: 1.5;
    min-height: 1.2em;
    font-weight: 500
}

.quiz-feedback b {
    font-family: 'Anton', sans-serif;
    text-transform: uppercase;
    letter-spacing: .04em;
    display: block;
    margin-bottom: .3rem
}

.quiz-feedback .good b {
    color: #2E7D32
}

.quiz-feedback .bad b {
    color: var(--blood)
}

.quiz-next {
    margin-top: 1.2rem;
    display: none
}

.quiz-next.show {
    display: inline-block
}

/* results */
.quiz-results {
    display: none;
    text-align: center;
    padding: 1rem 0
}

.quiz-results.show {
    display: block
}

.quiz-results .score {
    font-family: 'Anton', sans-serif;
    font-size: clamp(3rem, 9vw, 5rem);
    color: var(--blood)
}

.quiz-results .level {
    font-family: 'Anton', sans-serif;
    text-transform: uppercase;
    font-size: clamp(1.2rem, 3vw, 1.8rem);
    margin: .6rem 0 .4rem
}

.quiz-results .verdict {
    font-size: 1.05rem;
    line-height: 1.5;
    max-width: 520px;
    margin: 0 auto 1.6rem
}

/* ============ SHAME METER ============ */
.shame-section {
    padding: 4.5rem 0;
    border-top: 3px solid var(--ink);
    background: var(--ink);
    color: var(--paper)
}

.shame-section .kicker {
    background: var(--blood)
}

.shame-section h2 {
    font-family: 'Anton', sans-serif;
    text-transform: uppercase;
    font-size: clamp(2rem, 5vw, 3.4rem);
    margin: .9rem 0 2rem
}

.shame-meter {
    position: relative;
    background: var(--blood);
    color: var(--paper);
    border: 3px solid var(--paper);
    box-shadow: 8px 8px 0 var(--gold);
    padding: 2.4rem 2rem;
    text-align: center;
    max-width: 760px;
    margin: 0 auto;
    cursor: pointer
}

.shame-meter .sh-label {
    font-family: 'Anton', sans-serif;
    text-transform: uppercase;
    font-size: 1.2rem;
    letter-spacing: .06em
}

.shame-meter .sh-num {
    font-family: 'Anton', sans-serif;
    font-size: clamp(2.6rem, 8vw, 4.6rem);
    line-height: 1.1;
    margin: .6rem 0;
    font-variant-numeric: tabular-nums
}

.shame-meter .sh-clue {
    font-size: .95rem;
    font-weight: 500;
    opacity: .95;
    max-width: 520px;
    margin: 0 auto;
    line-height: 1.5
}

.shame-meter .sh-hint {
    margin-top: 1rem;
    font-size: .95rem;
    font-weight: 700;
    color: var(--gold);
    max-height: 0;
    overflow: hidden;
    opacity: 0;
    transition: max-height .6s ease, opacity .6s ease
}

.shame-meter.revealed .sh-hint {
    max-height: 200px;
    opacity: 1
}

.shame-meter .tap {
    font-size: .72rem;
    letter-spacing: .2em;
    text-transform: uppercase;
    opacity: .7;
    margin-top: 1rem
}

.shame-circle {
    position: absolute;
    inset: -30px;
    pointer-events: none;
    overflow: visible
}

.shame-caption {
    max-width: 760px;
    margin: 1.6rem auto 0;
    text-align: center;
    font-size: .9rem;
    opacity: .85;
    line-height: 1.6
}

/* ============ WALL OF SHAME ============ */
.wall {
    padding: 5rem 0;
    border-top: 3px solid var(--ink)
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
    border: 3px solid var(--ink);
    background: #fff;
    padding: 1.8rem;
    position: relative;
    transition: transform .15s;
    box-shadow: 6px 6px 0 var(--ink)
}

.stat:hover {
    transform: translateY(-6px) rotate(-.5deg)
}

.stat .tag {
    position: absolute;
    top: -12px;
    right: 14px;
    background: var(--aegean);
    color: #fff;
    font-size: .62rem;
    font-weight: 700;
    letter-spacing: .15em;
    text-transform: uppercase;
    padding: .2rem .6rem;
    border: 2px solid var(--ink)
}

.stat .big {
    font-family: 'Anton', sans-serif;
    font-size: clamp(3rem, 6vw, 4.6rem);
    line-height: 1;
    color: var(--blood)
}

.stat p {
    margin-top: .8rem;
    font-size: .96rem;
    line-height: 1.5
}

.stat .src {
    display: block;
    margin-top: .9rem;
    font-size: .68rem;
    letter-spacing: .1em;
    text-transform: uppercase;
    opacity: .55
}

/* ============ STATS MODAL (Shame me now) ============ */
.modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(22, 19, 14, .85);
    z-index: 100;
    display: none;
    align-items: flex-start;
    justify-content: center;
    overflow-y: auto;
    padding: 3rem 1rem
}

.modal-overlay.open {
    display: flex
}

.modal {
    background: var(--paper);
    border: 3px solid var(--ink);
    box-shadow: 12px 12px 0 var(--blood);
    max-width: 900px;
    width: 100%;
    padding: 2.5rem;
    position: relative;
    animation: pop .35s cubic-bezier(.2, 1.4, .4, 1)
}

@keyframes pop {
    from {
        transform: scale(.9) rotate(-1deg);
        opacity: 0
    }

    to {
        transform: scale(1) rotate(0);
        opacity: 1
    }
}

.modal .close {
    position: absolute;
    top: 14px;
    right: 14px;
    width: 44px;
    height: 44px;
    border: 3px solid var(--ink);
    background: var(--blood);
    color: #fff;
    font-size: 1.3rem;
    font-weight: 700;
    cursor: pointer;
    line-height: 1
}

.modal h3 {
    font-family: 'Anton', sans-serif;
    text-transform: uppercase;
    font-size: clamp(1.6rem, 4vw, 2.6rem);
    margin: .6rem 0 1.6rem
}

.modal-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
    gap: 1.2rem
}

.mstat {
    border: 3px solid var(--ink);
    background: #fff;
    padding: 1.3rem
}

.mstat .cat {
    display: inline-block;
    font-size: .6rem;
    font-weight: 700;
    letter-spacing: .18em;
    text-transform: uppercase;
    padding: .2rem .55rem;
    border: 2px solid var(--ink);
    margin-bottom: .7rem;
    background: var(--gold)
}

.mstat .cat.media {
    background: var(--blood);
    color: #fff
}

.mstat .cat.lit {
    background: var(--aegean);
    color: #fff
}

.mstat .cat.will {
    background: #2E7D32;
    color: #fff
}

.mstat .cat.civic {
    background: #8e44ad;
    color: #fff
}

.mstat .num {
    font-family: 'Anton', sans-serif;
    font-size: 2.2rem;
    color: var(--blood);
    line-height: 1
}

.mstat p {
    font-size: .9rem;
    line-height: 1.5;
    margin-top: .5rem
}

.mstat .src {
    display: block;
    margin-top: .6rem;
    font-size: .62rem;
    letter-spacing: .1em;
    text-transform: uppercase;
    opacity: .55
}

.modal .footnote {
    margin-top: 1.6rem;
    font-size: .75rem;
    opacity: .7;
    line-height: 1.6
}

/* ============ MANIFESTO ============ */
.manifesto {
    padding: 5rem 0;
    border-top: 3px solid var(--ink)
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
    align-items: start
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

@media(max-width:720px) {
    .thesis {
        grid-template-columns: 56px 1fr
    }

    .thesis .num {
        font-size: 1.6rem
    }
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
    box-shadow: var(--shadow)
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

/* draw-line SVGs */
.draw path,
.draw ellipse {
    fill: none;
    stroke-linecap: round
}

/* toast */
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
    z-index: 120;
    transition: transform .35s cubic-bezier(.2, 1.4, .4, 1);
    pointer-events: none
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

@media(max-width:720px) {
    .stamp {
        display: none
    }

    .countdown-strip .wrap {
        justify-content: center;
        text-align: center
    }
}

@media(prefers-reduced-motion:reduce) {
    .ticker-track {
        animation: none
    }

    .float-letter {
        animation: none
    }

    * {
        scroll-behavior: auto
    }
}

  </style>
</svelte:head>

<!-- TICKER -->
    <button class="theme-toggle" id="themeToggle" type="button" aria-label="Toggle dark mode">🌙</button>
    <div class="ticker" aria-hidden="true">
        <div class="ticker-track" id="tickerTrack">
            <span class="red">⚠ ONLY 1 IN 3 AMERICANS CAN PASS THE CITIZENSHIP TEST</span>
            <span>🏛 THE GREEKS SAW THIS COMING</span>
            <span class="red">⚠ 54% OF ADULTS READ BELOW 6TH GRADE</span>
            <span class="gold">ἰδιώτης · n. — you, probably</span>
            <span class="red">⚠ CONSTITUTION DAY · SEPT 17</span>
            <span>🦉 ATHENA IS WATCHING</span>
        </div>
    </div>

    <!-- HERO -->
    <section class="hero">
        <span class="float-letter" style="top:8%;left:6%;font-size:5rem;animation-delay:0s">ἰ</span>
        <span class="float-letter" style="top:60%;left:3%;font-size:3.4rem;animation-delay:2s">δ</span>
        <span class="float-letter" style="top:18%;right:22%;font-size:4.2rem;animation-delay:1s">ι</span>
        <span class="float-letter" style="top:72%;right:8%;font-size:6rem;animation-delay:3s">ώ</span>
        <span class="float-letter" style="top:40%;left:46%;font-size:3rem;animation-delay:4s">τ</span>
        <span class="float-letter" style="top:85%;left:38%;font-size:3.6rem;animation-delay:2.5s">η</span>

        <div class="wrap">
            <div class="stamp">COMING<br>SEPT 17<br>2026</div>
            <span class="kicker">A civic humiliation experiment · est. 2026</span>
            <div class="greek-word" id="greekWord" title="Go ahead, say it out loud.">ἰδιώτης</div>
            <div class="pronounce">id-ee-OH-tees · noun · Ancient Greek</div>
            <h1>Are you smarter<br>than
                <span class="scribble-wrap">
                    <span class="outline">an</span> <span class="red">idiot?</span>
                    <svg class="scribble draw" viewBox="0 0 300 26" preserveAspectRatio="none" aria-hidden="true">
                        <path d="M6 18 C 60 6, 120 24, 170 12 S 270 16, 294 10" stroke="#D8232A" stroke-width="5" />
                    </svg>
                </span>
            </h1>
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
            <div class="label">Launching on<br>Constitution Day</div>
            <div class="countdown" id="countdown" role="timer" aria-live="polite">
                <div class="cd-cell"><b id="cd-d">--</b><small>days</small></div>
                <div class="cd-cell"><b id="cd-h">--</b><small>hours</small></div>
                <div class="cd-cell"><b id="cd-m">--</b><small>min</small></div>
                <div class="cd-cell"><b id="cd-s">--</b><small>sec</small></div>
            </div>
        </div>
    </div>

    <!-- DEFINITION -->
    <section class="definition reveal">
        <div class="wrap">
            <div class="section-head" style="text-align:center"><span class="kicker">What the word actually means</span>
            </div>
            <div class="def-card">
                <div class="def-word">id·i·ot <span>— noun</span></div>
                <div class="def-pos">from Ancient Greek <span class="greek">ἰδιώτης</span> (idiṓtēs) — "a private
                    person, one's own"</div>
                <p class="def-body">
                    Not "stupid." The original ἰδιώτης was a <b>citizen who withdrew from public life</b> —
                    someone with no interest in how their country was governed. To the Athenians, the people
                    who <em>invented democracy</em>, that wasn't funny. It was the mark of a useless citizen.
                    Over 2,500 years we softened it into "silly person." We're bringing back the original.
                </p>
                <div class="def-cols">
                    <div class="def-col then">
                        <h4>THEN · Athens, ~400 BC</h4>
                        <p>The saddest thing a citizen could be. You had a voice in the greatest experiment in self-rule
                            — and you chose to tune out.</p>
                    </div>
                    <div class="def-col now">
                        <h4>NOW · You, today</h4>
                        <p>You can't name your senator, missed the last three local elections, and think the
                            Constitution "got ratified like, 1900-something."</p>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <hr class="meander">

    <!-- QUIZ -->
    <section class="quiz reveal" id="quiz">
        <div class="wrap">
            <div class="section-head">
                <span class="kicker">Warm-up round</span>
                <h2>Try not to cry.</h2>
            </div>
            <div class="quiz-box">
                <div id="quizPlay">
                    <div class="quiz-progress">
                        <div class="qcount" id="qCount">Q 1 / 5</div>
                        <div class="bar">
                            <div class="fill" id="qFill"></div>
                        </div>
                    </div>
                    <div class="quiz-q" id="qText"></div>
                    <div class="quiz-opts" id="qOpts"></div>
                    <div class="quiz-feedback" id="qFeedback"></div>
                    <button class="btn btn-primary quiz-next" id="qNext" type="button">Next →</button>
                </div>
                <div class="quiz-results" id="quizResults">
                    <div class="score" id="resScore">0/5</div>
                    <div class="level" id="resLevel"></div>
                    <div class="verdict" id="resVerdict"></div>
                    <div class="cta-row" style="justify-content:center">
                        <button class="btn btn-blue" id="shareBtn" type="button">📣 Share my shame</button>
                        <button class="btn" id="retakeBtn" type="button">Redeem myself</button>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- SHAME METER -->
    <section class="shame-section reveal">
        <div class="wrap">
            <div class="section-head" style="text-align:center"><span class="kicker">The National Shame-O-Meter™</span>
            </div>
            <h2 style="text-align:center">A record. Set recently.</h2>
            <div class="shame-meter" id="shameMeter" role="button" tabindex="0"
                aria-label="Reveal what this number means">
                <svg class="shame-circle draw" viewBox="0 0 800 260" preserveAspectRatio="none" aria-hidden="true">
                    <ellipse cx="400" cy="130" rx="388" ry="118" stroke="#C9A227" stroke-width="5" />
                </svg>
                <div class="sh-label">🔥 Shame units on record</div>
                <div class="sh-num" id="shameNum">0</div>
                <div class="sh-clue">This number was set <b>by the American people themselves</b>. All at once. In a
                    single day.<br><span style="opacity:.8">November 5, 2024.</span></div>
                <div class="sh-hint" id="shameHint">That's <b>77,080,038</b> — the most votes any candidate for
                    president has <i>ever</i> received. We're not saying it's your fault. We're saying… it's everyone's
                    fault. <b>Mostly yours.</b></div>
                <div class="tap">— tap to figure it out —</div>
            </div>
            <p class="shame-caption">The Shame-O-Meter™ does not count down. It only counts up. Like everything else in
                this country.</p>
        </div>
    </section>

    <!-- WALL OF SHAME -->
    <section class="wall reveal">
        <div class="wrap">
            <span class="kicker">The wall of shame</span>
            <h2>Real numbers. Verified. Unfortunately.</h2>
            <div class="stats">
                <div class="stat"><span class="tag">Civics</span>
                    <div class="big">36%</div>
                    <p>of Americans can pass a version of the citizenship test. Two out of three of you are failing a
                        test designed for people who <em>want</em> to be American.</p>
                    <span class="src">Woodrow Wilson Foundation, 2018</span>
                </div>
                <div class="stat"><span class="tag">History</span>
                    <div class="big">13%</div>
                    <p>knew when the Constitution was ratified. It's 1788. It's been 1788 your entire life.</p>
                    <span class="src">Same study. Yes, really.</span>
                </div>
                <div class="stat"><span class="tag">Government</span>
                    <div class="big">1 in 3</div>
                    <p>can't name all three branches of government. Legislative. Executive. Judicial. You learned this
                        in 5th grade and simply… let it go.</p>
                    <span class="src">Annenberg Constitution Day Survey</span>
                </div>
                <div class="stat"><span class="tag">Rights</span>
                    <div class="big">37%</div>
                    <p>couldn't name a <em>single</em> right guaranteed by the First Amendment. You use free speech
                        daily to post nonsense, but you can't name it.</p>
                    <span class="src">Annenberg Public Policy Center, 2017</span>
                </div>
                <div class="stat"><span class="tag">Literacy</span>
                    <div class="big">~54%</div>
                    <p>of American adults read below a 6th-grade level. You're reading this sentence. Can your neighbor?
                    </p>
                    <span class="src">U.S. Dept. of Education / PIAAC</span>
                </div>
                <div class="stat"><span class="tag">Spending</span>
                    <div class="big">#1 → #30</div>
                    <p>The U.S. spends more per student than almost any country on Earth — and still ranks around 30th
                        in math outcomes. It's not the budget. It's the attention.</p>
                    <span class="src">OECD / PISA</span>
                </div>
            </div>
        </div>
    </section>

    <hr class="meander">

    <!-- MANIFESTO -->
    <section class="manifesto reveal">
        <div class="wrap">
            <div class="section-head"><span class="kicker">The amitheidiot manifesto</span>
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
                        answers are free. You just… didn't.</p>
                </div>
                <div class="thesis">
                    <div class="num">03</div>
                    <p><b>The bar is on the floor.</b> Immigrants study for months, in a second language, while working
                        two jobs, to pass this test. If they can out-civics you, you owe them a nod and an apology.</p>
                </div>
                <div class="thesis">
                    <div class="num">04</div>
                    <p><b>The country isn't broken because of politicians.</b> It's broken because the people who elect
                        them can't pass a 10-question quiz. You're not the victim here. You're the variable.</p>
                </div>
                <div class="thesis">
                    <div class="num">05</div>
                    <p><b>Do better.</b> Read the thing. Learn the thing. Vote in the boring local ones. Then come back,
                        retake the test, and wipe that look off your face.</p>
                </div>
            </div>
        </div>
    </section>

    <!-- SIGNUP -->
    <section class="signup reveal" id="signup">
        <div class="wrap">
            <span class="kicker" style="background:var(--ink)">Be there when the bar hits the floor</span>
            <h2>Find out on<br>September 17.</h2>
            <p class="sub">Drop your email and we'll send the test the moment it's live. Be first in line to discover
                what you don't know. Bring a helmet.</p>
            <form class="signup-form" id="signupForm" novalidate>
                <input type="email" id="emailInput" placeholder="your.email@probably-an-idiot.com"
                    aria-label="Email address" required>
                <button type="submit" class="btn btn-primary" style="box-shadow:6px 6px 0 var(--ink)">I volunteer for
                    judgment</button>
            </form>
            <div class="form-msg" id="formMsg" aria-live="polite"></div>
            <p class="fine-print">No spam. One launch email, maybe one follow-up if you chicken out. No immigrants were
                harmed in the making of this site — they all passed. amitheidiot.com is not affiliated with USCIS, the
                Greek government, Fairleigh Dickinson University, or anyone with actual authority.</p>
        </div>
    </section>

    <footer>
        <div class="wrap">
            <div><span class="greek">ἰδιώτης</span> · amitheidiot.com © 2026 — a project of civic humiliation, lovingly.
            </div>
            <div>The Greeks started it. We're just finishing the job. 🦉</div>
        </div>
    </footer>

    <!-- SHAME ME NOW MODAL -->
    <div class="modal-overlay" id="modalOverlay" role="dialog" aria-modal="true" aria-label="Shame statistics">
        <div class="modal">
            <button class="close" id="modalClose" aria-label="Close">✕</button>
            <span class="kicker" style="background:var(--blood)">You asked for this</span>
            <h3>The Receipts.</h3>
            <div class="modal-grid">
                <div class="mstat"><span class="cat media">Media</span>
                    <div class="num">Worse than zero</div>
                    <p>In a 2009 study, people who watched Fox News scored <b>lower</b> on international-affairs
                        knowledge than people who watched <b>no news at all.</b> Tuning out entirely would have informed
                        you more.</p>
                    <span class="src">Fairleigh Dickinson Univ. PublicMind Poll, 2009</span>
                </div>
                <div class="mstat"><span class="cat lit">Literacy</span>
                    <div class="num">~54%</div>
                    <p>of American adults read below a 6th-grade level. This site is written at roughly an 8th-grade
                        level. You're already behind.</p>
                    <span class="src">U.S. Dept. of Education / PIAAC</span>
                </div>
                <div class="mstat"><span class="cat will">Willful Ignorance</span>
                    <div class="num">13%</div>
                    <p>knew the Constitution was ratified in 1788. The other 87% weren't busy. They just didn't care.
                        That's the definition of <i>idiotes</i>.</p>
                    <span class="src">Woodrow Wilson Foundation, 2018</span>
                </div>
                <div class="mstat"><span class="cat civic">Civics</span>
                    <div class="num">36%</div>
                    <p>could pass the citizenship test. That means 64% of people born here would be denied the very
                        citizenship they already have.</p>
                    <span class="src">Woodrow Wilson Foundation, 2018</span>
                </div>
                <div class="mstat"><span class="cat media">Media</span>
                    <div class="num">Least informed</div>
                    <p>Regular viewers of highly partisan cable news ranked among the <b>least informed</b> in the same
                        FDU study — behind people who skipped the news entirely.</p>
                    <span class="src">Fairleigh Dickinson Univ. PublicMind Poll</span>
                </div>
                <div class="mstat"><span class="cat civic">Civics</span>
                    <div class="num">37%</div>
                    <p>couldn't name a single First Amendment right. Free speech: exercised constantly. Named: never.
                    </p>
                    <span class="src">Annenberg Public Policy Center, 2017</span>
                </div>
                <div class="mstat"><span class="cat lit">Education</span>
                    <div class="num">#1 → #30</div>
                    <p>The U.S. spends more per student than almost anyone and still ranks ~30th in math. Money isn't
                        the problem. Paying attention is.</p>
                    <span class="src">OECD / PISA</span>
                </div>
                <div class="mstat"><span class="cat will">Willful Ignorance</span>
                    <div class="num">1 in 3</div>
                    <p>can't name the three branches of government. You learned them in 5th grade and chose to forget.
                        Actively. For decades.</p>
                    <span class="src">Annenberg Constitution Day Survey</span>
                </div>
            </div>
            <p class="footnote">Stats sourced from public surveys; numbers rounded for shame purposes. The roasts are
                original. The ignorance is not. Now go vote in something.</p>
        </div>
    </div>

    <div class="roast-toast" id="roastToast" role="status"></div>

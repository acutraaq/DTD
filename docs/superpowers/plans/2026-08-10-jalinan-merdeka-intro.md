# Jalinan Merdeka Intro Screen Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking. **Note:** this directory is NOT a git repo — skip all `git add`/`git commit` steps; just save the file with the Write/Edit tool at each step.

**Goal:** Build `dtd-jalinan-merdeka-intro.html`, a static single-page intro screen (same neon-tech visual style as `dtd-jalinan-merdeka-pin-unlock.html`) that reveals the 3 "Jalinan Merdeka" pillars in a terminal boot-sequence animation, then shows a CTA button linking to the existing pin-unlock quiz.

**Architecture:** Single static HTML file, same fixed 1920×1080 `#stage` pattern as pin-unlock.html (scaled to fit viewport via `fitStage()`), same animated circuit-weave SVG background, same fonts/CSS vars. A static `PILLARS` array drives a `setTimeout`-chained sequential reveal (mirrors the `QUESTIONS` array / `handleAnswer` sequencing pattern in pin-unlock.html). No backend, no build step, no automated tests (static page — verification is manual, in-browser).

**Tech Stack:** Vanilla HTML/CSS/JS, Google Fonts (Caveat, Archivo Black, Space Mono), inline SVG circuit animation (no external JS libraries).

## Global Constraints

- Reuse exact CSS custom properties from pin-unlock.html: `--navy:#001a70`, `--gold:#ffd700`, `--red:#d7282f`, `--cyan:#22c0de`, `--panel:#d7f7ff`, `--paper:#040b18`, `--green:#1f9d55`.
- Reuse exact fonts: Caveat (600;700), Archivo Black, Space Mono (400;700) via the same Google Fonts `<link>` tags.
- Reuse `assets/glitch-bg.png` as the `#bgImage` background (already exists in `assets/`).
- Fixed 1920×1080 stage, no scrolling, scaled to fit via `fitStage()` — same as pin-unlock.html. Do not build a scrolling/responsive-reflow layout.
- `@media (prefers-reduced-motion: reduce)` must disable stagger/typewriter timing and show all content immediately — same guard pattern as pin-unlock.html.
- Copy text is fixed (see Task 2) — do not rephrase further; it was already condensed and approved.
- CTA button must link to `dtd-jalinan-merdeka-pin-unlock.html` (relative path, same directory).
- Do not modify `dtd-jalinan-merdeka-pin-unlock.html`.

---

### Task 1: Page shell + circuit background (static, no content yet)

**Files:**
- Create: `dtd-jalinan-merdeka-intro.html`

**Interfaces:**
- Produces: DOM structure `#stage > #bgImage, #circuitSvg, .frame(.corner.tl/.tr/.bl/.br) > .content#contentEl` — later tasks (Task 2) insert boot-log/CTA markup inside `#contentEl`.
- Produces: `fitStage()` (scales `#stage` to viewport) and `buildCircuit()` (renders animated SVG circuit-weave into `#circuitSvg`) — copied verbatim from pin-unlock.html, both called once at the bottom of the script.
- Produces: CSS vars, fonts, `#stage`/`.frame`/`.corner` styling identical to pin-unlock.html.

- [ ] **Step 1: Write the static shell file**

Create `dtd-jalinan-merdeka-intro.html` with this content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>DTD Townhall 2026 - Jalinan Merdeka - Intro</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Caveat:wght@600;700&family=Archivo+Black&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
<style>
  :root{
    --navy:#001a70;
    --gold:#ffd700;
    --red:#d7282f;
    --cyan:#22c0de;
    --panel:#d7f7ff;
    --paper:#040b18;
    --green:#1f9d55;
  }
  *{box-sizing:border-box;}
  html,body{
    margin:0;
    padding:0;
    width:100%;
    height:100%;
    background:var(--paper);
    overflow:hidden;
    font-family:'Space Mono',monospace;
  }

  #stage{
    position:absolute;
    top:50%;
    left:50%;
    width:1920px;
    height:1080px;
    background:var(--paper);
    transform:translate(-50%,-50%) scale(min(calc(100vw / 1920px), calc(100vh / 1080px)));
    transform-origin:center center;
    overflow:hidden;
  }

  #bgImage{
    position:absolute;
    inset:0;
    width:1920px;
    height:1080px;
    object-fit:cover;
  }

  #circuitSvg{
    position:absolute;
    inset:0;
    width:1920px;
    height:1080px;
  }

  .frame{
    position:absolute;
    inset:0;
    width:1920px;
    height:1080px;
    background:rgba(6,20,34,.62);
    border:1px solid rgba(34,192,222,.4);
    border-radius:6px;
    box-shadow:0 0 50px rgba(34,192,222,.18), inset 0 0 70px rgba(34,192,222,.08);
  }

  .corner{
    position:absolute;
    width:64px;
    height:64px;
    pointer-events:none;
  }
  .corner::before,
  .corner::after{
    content:'';
    position:absolute;
    background:var(--cyan);
    box-shadow:0 0 10px rgba(34,192,222,.7);
  }
  .corner::before{ width:100%; height:4px; }
  .corner::after{ width:4px; height:100%; }
  .corner.tl{ top:-2px; left:-2px; }
  .corner.tl::before,
  .corner.tl::after{ top:0; left:0; }
  .corner.tr{ top:-2px; right:-2px; }
  .corner.tr::before,
  .corner.tr::after{ top:0; right:0; }
  .corner.bl{ bottom:-2px; left:-2px; }
  .corner.bl::before,
  .corner.bl::after{ bottom:0; left:0; }
  .corner.br{ bottom:-2px; right:-2px; }
  .corner.br::before,
  .corner.br::after{ bottom:0; right:0; }

  [hidden]{ display:none !important; }

  .content{
    position:absolute;
    inset:0;
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    padding:100px 80px;
    text-align:center;
  }
</style>
</head>
<body>

<div id="stage">
  <img id="bgImage" src="assets/glitch-bg.png" alt="">
  <svg id="circuitSvg" viewBox="0 0 1920 1080"></svg>

  <div class="frame">
    <span class="corner tl"></span>
    <span class="corner tr"></span>
    <span class="corner bl"></span>
    <span class="corner br"></span>

    <div class="content" id="contentEl">
      <!-- Task 2 inserts boot-log + CTA markup here -->
    </div>
  </div>
</div>

<script>
(function(){
  var stage = document.getElementById('stage');
  function fitStage(){
    var scale = Math.min(window.innerWidth / 1920, window.innerHeight / 1080);
    stage.style.transform = 'translate(-50%, -50%) scale(' + scale + ')';
  }
  window.addEventListener('resize', fitStage);
  fitStage();

  var COLORS = { cyan: '#0cc0df', gold: '#ffd700' };

  function buildCircuit(){
    var svgNS = 'http://www.w3.org/2000/svg';
    var xlinkNS = 'http://www.w3.org/1999/xlink';
    var svg = document.getElementById('circuitSvg');

    var defs = document.createElementNS(svgNS, 'defs');
    defs.innerHTML =
      '<filter id="packetGlow" x="-200%" y="-200%" width="500%" height="500%">' +
      '<feGaussianBlur stdDeviation="2.6" result="b"/>' +
      '<feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge>' +
      '</filter>';
    svg.appendChild(defs);

    var reduceMotion = window.matchMedia && window.matchMedia('(prefers-reduced-motion: reduce)').matches;
    var traceCount = 40;
    var viaMax = reduceMotion ? 0 : 80;
    var packetCount = reduceMotion ? 0 : 14;
    var cols = 16, rows = 9;
    var cellW = 1920 / cols, cellH = 1080 / rows;
    var viaBudget = viaMax;
    var traces = [];

    for (var t = 0; t < traceCount; t++){
      var gx = Math.floor(Math.random() * cols);
      var gy = Math.floor(Math.random() * rows);
      var x = gx * cellW + (Math.random() - 0.5) * cellW * 0.6 + cellW / 2;
      var y = gy * cellH + (Math.random() - 0.5) * cellH * 0.6 + cellH / 2;
      var steps = 3 + Math.floor(Math.random() * 4);
      var dir = Math.random() < 0.5 ? 'h' : 'v';
      var pts = [[x, y]];

      for (var s = 0; s < steps; s++){
        var stepLen = (cellW + cellH) / 2 * (0.6 + Math.random() * 0.9);
        if (dir === 'h'){ x += (Math.random() < 0.5 ? -1 : 1) * stepLen; }
        else { y += (Math.random() < 0.5 ? -1 : 1) * stepLen; }
        x = Math.max(20, Math.min(1900, x));
        y = Math.max(20, Math.min(1060, y));
        pts.push([x, y]);
        dir = dir === 'h' ? 'v' : 'h';
      }

      var id = 'trace' + t;
      var d = 'M' + pts.map(function(p){ return p[0].toFixed(1) + ',' + p[1].toFixed(1); }).join(' L');

      var path = document.createElementNS(svgNS, 'path');
      path.setAttribute('id', id);
      path.setAttribute('d', d);
      path.setAttribute('fill', 'none');
      path.setAttribute('stroke', COLORS.cyan);
      path.setAttribute('stroke-width', (1 + Math.random() * 0.8).toFixed(2));
      path.setAttribute('stroke-linecap', 'round');
      path.setAttribute('stroke-linejoin', 'round');
      path.setAttribute('opacity', (0.24 + Math.random() * 0.26).toFixed(2));
      svg.appendChild(path);
      traces.push({ id: id, pts: pts });

      for (var bi = 1; bi < pts.length - 1 && viaBudget > 0; bi++){
        if (Math.random() < 0.7){
          viaBudget--;
          var vx = pts[bi][0], vy = pts[bi][1];
          var r = 2.4 + Math.random() * 1.6;
          var c = document.createElementNS(svgNS, 'circle');
          c.setAttribute('cx', vx.toFixed(1));
          c.setAttribute('cy', vy.toFixed(1));
          c.setAttribute('r', r.toFixed(1));
          c.setAttribute('fill', COLORS.cyan);
          c.setAttribute('opacity', '0.35');
          svg.appendChild(c);

          var begin = (Math.random() * 3).toFixed(2) + 's';

          var animOp = document.createElementNS(svgNS, 'animate');
          animOp.setAttribute('attributeName', 'opacity');
          animOp.setAttribute('values', '0.18;0.75;0.18');
          animOp.setAttribute('dur', '3s');
          animOp.setAttribute('begin', begin);
          animOp.setAttribute('repeatCount', 'indefinite');
          c.appendChild(animOp);

          var animR = document.createElementNS(svgNS, 'animate');
          animR.setAttribute('attributeName', 'r');
          animR.setAttribute('values', r.toFixed(1) + ';' + (r * 1.5).toFixed(1) + ';' + r.toFixed(1));
          animR.setAttribute('dur', '3s');
          animR.setAttribute('begin', begin);
          animR.setAttribute('repeatCount', 'indefinite');
          c.appendChild(animR);
        }
      }
    }

    var shuffled = traces.slice().sort(function(){ return Math.random() - 0.5; });
    var chosen = shuffled.slice(0, packetCount);
    chosen.forEach(function(tr){
      var g = document.createElementNS(svgNS, 'g');

      var cyanStreak = document.createElementNS(svgNS, 'rect');
      cyanStreak.setAttribute('x', '-14');
      cyanStreak.setAttribute('y', '-1.3');
      cyanStreak.setAttribute('width', '11');
      cyanStreak.setAttribute('height', '2.6');
      cyanStreak.setAttribute('rx', '1.3');
      cyanStreak.setAttribute('fill', COLORS.cyan);
      cyanStreak.setAttribute('opacity', '0.85');
      cyanStreak.setAttribute('filter', 'url(#packetGlow)');

      var redStreak = document.createElementNS(svgNS, 'rect');
      redStreak.setAttribute('x', '3');
      redStreak.setAttribute('y', '-1.3');
      redStreak.setAttribute('width', '11');
      redStreak.setAttribute('height', '2.6');
      redStreak.setAttribute('rx', '1.3');
      redStreak.setAttribute('fill', '#d7282f');
      redStreak.setAttribute('opacity', '0.7');
      redStreak.setAttribute('filter', 'url(#packetGlow)');

      g.appendChild(cyanStreak);
      g.appendChild(redStreak);

      var n = 8 + Math.floor(Math.random() * 5);
      var pts = [];
      for (var k = 0; k < n; k++){ pts.push(Math.random()); }
      pts.sort(function(a, b){ return a - b; });
      if (Math.random() < 0.5){ pts.reverse(); }
      var keyPoints = pts.map(function(p){ return p.toFixed(3); }).join(';');
      var keyTimes = pts.map(function(_, i){ return (i / (n - 1)).toFixed(3); }).join(';');

      var mm = document.createElementNS(svgNS, 'animateMotion');
      var dur = (2.5 + Math.random() * 3.5).toFixed(2) + 's';
      mm.setAttribute('dur', dur);
      mm.setAttribute('repeatCount', 'indefinite');
      mm.setAttribute('rotate', 'auto');
      mm.setAttribute('calcMode', 'discrete');
      mm.setAttribute('keyPoints', keyPoints);
      mm.setAttribute('keyTimes', keyTimes);
      var mpath = document.createElementNS(svgNS, 'mpath');
      mpath.setAttributeNS(xlinkNS, 'href', '#' + tr.id);
      mm.appendChild(mpath);
      g.appendChild(mm);

      var flick = document.createElementNS(svgNS, 'animate');
      flick.setAttribute('attributeName', 'opacity');
      flick.setAttribute('values', '1;0.15;1;1;0.4;1');
      flick.setAttribute('dur', (0.6 + Math.random() * 0.8).toFixed(2) + 's');
      flick.setAttribute('calcMode', 'discrete');
      flick.setAttribute('repeatCount', 'indefinite');
      g.appendChild(flick);

      svg.appendChild(g);
    });
  }

  buildCircuit();
})();
</script>

</body>
</html>
```

- [ ] **Step 2: Manually verify in browser**

Open `dtd-jalinan-merdeka-intro.html` directly in a browser (double-click or `start dtd-jalinan-merdeka-intro.html` on Windows).
Expected: dark navy/paper background fills viewport, animated cyan circuit traces with drifting glow packets visible, cyan-glow frame border with corner brackets, empty center (no content yet — expected, Task 2 adds it). No console errors (open DevTools console to confirm).

---

### Task 2: Pillar boot-log content, sequencing, CTA, reduced motion

**Files:**
- Modify: `dtd-jalinan-merdeka-intro.html` (insert CSS into the existing `<style>` block, insert markup into `#contentEl`, insert JS into the existing `<script>` IIFE before the closing `buildCircuit();` call)

**Interfaces:**
- Consumes: `#contentEl` (from Task 1), `buildCircuit()` / `fitStage()` (from Task 1, already called).
- Produces: `PILLARS` array (`{title, body}` × 3), `renderPillars()` sequencing function, CTA `<a>` linking to `dtd-jalinan-merdeka-pin-unlock.html`.

- [ ] **Step 1: Add CSS for status line, boot-log panel, pillar blocks, and CTA button**

Insert this into the `<style>` block, just before the closing `</style>` tag:

```css
  .status-line{
    font-family:'Archivo Black',sans-serif;
    font-size:24px;
    letter-spacing:5px;
    color:var(--red);
    text-transform:uppercase;
    margin:0 0 34px 0;
  }

  .boot-log{
    position:relative;
    width:900px;
    min-height:360px;
    background:rgba(6,20,34,.55);
    border:1px solid rgba(34,192,222,.6);
    border-radius:6px;
    padding:40px 52px;
    text-align:left;
    box-shadow:0 0 24px rgba(34,192,222,.15), inset 0 0 34px rgba(34,192,222,.06);
  }
  .boot-log::before{
    content:'';
    position:absolute;
    top:-1px;
    left:24px;
    width:120px;
    height:3px;
    background:var(--cyan);
    box-shadow:0 0 8px rgba(34,192,222,.7);
  }

  .pillar-block{
    margin-bottom:28px;
    opacity:0;
    transform:translateY(14px);
  }
  .pillar-block:last-child{ margin-bottom:0; }
  .pillar-block.enter{
    animation:pillarIn .5s ease-out forwards;
  }
  @keyframes pillarIn{
    from{ opacity:0; transform:translateY(14px); }
    to  { opacity:1; transform:translateY(0); }
  }

  .pillar-title{
    font-family:'Archivo Black',sans-serif;
    font-size:26px;
    color:var(--gold);
    text-shadow:0 0 14px rgba(255,215,0,.5);
    margin:0 0 10px 0;
  }
  .pillar-title::before{
    content:'> ';
    color:var(--cyan);
  }

  .pillar-body{
    font-family:'Space Mono',monospace;
    font-size:17px;
    line-height:1.55;
    color:var(--panel);
    margin:0;
  }

  .cta-btn{
    display:inline-block;
    margin-top:36px;
    font-family:'Archivo Black',sans-serif;
    font-size:22px;
    letter-spacing:2px;
    text-transform:uppercase;
    text-decoration:none;
    color:var(--navy);
    background:var(--gold);
    border:2px solid var(--gold);
    border-radius:6px;
    padding:20px 44px;
    box-shadow:0 0 24px rgba(255,215,0,.5);
    opacity:0;
    transform:scale(.9);
    transition:background .15s ease, transform .1s ease, box-shadow .15s ease;
  }
  .cta-btn.enter{
    animation:ctaIn .5s cubic-bezier(.16,1,.3,1) forwards;
  }
  .cta-btn:hover{
    background:#fff2a8;
    box-shadow:0 0 34px rgba(255,215,0,.75);
  }
  .cta-btn:active{
    transform:scale(.96);
  }
  @keyframes ctaIn{
    from{ opacity:0; transform:scale(.9); }
    to  { opacity:1; transform:scale(1); }
  }

  @media (prefers-reduced-motion: reduce){
    .pillar-block,
    .cta-btn{
      animation:none !important;
      opacity:1 !important;
      transform:none !important;
    }
  }
```

- [ ] **Step 2: Add markup inside `#contentEl`**

Replace the `<!-- Task 2 inserts boot-log + CTA markup here -->` comment inside `#contentEl` with:

```html
      <div class="status-line" id="statusLine">Status: Initializing Jalinan Merdeka...</div>

      <div class="boot-log" id="bootLog"></div>

      <a class="cta-btn" id="ctaBtn" href="dtd-jalinan-merdeka-pin-unlock.html" hidden>Begin Verification &rarr;</a>
```

- [ ] **Step 3: Add `PILLARS` data + sequencing JS**

Insert this into the script's IIFE, immediately before the final `buildCircuit();` line:

```javascript
  var PILLARS = [
    {
      title: 'JALINAN — Threads of Connection',
      body: 'Cloud, AI, automation woven into one digital ecosystem — connecting every department, seamlessly.'
    },
    {
      title: 'MERDEKA — Digital Liberation',
      body: 'Freedom from manual work, legacy bottlenecks, red tape. AI and automation liberate our people for higher-value work.'
    },
    {
      title: 'KESEJAHTERAAN DINIKMATI — Well-being Enjoyed',
      body: 'Woven systems + liberated work = smooth operations, faster service, lasting peace of mind for all.'
    }
  ];

  var bootLog = document.getElementById('bootLog');
  var ctaBtn = document.getElementById('ctaBtn');
  var reduceMotion = window.matchMedia && window.matchMedia('(prefers-reduced-motion: reduce)').matches;

  function renderPillars(){
    if (reduceMotion){
      PILLARS.forEach(function(p){
        bootLog.appendChild(buildPillarEl(p));
      });
      ctaBtn.hidden = false;
      return;
    }

    var delayBetween = 1300;
    PILLARS.forEach(function(p, i){
      setTimeout(function(){
        var el = buildPillarEl(p);
        bootLog.appendChild(el);
        void el.offsetWidth;
        el.classList.add('enter');
      }, i * delayBetween);
    });

    setTimeout(function(){
      ctaBtn.hidden = false;
      void ctaBtn.offsetWidth;
      ctaBtn.classList.add('enter');
    }, PILLARS.length * delayBetween + 400);
  }

  function buildPillarEl(pillar){
    var wrap = document.createElement('div');
    wrap.className = 'pillar-block';
    var title = document.createElement('p');
    title.className = 'pillar-title';
    title.textContent = pillar.title;
    var body = document.createElement('p');
    body.className = 'pillar-body';
    body.textContent = pillar.body;
    wrap.appendChild(title);
    wrap.appendChild(body);
    return wrap;
  }

  renderPillars();

```

- [ ] **Step 4: Manually verify full flow in browser**

Open `dtd-jalinan-merdeka-intro.html` in a browser.
Expected:
- "Status: Initializing Jalinan Merdeka..." shown immediately in red, top of panel.
- The 3 pillar blocks appear one at a time (~1.3s apart), each fading/sliding up into place, gold title + light body text.
- After the 3rd block, the gold "Begin Verification →" button fades/pops in.
- Click the button → navigates to `dtd-jalinan-merdeka-pin-unlock.html` in the same tab.
- In DevTools, enable "Emulate CSS prefers-reduced-motion: reduce" (Rendering tab), reload → all 3 pillars and the CTA button appear instantly with no stagger/animation.
- Resize the browser window → stage scales to fit, no layout break, no scrollbars.

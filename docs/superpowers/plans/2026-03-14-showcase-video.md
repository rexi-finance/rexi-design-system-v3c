# Showcase Video Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `showcase.html` — a cinematic 15.5s auto-playing HTML scene for recording a product showcase video with Playwright.

**Architecture:** Single static HTML file with CSS animations + async/await JS timeline. 5 scenes play sequentially: hook → contrast → main interaction (typing + zoom + widget build) → second widget → outro. Auto-loops after 2s pause.

**Tech Stack:** HTML, CSS (keyframes + transitions), vanilla JS (async/await), Google Fonts (Figtree, Merriweather), Lucide icons CDN (pinned to 0.474.0)

**Spec:** `docs/superpowers/specs/2026-03-14-showcase-video-design.md`
**Visual reference:** `demo.html` (for layout and V3c component styles)

---

## Chunk 1: showcase.html

### Task 1: Create `showcase.html` with all 5 scenes and the JS timeline

Single-file deliverable. The file contains: CSS variables (V3c tokens), all scene DOM elements, CSS animations (gradient mesh, typing cursor, dot bounce, bar growth, path draw, zoom), and the async JS timeline.

**Files:**
- Create: `showcase.html`

- [ ] **Step 1: Create `showcase.html` with CSS + DOM structure**

The file structure:

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=1280">
  <title>Rexi Dashboards — Showcase</title>
  <link fonts>
  <script lucide@0.474.0>
  <style> ALL CSS </style>
</head>
<body>
  <div id="scene1"> ... </div>
  <div id="scene2"> ... </div>
  <div id="scene3-4"> ... </div>  ← scenes 3+4 share one browser mockup
  <div id="scene5"> ... </div>
  <script> async timeline </script>
  <!-- recording pipeline comment -->
</body>
</html>
```

**Key CSS sections to include:**

1. **V3c tokens** — copy `:root` block from `demo.html` verbatim
2. **Base** — `body { width:1280px; height:720px; overflow:hidden; background:#1a1a1a; font-family:'Figtree'; }`
3. **Scene system** — `.scene { position:absolute; inset:0; opacity:0; transition:opacity 300ms; } .scene.active { opacity:1; }`
4. **Easing variables** — `--ease-enter: cubic-bezier(0.2,0,0,1); --ease-exit: cubic-bezier(0.4,0,1,1); --ease-spring: cubic-bezier(0.34,1.56,0.64,1);`
5. **Scene 1** — gradient mesh keyframes (`@keyframes meshDrift`), text entry classes
6. **Scene 2** — browser mockup (cluttered), filter, overlay text
7. **Scene 3-4** — browser mockup (rexi layout), sidebar, dashboard, chat, skeleton widgets, zoom, spotlight
8. **Widget build** — bar chart SVG, area chart SVG, stroke-dashoffset animations
9. **Typing** — cursor blink keyframe, typewriter effect classes
10. **Scene 5** — outro zoom-out, center text

**Key DOM sections:**

**Scene 1 (Hook):**
```html
<div id="scene1" class="scene">
  <div class="mesh-bg"></div>
  <div class="hook-text">
    <div class="hook-eyebrow">INTRODUCING</div>
    <div class="hook-title">Rexi Dashboards</div>
    <div class="hook-subtitle">Your dashboard. Your words.</div>
  </div>
</div>
```

**Scene 2 (Contrast):**
```html
<div id="scene2" class="scene">
  <div class="contrast-browser">
    <div class="browser-chrome"><!-- dots + url --></div>
    <div class="contrast-content" style="filter:saturate(0.3) brightness(0.6)">
      <!-- 6 cramped rectangles in 3x2 grid -->
    </div>
  </div>
  <div class="contrast-text">Building dashboards takes hours.</div>
</div>
```

**Scene 3-4 (Main + Second widget):**
```html
<div id="scene3" class="scene">
  <div class="browser-wrap">
    <div class="browser-chrome"><!-- dots + url --></div>
    <div class="browser-content">
      <!-- 64px sidebar -->
      <div class="sc-sidebar">...</div>
      <!-- Dashboard -->
      <div class="sc-dashboard">
        <div class="sc-dash-header">Stripe vs Modern Treasury</div>
        <div class="sc-widget-grid">
          <div class="sc-skeleton" id="skel1"></div>  <!-- becomes bar chart -->
          <div class="sc-skeleton" id="skel2"></div>  <!-- becomes area chart -->
        </div>
      </div>
      <!-- Chat panel -->
      <div class="sc-chat">
        <div class="sc-chat-header">...</div>
        <div class="sc-chat-messages" id="chatMessages"></div>
        <div class="sc-chat-input">
          <span id="inputText"></span>
          <span class="cursor-blink"></span>
        </div>
      </div>
    </div>
    <!-- Spotlight overlay -->
    <div class="spotlight" id="spotlight"></div>
  </div>
  <!-- Callout badges -->
  <div class="callout" id="callout1">GENERATED IN 3S</div>
  <div class="callout" id="callout2">Any question. Any chart.</div>
  <!-- Outro overlay -->
  <div class="outro-overlay" id="outroOverlay">
    <div class="outro-title">Rexi Dashboards</div>
    <div class="outro-url">app.rexi.finance</div>
  </div>
</div>
```

**JS timeline structure:**

```javascript
const delay = ms => new Promise(r => setTimeout(r, ms));

function show(id) { document.getElementById(id).classList.add('active'); }
function hide(id) { document.getElementById(id).classList.remove('active'); }

async function typeText(elementId, text, charDelay = 60) {
  const el = document.getElementById(elementId);
  for (const char of text) {
    el.textContent += char;
    await delay(charDelay);
  }
}

function addChatBubble(type, content) {
  const area = document.getElementById('chatMessages');
  const msg = document.createElement('div');
  msg.className = `sc-msg ${type}`;
  msg.innerHTML = content;
  area.appendChild(msg);
  area.scrollTop = area.scrollHeight;
}

function addThinkingDots() {
  // Returns the element so it can be removed later
}

async function buildBarChart(containerId) {
  // 1. Show card border (200ms)
  // 2. Show title (200ms)
  // 3. Animate axes via stroke-dashoffset (400ms)
  // 4. Grow 6 bars with staggered spring (80ms each)
  // 5. Scale in "+23%" badge
}

async function buildAreaChart(containerId) {
  // 1. Show card border (200ms)
  // 2. Show title (200ms)
  // 3. Animate axes (300ms)
  // 4. Draw area path via stroke-dashoffset (600ms)
  // 5. Fade in data points
}

async function scene1_hook() {
  show('scene1');
  await delay(200);
  // Stagger text entries 200ms apart
  document.querySelector('.hook-eyebrow').classList.add('visible');
  await delay(200);
  document.querySelector('.hook-title').classList.add('visible');
  await delay(200);
  document.querySelector('.hook-subtitle').classList.add('visible');
  await delay(1600); // hold
  document.getElementById('scene1').classList.add('exiting');
  await delay(300);
  hide('scene1');
}

async function scene2_contrast() {
  show('scene2');
  await delay(1200);
  document.getElementById('scene2').classList.add('exiting');
  await delay(300);
  hide('scene2');
}

async function scene3_main() {
  show('scene3');
  // +300ms cursor blink (CSS handles this)
  await delay(600);
  // +600ms typing
  document.querySelector('.cursor-blink').style.display = 'none';
  await typeText('inputText', 'Show me revenue by region this quarter', 60);
  await delay(200);
  // +2800ms send message
  document.getElementById('inputText').textContent = '';
  addChatBubble('user', '..avatar.. Show me revenue by region this quarter');
  await delay(200);
  // +3000ms AI thinking
  const dots = addThinkingDots();
  await delay(500);
  // +3500ms ZOOM IN
  document.querySelector('.browser-wrap').classList.add('zoomed');
  document.getElementById('spotlight').classList.add('active');
  await delay(500);
  // +4000ms build widget
  dots.remove();
  await buildBarChart('skel1');
  await delay(0);
  // +5200ms ZOOM OUT
  document.querySelector('.browser-wrap').classList.remove('zoomed');
  document.getElementById('spotlight').classList.remove('active');
  await delay(200);
  // +5400ms AI response
  addChatBubble('ai', "..avatar.. Here's your revenue breakdown by region...");
  await delay(300);
  // +5700ms callout
  document.getElementById('callout1').classList.add('visible');
  await delay(300);
}

async function scene4_second() {
  document.getElementById('callout1').classList.remove('visible');
  // Type faster
  await typeText('inputText', 'Sales forecast next quarter', 40);
  await delay(200);
  document.getElementById('inputText').textContent = '';
  addChatBubble('user', '..avatar.. Sales forecast next quarter');
  await delay(200);
  const dots = addThinkingDots();
  await delay(200);
  dots.remove();
  await buildAreaChart('skel2');
  await delay(200);
  document.getElementById('callout2').classList.add('visible');
  await delay(300);
}

async function scene5_outro() {
  document.getElementById('callout2').classList.remove('visible');
  document.querySelector('.browser-wrap').classList.add('scaled-down');
  await delay(400);
  document.getElementById('outroOverlay').classList.add('visible');
  await delay(1600);
  document.getElementById('scene3').classList.add('exiting');
  await delay(500);
}

async function runShowcase() {
  await scene1_hook();
  await scene2_contrast();
  await scene3_main();
  await scene4_second();
  await scene5_outro();
  await delay(2000);
  // Reset all state and loop
  resetAll();
  runShowcase();
}

// Init
lucide.createIcons();
runShowcase();
```

The HTML comment block at the end contains the Playwright `record.js` script, the Suno audio prompt, and the ffmpeg merge command — copied verbatim from the spec.

- [ ] **Step 2: Open in browser and verify all 5 scenes play correctly**

Open `showcase.html` in Chrome via `file://` protocol. Watch the full 17.5s loop and verify:
- Scene 1: gradient mesh animates, text staggers in, fades out
- Scene 2: cluttered browser appears desaturated, text shows, fades out
- Scene 3: browser mockup with 3-column layout, cursor blinks, typing works, message sends, AI dots, zoom in, bar chart builds with 6 bars + badge, zoom out, AI response, callout
- Scene 4: faster typing, second message, area chart builds, callout
- Scene 5: browser scales down, outro text fades in, everything fades out
- Loop restarts after 2s black pause
- All easing curves are correct (no `ease-in-out` anywhere)

- [ ] **Step 3: Commit**

```bash
git add showcase.html
git commit -m "feat: add cinematic showcase video (15.5s auto-playing HTML scene)"
```

---

## Chunk 2: Push & Verify

### Task 2: Push to GitHub Pages

- [ ] **Step 1: Push**

```bash
git push origin main
```

- [ ] **Step 2: Verify showcase.html is accessible**

```bash
curl -s -o /dev/null -w "%{http_code}" https://rexi-finance.github.io/rexi-design-system-v3c/showcase.html
```

Expected: `200`

Note: The showcase is NOT linked from the site nav (per spec: "No site nav — this is a standalone cinematic piece"). It's accessible by direct URL only.

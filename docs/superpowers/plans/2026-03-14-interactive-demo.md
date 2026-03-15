# Interactive Demo Page — Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a click-to-advance interactive demo page (`demo.html`) simulating rexi-app's AI-driven widget creation flow using exact V3c design system components.

**Architecture:** Single static HTML file with all 8 demo steps pre-rendered but hidden via `data-step` attributes. A document-level click/keyboard handler advances `currentStep`, toggling `step-visible` classes with CSS transitions. No build tools, no dependencies beyond Lucide (CDN) and Google Fonts.

**Tech Stack:** HTML, CSS, vanilla JavaScript, Lucide icons (CDN), Google Fonts (Figtree, Merriweather)

**Spec:** `docs/superpowers/specs/2026-03-14-interactive-demo-design.md`
**Approved mockup (static snapshot at step 6):** `.superpowers/brainstorm/4888-1773531857/design-v3.html`

---

## Chunk 1: Demo Page

### Task 1: Create `demo.html` with full layout, all 8 steps, and interaction logic

This is a single-file deliverable. The approved mockup (`design-v3.html`) contains all the CSS and component HTML needed — it shows a static snapshot at step 6. The task is to:

1. Take the mockup's CSS verbatim
2. Add the `data-step` hide/show CSS and JS
3. Expand the HTML to include all 8 steps (the mockup shows steps 2-6, we need to add steps 1, 7, 8)
4. Add keyboard and click interaction logic
5. Wire up the progress bar

**Files:**
- Create: `demo.html`

- [ ] **Step 1: Create `demo.html` with the full page structure**

Copy the CSS from the approved mockup (`design-v3.html`) exactly. Add the step-visibility CSS. Build the full HTML with all 8 steps using `data-step` attributes.

The page structure is:
```
<body>
  <nav class="site-nav">          ← site nav (Demo link active)
  <div class="app-layout">
    <div class="sidebar">         ← 64px collapsed sidebar
    <div class="dashboard">       ← flex:1 dashboard area
      <div class="dash-header">   ← title + "Add Widget" button
      <div class="widget-grid">   ← 2-col grid with widget cards
    <div class="chat-panel">      ← 420px chat panel
      <div class="chat-header-bar">
      <div class="chat-messages-area"> ← all chat messages with data-step
      <div class="chat-input-bar">
  <div class="progress-bar">      ← bottom progress dots + label
```

**CSS additions beyond the mockup:**

```css
/* Step visibility system — use display:none + keyframe animation
   to avoid max-height timing issues */
[data-step] {
  display: none;
}
[data-step].step-visible {
  display: flex;
  animation: fadeSlideIn 0.4s ease forwards;
}
/* Chat messages need flex for avatar + bubble layout */
.chat-msg[data-step].step-visible { display: flex; }
/* Widget cards need default display */
.widget-card[data-step].step-visible { display: flex; }
@keyframes fadeSlideIn {
  from { opacity: 0; transform: translateY(8px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Ghost → solid widget transition */
.widget-card.ghost {
  border: 1px dashed rgba(59,130,246,0.35);
  background: linear-gradient(135deg, rgba(59,130,246,0.04) 0%, rgba(59,130,246,0.01) 50%, rgba(59,130,246,0.02) 100%);
  transition: all 0.5s ease;
}
.widget-card.ghost.solid {
  border: 1px solid rgba(255,255,255,0.04);
  background: linear-gradient(135deg, rgba(255,255,255,0.05) 0%, rgba(255,255,255,0.01) 50%, rgba(255,255,255,0.03) 100%);
}

/* Empty grid placeholder */
.widget-grid-empty {
  grid-column: span 2;
  display: flex; align-items: center; justify-content: center;
  min-height: 200px;
  border: 1px dashed rgba(255,255,255,0.08);
  border-radius: 12px;
  color: var(--text-faint);
  font-size: 13px;
}

/* Welcome state */
.chat-welcome {
  flex: 1; display: flex; flex-direction: column;
  align-items: center; justify-content: center; gap: 12px;
  text-align: center; padding: 40px;
}
.chat-welcome-icon {
  width: 48px; height: 48px; border-radius: 16px;
  background: linear-gradient(135deg, rgba(139,92,246,0.15), rgba(59,130,246,0.15));
  display: flex; align-items: center; justify-content: center;
}
.chat-welcome-icon svg { width: 24px; height: 24px; color: var(--accent-purple); }
.chat-welcome-title { font-size: 16px; font-weight: 700; color: var(--text-bright); }
.chat-welcome-subtitle { font-size: 13px; color: var(--text-dim); max-width: 280px; line-height: 1.6; }

/* Restart button */
.restart-btn {
  padding: 8px 20px; border-radius: 12px; font-family: 'Figtree', sans-serif;
  font-size: 12px; font-weight: 600; cursor: pointer; border: 1px solid rgba(59,130,246,0.30);
  background: var(--accent-blue-muted); color: var(--accent-blue);
  display: inline-flex; align-items: center; gap: 6px; transition: all var(--transition-fast);
}
.restart-btn:hover { background: rgba(59,130,246,0.15); }
.restart-btn svg { width: 14px; height: 14px; }
```

**HTML — 8-step content map:**

Dashboard widget grid (elements appear/transition based on steps):
```html
<!-- Always visible empty placeholder, hidden when first widget appears -->
<div class="widget-grid-empty" id="grid-empty">
  <span>Widgets will appear here as you build...</span>
</div>

<!-- Widget 1: Match Rate KPI — appears as ghost at step 5, solidifies at step 6 -->
<div class="widget-card ghost" data-step="5" id="widget-kpi-match">
  <div class="widget-card-header">...</div>
  <div class="widget-card-content">KPI: 96.8%</div>
</div>

<!-- Widget 2: Total Unmatched KPI — appears as ghost at step 7, solidifies at step 8 -->
<div class="widget-card ghost" data-step="7" id="widget-kpi-unmatched">
  <div class="widget-card-header">...</div>
  <div class="widget-card-content">KPI: 142</div>
</div>

<!-- Widget 3: Area chart — appears as ghost at step 7, solidifies at step 8 -->
<div class="widget-card ghost" data-step="7" id="widget-chart" style="grid-column:span 2;">
  <div class="widget-card-header">...</div>
  <div class="widget-card-content">SVG area chart</div>
</div>
```

Chat messages (each with `data-step`):
```html
<!-- Step 1: Welcome state (visible at step 1, hidden when step 2+ starts) -->
<div class="chat-welcome" id="chat-welcome">...</div>

<!-- Step 2: User message -->
<div class="chat-msg user" data-step="2">
  "Build me a dashboard for the Stripe vs Modern Treasury reconciliation"
</div>

<!-- Step 3: AI progress steps -->
<div class="chat-msg ai" data-step="3">
  Progress steps: found recon, loaded schema, queried stats (all done)
</div>

<!-- Step 4: AI markdown briefing -->
<div class="chat-msg ai" data-step="4">
  Stat row: 96.8% match | 142 unmatched | 23 exceptions
  + narrative text
</div>

<!-- Step 5: AI suggests KPI with sparkline + decision buttons -->
<div class="chat-msg ai" data-step="5">
  Inline KPI card with sparkline SVG + Add/Skip buttons
</div>

<!-- Step 6: User "Add it" + AI inline table + next suggestion -->
<div class="chat-msg user" data-step="6">"Add it"</div>
<div class="chat-msg ai" data-step="6">
  Inline table (Side, Count, Reason, Impact) + Add/Skip buttons
</div>

<!-- Step 7: User "Add it too" + AI suggests area chart -->
<div class="chat-msg user" data-step="7">"Add it too"</div>
<div class="chat-msg ai" data-step="7">
  Suggests "Match Rate — Last 7 Days" area chart + Add/Skip buttons
</div>

<!-- Step 8: User "Yes, add the chart" + AI summary -->
<div class="chat-msg user" data-step="8">"Yes, add the chart"</div>
<div class="chat-msg ai" data-step="8">
  "Your reconciliation dashboard is ready. I've added a Match Rate KPI,
  Total Unmatched count, and a 7-day trend chart. You can ask me to
  adjust any widget at any time."
  + Restart Demo button
</div>
```

**JavaScript — interaction controller:**

```javascript
<script>
  lucide.createIcons();

  const TOTAL_STEPS = 8;
  let currentStep = 1;

  function updateDemo() {
    // Show/hide step elements
    document.querySelectorAll('[data-step]').forEach(el => {
      const step = parseInt(el.dataset.step);
      el.classList.toggle('step-visible', step <= currentStep);
    });

    // Welcome state: visible only at step 1
    const welcome = document.getElementById('chat-welcome');
    if (welcome) welcome.style.display = currentStep >= 2 ? 'none' : '';

    // Grid empty placeholder: hide when widgets appear (step 5+)
    const gridEmpty = document.getElementById('grid-empty');
    if (gridEmpty) gridEmpty.style.display = currentStep >= 5 ? 'none' : '';

    // Ghost → solid transitions
    // Widget 1 (match rate): ghost at 5, solid at 6
    const w1 = document.getElementById('widget-kpi-match');
    if (w1) w1.classList.toggle('solid', currentStep >= 6);

    // Widget 2 (unmatched): ghost at 7, solid at 8
    const w2 = document.getElementById('widget-kpi-unmatched');
    if (w2) w2.classList.toggle('solid', currentStep >= 8);

    // Widget 3 (chart): ghost at 7, solid at 8
    const w3 = document.getElementById('widget-chart');
    if (w3) w3.classList.toggle('solid', currentStep >= 8);

    // Update progress bar
    document.querySelectorAll('.progress-dot').forEach((dot, i) => {
      dot.classList.remove('done', 'active');
      if (i + 1 < currentStep) dot.classList.add('done');
      else if (i + 1 === currentStep) dot.classList.add('active');
    });
    const label = document.querySelector('.progress-label');
    if (label) label.textContent = `Step ${currentStep} of ${TOTAL_STEPS}`;

    // Auto-scroll chat to bottom
    const chatArea = document.querySelector('.chat-messages-area');
    if (chatArea) chatArea.scrollTop = chatArea.scrollHeight;
  }

  function advance() {
    if (currentStep < TOTAL_STEPS) {
      currentStep++;
      updateDemo();
    }
  }

  function goBack() {
    if (currentStep > 1) {
      currentStep--;
      updateDemo();
    }
  }

  function restartDemo(e) {
    e.stopPropagation();
    currentStep = 1;
    updateDemo();
  }

  // Click anywhere to advance
  document.addEventListener('click', (e) => {
    if (e.target.closest('.restart-btn')) return;
    if (e.target.closest('.site-nav')) return;
    advance();
  });

  // Keyboard navigation
  document.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowRight' || e.key === ' ') { e.preventDefault(); advance(); }
    if (e.key === 'ArrowLeft') { e.preventDefault(); goBack(); }
  });

  // Initialize
  updateDemo();
</script>
```

- [ ] **Step 2: Verify the demo works locally**

Open `demo.html` in a browser. Click through all 8 steps and verify:
- Step 1: welcome state + empty grid
- Step 2: user message appears
- Step 3: AI progress steps appear
- Step 4: AI stat briefing appears
- Step 5: AI KPI suggestion appears + ghost widget in grid
- Step 6: user "Add it" + AI table appear + widget solidifies
- Step 7: user "Add it too" + AI chart suggestion + 2nd widget solid + ghost chart
- Step 8: user "Yes, add the chart" + AI summary + all widgets solid + Restart button
- Keyboard (arrows, space) works
- Restart button resets to step 1
- Progress dots update correctly

- [ ] **Step 3: Commit**

```bash
git add demo.html
git commit -m "feat: add interactive demo page with 8-step click-to-advance flow"
```

---

## Chunk 2: Nav Updates

### Task 2: Add "Demo" link to site navigation across all pages

Add a "Demo" link to the nav in: `index.html`, `reference.html`, `process.html`, and all 12 `process/*.html` files.

**Files:**
- Modify: `index.html` (nav section)
- Modify: `reference.html` (nav section, inline styles)
- Modify: `process.html` (nav section)
- Modify: `process/01-surfaces.html` through `process/12-chat-ui.html` (inline nav, all 12 files)

- [ ] **Step 1: Update `index.html` nav**

Find the nav links section and add a Demo link before GitHub:

```html
<!-- Before -->
<a href="process.html">Process</a>
<a href="https://github.com/rexi-finance/rexi-design-system-v3c" target="_blank">GitHub</a>

<!-- After -->
<a href="process.html">Process</a>
<a href="demo.html">Demo</a>
<a href="https://github.com/rexi-finance/rexi-design-system-v3c" target="_blank">GitHub</a>
```

- [ ] **Step 2: Update `process.html` nav**

Same pattern — add Demo link before GitHub:

```html
<!-- Before -->
<a href="process.html" class="active">Process</a>
<a href="https://github.com/rexi-finance/rexi-design-system-v3c" target="_blank">GitHub</a>

<!-- After -->
<a href="process.html" class="active">Process</a>
<a href="demo.html">Demo</a>
<a href="https://github.com/rexi-finance/rexi-design-system-v3c" target="_blank">GitHub</a>
```

- [ ] **Step 3: Update `reference.html` nav**

The nav uses inline styles. Add a Demo link with the inactive style (matching existing links):

```html
<!-- Add before the GitHub link -->
<a href="demo.html" style="font-size:11px;font-weight:500;color:rgba(255,255,255,0.45);text-decoration:none;padding:5px 12px;border-radius:6px;transition:all 120ms ease;">Demo</a>
```

- [ ] **Step 4: Update all 12 `process/*.html` nav bars**

Each process subpage has a right-side nav with Reference and Process buttons. Add a Demo link using the same inline style pattern. Use a script to inject into all 12 files:

```bash
# For each process/*.html, find the closing </div> of the right-side nav
# and insert the Demo link before the Process link
```

The exact insertion: add `<a href="../demo.html" style="font-size:12px;font-weight:500;color:rgba(255,255,255,0.45);padding:6px 14px;border-radius:6px;text-decoration:none;">Demo</a>` to the right-side nav buttons.

- [ ] **Step 5: Verify all nav links work**

```bash
# Check that all files have the Demo link
grep -l "demo.html" *.html process/*.html
```

Expected: 15 files (index.html, reference.html, process.html, + 12 process subpages)

- [ ] **Step 6: Commit nav updates**

```bash
git add index.html reference.html process.html process/*.html
git commit -m "feat: add Demo link to site navigation across all pages"
```

---

## Chunk 3: Deploy

### Task 3: Push to GitHub Pages and verify

- [ ] **Step 1: Push to main**

```bash
git push origin main
```

- [ ] **Step 2: Wait for GitHub Pages deployment**

```bash
# Check deployment status
gh api repos/rexi-finance/rexi-design-system-v3c/pages/builds --jq '.[0].status'
```

- [ ] **Step 3: Verify demo page is live**

```bash
curl -s -o /dev/null -w "%{http_code}" https://rexi-finance.github.io/rexi-design-system-v3c/demo.html
```

Expected: `200`

- [ ] **Step 4: Verify nav links from other pages**

```bash
for page in "" "reference.html" "process.html" "process/01-surfaces.html"; do
  curl -s "https://rexi-finance.github.io/rexi-design-system-v3c/$page" | grep -c "demo.html"
done
```

Expected: each returns `1` (one Demo link per page)

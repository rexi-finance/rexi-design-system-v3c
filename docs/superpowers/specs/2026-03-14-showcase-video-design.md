# Showcase Video — Design Spec

**Date:** 2026-03-14
**Status:** Approved
**Repo:** `rexi-finance/rexi-design-system-v3c` (GitHub Pages)
**File:** `showcase.html` in repo root

## Purpose

A ~16-second cinematic auto-playing HTML scene (15.5s content + 2s black pause = 17.5s per loop) recorded with Playwright to produce a .mp4 showcase video for Twitter, LinkedIn, and web. Demonstrates the "wow moment" of Rexi Dashboards: user types in natural language → AI generates a widget in real time. Playwright recording should capture exactly 17500ms for one full loop, or 15500ms for the content without the black pause.

## Design System Tokens

All visuals use V3c Hybrid Glass tokens exclusively. No external colors, fonts, or styles.

```
colors:
  background:   #1a1a1a
  bgElevated:   #1e1e1e
  glass-bg-1:   rgba(255,255,255,0.03)
  glass-bg-2:   rgba(255,255,255,0.04)
  glass-bg-3:   rgba(255,255,255,0.06)
  glass-bg-4:   rgba(255,255,255,0.10)
  glass-border:  rgba(255,255,255,0.06)
  glass-border-subtle: rgba(255,255,255,0.04)
  text-bright:  rgba(255,255,255,0.95)
  text:         #e5e5e5
  text-muted:   rgba(255,255,255,0.45)
  text-dim:     rgba(255,255,255,0.25)
  text-faint:   rgba(255,255,255,0.16)
  accent-blue:  #3b82f6
  accent-purple:#8b5cf6
  accent-emerald:#34d399
  accent-red:   #f87171
  accent-amber: #fbbf24

typography:
  display: 'Figtree', sans-serif
  accent:  'Merriweather', serif
  weights: 400, 500, 600, 700, 900

radii:
  xl: 12px (browser mockup, cards, buttons)

motion (video-specific):
  easingEnter:  cubic-bezier(0.2, 0, 0, 1)
  easingExit:   cubic-bezier(0.4, 0, 1, 1)
  easingSpring: cubic-bezier(0.34, 1.56, 0.64, 1)
```

## Product Details

- **Product name:** Rexi Dashboards
- **Tagline:** "Your dashboard. Your words."
- **Browser URL:** app.rexi.finance
- **Viewport:** 1280x720px, overflow hidden

## Visual Reference

The browser mockup interior reuses the exact layout and component styles from `demo.html`:
- 64px collapsed sidebar (Merriweather "Rx", Lucide icons, glass active state)
- Dashboard grid (2-column, widget cards with headers: drag handle, title, info/menu icons)
- 420px chat panel (purple-blue gradient header icon, chat bubbles with V3c styling)
- Widget cards: glass gradient background, 12px radius, 1px border, card shadow
- Ghost widgets: dashed blue border variant

## Architecture

Single static HTML file. All CSS in `<style>`, all JS in `<script>` at body end. Zero dependencies except Google Fonts (Figtree + Merriweather) and Lucide icons CDN (pin to `lucide@0.474.0` for reproducible recordings).

**JS pattern:** Promise-based timeline with `async/await` and `delay(ms)` helper. Reads like a screenplay — each line corresponds to a timing cue.

```javascript
async function runShowcase() {
  await scene1_hook();    // 0 → 2500ms
  await scene2_contrast(); // 2500 → 4000ms
  await scene3_main();    // 4000 → 10000ms
  await scene4_second();  // 10000 → 13000ms
  await scene5_outro();   // 13000 → 15500ms
  await delay(2000);      // pause
  restartShowcase();      // loop
}
```

## Scene Breakdown

### Scene 1 — Hook (2500ms)

**Background:** Full-screen `#1a1a1a` with two animated radial gradients — `#3b82f6` at ~10% opacity (top-right, drifting) and `#8b5cf6` at ~8% opacity (bottom-left, drifting). CSS `@keyframes` with 8s loop for ambient movement.

**Text entry** (centered, staggered 200ms apart):
1. Eyebrow: "INTRODUCING" — Figtree 600, 12px, uppercase, letter-spacing 3px, `#8b5cf6`
2. Title: "Rexi Dashboards" — Figtree 900, 52px, `rgba(255,255,255,0.95)`, letter-spacing -0.03em
3. Subtitle: "Your dashboard. Your words." — Figtree 400, 18px, `rgba(255,255,255,0.45)`

**Animation:** Each text element: `opacity:0, translateY(16px)` → `opacity:1, translateY(0)` with `cubic-bezier(0.2,0,0,1)`, 600ms duration.

**Exit:** All elements fade out at 2200ms (300ms, `cubic-bezier(0.4,0,1,1)`).

### Scene 2 — Contrast (1500ms)

**Browser mockup** (900px wide, centered): Shows a static cluttered dashboard — 6 tightly packed rectangles (glass-bg-2 fill, glass-border) in a messy 3x2 grid with inconsistent sizes and no breathing room. No widget headers, no titles — just numbered placeholder boxes with random bar fragments inside. Intentionally generic and overwhelming.

Content inside the mockup:
- Browser chrome bar (same as Scene 3: dots + "app.rexi.finance")
- 6 cramped rectangles: `background: var(--glass-bg-2); border: 1px solid var(--glass-border); border-radius: 8px`
- Inside some rectangles: faint horizontal lines (simulating bad charts), small text blocks in `text-faint`
- No sidebar, no chat panel — just a wall of widgets

**Filter:** `saturate(0.3) brightness(0.6)` applied on the browser content area.

**Overlay text:** "Building dashboards takes hours." — Figtree 600, 24px, `rgba(255,255,255,0.95)`, centered below browser.

**Timing:** Hold 1200ms, then 300ms fade out (exit easing).

### Scene 3 — Main Interaction (6000ms)

**Browser mockup (`.browser-wrap`):**
- Width: 900px, centered
- `border-radius: 12px`
- `border: 1px solid rgba(255,255,255,0.06)`
- `box-shadow: 0 40px 120px rgba(0,0,0,0.8), 0 0 80px rgba(59,130,246,0.08)`
- Chrome bar: 3 dots (#f87171, #fbbf24, #34d399 — V3c tokens) + URL "app.rexi.finance" in text-dim

**Interior layout** (three columns, from demo.html — NO site nav bar):
- 64px sidebar: Merriweather "Rx" logo, icon nav, glass active state (same as demo.html but without the 44px site nav above)
- Dashboard area (flex:1, ~60% of remaining): header "Stripe vs Modern Treasury" + 2 skeleton widget placeholders (glass-bg-1, dashed border, pulsing shimmer animation)
- Chat panel (340px, ~40%): header ("Rexi AI", purple-blue gradient icon, "ONLINE" badge), empty messages area, input bar with blinking cursor

**Choreography (relative to scene start):**

| Offset | Action |
|--------|--------|
| +300ms | Cursor blinks in chat input (1px wide `::after` on input element, `rgba(255,255,255,0.45)`, `@keyframes blink { 50% { opacity:0 } }` at 1s interval) |
| +600ms | Typing starts: "Show me revenue by region this quarter" (40 chars at 60ms/char = 2400ms total, cursor hides when typing starts) |
| +2800ms | Message appears as user bubble (slide up, enter easing) |
| +3000ms | AI bubble appears with 3-dot bounce animation |
| +3500ms | **ZOOM IN**: `.browser-wrap` → `transform:scale(1.4)`, `transform-origin: 35% 60%` (targeting the top-left skeleton widget area). Spotlight overlay: `radial-gradient(circle at 35% 60%, transparent 30%, rgba(0,0,0,0.6) 100%)`. 800ms transition with deceleration. |
| +4000ms | Widget builds in empty area: |
|         | 1. Card border fades in (200ms) |
|         | 2. Title "Revenue by Region" appears (200ms) |
|         | 3. X/Y axes draw via `stroke-dashoffset` (400ms) |
|         | 4. 6 bars grow from height:0 with spring easing, 80ms stagger. Bars represent regions with relative heights: North America 85%, Europe 70%, Asia-Pacific 55%, Latin America 40%, Middle East 30%, Africa 20%. Colors: alternating `#3b82f6` and `rgba(59,130,246,0.5)`. |
|         | 5. "+23%" badge top-right of chart, `#34d399` text, scales in with spring (scale 0→1) |
| +5200ms | **ZOOM OUT**: `transform:scale(1)`, 800ms deceleration. Spotlight fades. The AI bubble and callout animate during zoom-out — they appear overlaid on the zooming browser. |
| +5400ms | AI response bubble: "Here's your revenue breakdown by region..." |
| +5700ms | Callout badge: "GENERATED IN 3S" — `#3b82f6`, uppercase, Figtree 600, 11px, positioned below the browser mockup |

### Scene 4 — Second Widget (3000ms)

Continues from Scene 3 browser mockup (no scene transition).

| Offset | Action |
|--------|--------|
| +0ms | User typing: "Sales forecast next quarter" (faster: 40ms/char) |
| +800ms | Message sends as user bubble |
| +1000ms | AI thinking dots |
| +1200ms | Area chart widget builds in 2nd skeleton slot (1500ms — faster than Scene 3): |
|         | Card border → title "Sales Forecast" → X/Y axes draw → area fill path draws via `stroke-dashoffset` (single `<path>` with emerald gradient fill + 2px emerald stroke, same as demo.html area chart) → data points fade in |
| +2700ms | Callout: "Any question. Any chart." — `rgba(255,255,255,0.95)`, Figtree 600, 14px, centered below browser |

Scene 4 has no explicit exit — it flows directly into Scene 5's zoom-out. All Scene 3+4 elements remain visible as Scene 5 begins scaling down the browser.

**Chat panel scroll:** The chat panel uses `overflow-y:auto`. As messages accumulate, JS sets `scrollTop = scrollHeight` after each new bubble appears. The initial state (Scene 3 start) has no prior messages — just the empty chat with the input bar.

### Scene 5 — Outro (2500ms)

| Offset | Action |
|--------|--------|
| +0ms | Browser mockup scales to 60% (`transform:scale(0.6)`), showing full dashboard with both widgets |
| +400ms | Center text fades in (enter easing): |
|        | "Rexi Dashboards" — Figtree 900, 36px, `rgba(255,255,255,0.95)` |
|        | "app.rexi.finance" — Figtree 400, 14px, `rgba(255,255,255,0.45)` |
| +2000ms | Everything fades out (500ms, exit easing) |

**After outro:** 2000ms pause at black, then auto-loop.

## Easing Rules

| Context | Curve | Never |
|---------|-------|-------|
| Entrances | `cubic-bezier(0.2, 0, 0, 1)` | ease-in-out |
| Exits | `cubic-bezier(0.4, 0, 1, 1)` | ease-in-out |
| Springs (bars, badges) | `cubic-bezier(0.34, 1.56, 0.64, 1)` | ease-in-out |
| Zoom in/out | `cubic-bezier(0.2, 0, 0, 1)` (deceleration) | linear |

## Zoom Cinematográfico

- Always CSS `transform` on `.browser-wrap`, never browser zoom
- `.browser-wrap.zoomed` sets `transform:scale(1.4)` + `transform-origin: 35% 60%`
- Spotlight: absolute-positioned overlay with `radial-gradient(circle at 35% 60%, transparent 30%, rgba(0,0,0,0.6) 100%)`
- Transition: 800ms with deceleration curve

## Browser Mockup Specs

- `border-radius: 12px` (V3c --radius-xl)
- `border: 1px solid rgba(255,255,255,0.06)` (--glass-border)
- `box-shadow: 0 40px 120px rgba(0,0,0,0.8), 0 0 80px rgba(59,130,246,0.08)` (deep shadow + blue glow)
- Chrome dots: `#f87171` / `#fbbf24` / `#34d399` (V3c accent tokens)
- URL bar: "app.rexi.finance" in `rgba(255,255,255,0.25)` (--text-dim)

## File Structure

```
rexi-design-system-v3c/
├── showcase.html      ← The cinematic showcase (new)
├── demo.html          ← Interactive demo (reference for layout/components)
├── record.js          ← Playwright recording script (optional, also in HTML comment)
```

## Recording Pipeline

All recording instructions are embedded as an HTML comment at the end of `showcase.html`. The `record.js` file is NOT a separate deliverable — it lives only inside the HTML comment. Users copy-paste it when needed.

**Playwright script (inside HTML comment):**
```javascript
const { chromium } = require('playwright');
const path = require('path');

(async () => {
  const browser = await chromium.launch();
  const context = await browser.newContext({
    viewport: { width: 1280, height: 720 },
    recordVideo: {
      dir: './videos/',
      size: { width: 1280, height: 720 }
    }
  });
  const page = await context.newPage();
  await page.goto('file://' + path.resolve('./showcase.html'));
  await page.waitForTimeout(17000); // 15.5s content + ~1.5s buffer
  await context.close();
  await browser.close();
  console.log('Video saved to ./videos/');
})();
```

**Audio generation:** Suno.ai prompt: "minimal dark ambient electronic, cinematic product reveal, subtle pulse, no drums, professional SaaS feel, 60 seconds"

**Merge command:**
```bash
ffmpeg -i videos/showcase.webm -i music.mp3 \
  -filter_complex "[1:a]afade=t=in:st=0:d=1,afade=t=out:st=13:d=2,volume=0.25[a]" \
  -map 0:v -map "[a]" -shortest final_video.mp4
```

## Non-Goals

- Not interactive — purely auto-playing for recording
- No responsive design (fixed 1280x720)
- No real data or API calls
- No site nav (this is a standalone cinematic piece, not a site page)

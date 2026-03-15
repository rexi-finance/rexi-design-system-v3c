# Interactive Demo Page — Design Spec

**Date:** 2026-03-14
**Status:** Approved
**Audience:** Internal team / hiring
**Repo:** `rexi-finance/rexi-design-system-v3c` (GitHub Pages)

## Purpose

A click-to-advance interactive demo page simulating rexi-app's AI-driven dashboard widget creation flow, built with the exact V3c Hybrid Glass design system. Showcases the chat experience where a user asks Rexi AI to build a reconciliation dashboard and the AI responds with rich inline content — KPIs with sparklines, data tables, progress indicators, and decision buttons — while widgets materialize on the dashboard grid.

## Approach: Stepped Timeline

Single static HTML page with all 8 steps pre-rendered but hidden. Each click reveals the next message/widget with CSS transitions. A progress bar at the bottom shows current step. Zero dependencies beyond Lucide icons (CDN) and Google Fonts.

**Why this approach:**
- Simplest to build and maintain (no state machine, no timing logic)
- Works perfectly on GitHub Pages (zero build, zero server)
- User controls pacing — better for demos where the viewer wants to study each step

## Layout

Three-column in-context panel matching the real rexi-app:

| Section | Width | Content |
|---------|-------|---------|
| Sidebar | 64px | Collapsed with Merriweather "Rx" logo, Lucide nav icons, glass active state with 3px `rgba(255,255,255,0.62)` accent bar |
| Dashboard | flex:1 | Header ("Stripe vs Modern Treasury" + "Add Widget" button) + 2-column widget grid |
| Chat Panel | 420px | Header with purple-blue gradient icon, message stream, input bar |

A site nav bar sits above (44px) linking to Home, Reference, Process, Demo, and GitHub. A progress bar sits below (40px) with dot indicators and step label.

## 8-Step Demo Script

| Step | Chat | Dashboard |
|------|------|-----------|
| 1 | Empty chat with welcome state | Empty grid with subtle placeholder |
| 2 | **User:** "Build me a dashboard for the Stripe vs Modern Treasury reconciliation" | — |
| 3 | **AI:** Progress steps (3 done: found recon, loaded schema, queried stats) | — |
| 4 | **AI:** Rich markdown briefing with 3 inline stats (96.8% match, 142 unmatched, 23 exceptions) | — |
| 5 | **AI:** Suggests "Match Rate" KPI with inline sparkline + Add/Skip decision buttons | Ghost widget (dashed blue border) appears in grid |
| 6 | **User:** "Add it" → **AI:** Shows inline table (unmatched by side/reason/impact) + suggests next widget | KPI widget solidifies (glass card with header) |
| 7 | **User:** "Add it too" → **AI:** Suggests area chart for 7-day trend | Second widget solidifies, ghost for chart |
| 8 | **User:** "Yes, add the chart" → **AI:** "Your reconciliation dashboard is ready. I've added a Match Rate KPI, Total Unmatched count, and a 7-day trend chart. You can ask me to adjust any widget at any time." | All 3 widgets solid: Match Rate KPI, Total Unmatched KPI, area chart (full-width) |

## V3c Components Used

All components must match `reference.html` exactly. Key specs:

### Glass Card / Widget Card
- Background: `linear-gradient(135deg, rgba(255,255,255,0.05) 0%, rgba(255,255,255,0.01) 50%, rgba(255,255,255,0.03) 100%)`
- Border: `1px solid rgba(255,255,255,0.04)`, radius `12px`
- Shadow: `0 1px 3px rgba(0,0,0,0.125)`
- Header bar: `bg glass-bg-2`, `cursor:move`, title + info/menu icon buttons
- Ghost variant (new CSS for demo): `border: 1px dashed rgba(59,130,246,0.35)`, `background: linear-gradient(135deg, rgba(59,130,246,0.04) 0%, rgba(59,130,246,0.01) 50%, rgba(59,130,246,0.02) 100%)`

### Chat Messages
- AI avatar: 24px, 6px radius, purple-blue gradient background
- User avatar: 24px, 6px radius, glass-bg-4
- Bubbles: 12px radius, AI has `border-top-left-radius:4px`, user has `border-top-right-radius:4px`
- AI bubble: `glass-bg-2` + subtle border; User bubble: `glass-bg-4` + brighter border

### Chat Rich Content Types
1. **Progress steps** — done (emerald check icon), active (blue highlight + glass bg), pending (dim)
2. **Inline KPI** — glass card with title, 8px uppercase label (chat context; widget card labels use 9px), 26px value, sparkline SVG
3. **Inline table** — glass-bordered table with uppercase 10px headers, "View full breakdown" expandable hint
4. **Decision buttons** — primary (blue-muted bg, blue border) and secondary (glass bg, glass border), `8px` radius (distinct from action `.btn` which uses `12px`)
5. **Markdown stats** — flex row with 8px uppercase labels and 20px bold values

### Action Buttons (`.btn`)
- Radius: `12px` — copy from `reference.html` hardcoded values (not `v3c.css` which uses 6px)
- Primary: `glass-bg-4`, `border rgba(255,255,255,0.16)`, `inset 0 1px 0` top shine
- Active: `scale(0.97)`

### Area Chart
- SVG with emerald gradient fill (30% → 2% opacity)
- 2px emerald stroke line
- 4% white grid lines
- Day-of-week labels in 10px Figtree

### Sidebar
- 64px wide, collapsed-only
- Merriweather italic "Rx" logo
- 22px Lucide icons at 30% white
- Active state: 44px glass card with 3px `rgba(255,255,255,0.62)` accent bar on left

## DOM/JS Pattern

Steps use `data-step="N"` attributes on chat messages and widget elements. JavaScript tracks `currentStep` and adds class `step-visible` to all elements where `data-step <= currentStep`.

```css
/* Default hidden state */
[data-step] {
  opacity: 0;
  transform: translateY(8px);
  pointer-events: none;
  transition: opacity 0.4s ease, transform 0.3s ease;
}
/* Revealed state */
[data-step].step-visible {
  opacity: 1;
  transform: translateY(0);
  pointer-events: auto;
}
```

Widget ghost→solid transitions use a separate `data-solid` attribute toggled when the widget is "confirmed" by the next step.

## Interaction Behavior

- **Click anywhere** on the page to advance to next step (document-level click handler)
- Keyboard: right arrow / space also advance; left arrow goes back
- Widget materialization: fade in + slight upward translate
- Ghost → solid transition: border style change + background gradient change
- Progress dots update: done (emerald) → active (blue pill) → pending (glass)
- Step 8 shows a "Restart Demo" button; the button calls `event.stopPropagation()` to prevent the document click handler from advancing past the final step

## File Structure

```
rexi-design-system-v3c/
├── demo.html          ← The interactive demo page
├── index.html         ← Updated nav to include Demo link
├── reference.html     ← Updated nav to include Demo link
└── process/*.html     ← All 12 files: add Demo link to inline nav (following existing pattern)
```

## CSS Strategy

Copy V3c CSS directly from `reference.html` / `v3c.css` — do not abstract or modify. The demo page is a standalone HTML file that should visually match the reference exactly. All styles are inline in `<style>` tags (consistent with the rest of the site).

## Non-Goals

- No real API calls or backend
- No responsive/mobile layout (desktop-only demo)
- No text input functionality (input bar is decorative)
- No branching paths (linear 8-step sequence only)

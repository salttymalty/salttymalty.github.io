---
target: /Users/garen/salttymalty.github.io/living-type/
type: improvement-pass
created: 2026-03-23
status: pending
---

# Living Type — Improvement Pass

## Performance Fixes (HIGH)

### P1: Throttle rAF loops to 30fps
Files: ripple.html, gravity.html, chorus.html, waveform.html
Pattern: `if (ts - lastFrame < 33) return; lastFrame = ts;`
Why: 120Hz displays double CPU cost for no visual benefit on font-variation-settings (which repaints anyway).

### P2: Batch layout reads in worn.html
worn.html mousemove handler calls getBoundingClientRect() per span on every event.
Fix: Collect all rects first, then update styles in a second pass. Debounce mousemove to 50ms.

### P3: Reduce will-change in ripple.html
ripple.html sets `will-change` on 500+ spans = 500 compositing layers.
Fix: Remove `will-change` from individual spans; let the browser manage.

### P4: Add audio stream cleanup
Files: waveform.html, heartbeat.html, chorus.html, speak.html, notepad.html
Fix: `window.addEventListener('beforeunload', () => stream.getTracks().forEach(t => t.stop()))`.

### P5: Reduce SVG filter update frequency
Files: watercolor.html, turbulence.html
Fix: Only update feTurbulence baseFrequency every 3rd frame instead of every frame.

## Smoothness Fixes (MEDIUM)

### S1: CSS @property for declarative axis animation
Opportunity: Replace JS rAF font-variation-settings with CSS @property where possible.
Target pieces: breathe.html, softness.html, width.html (CSS-only pieces that currently use @keyframes).
Pattern:
```css
@property --wght {
  syntax: '<number>';
  initial-value: 300;
  inherits: false;
}
.word {
  font-variation-settings: 'wght' var(--wght);
  animation: breathe 8s ease-in-out infinite;
}
@keyframes breathe {
  50% { --wght: 900; }
}
```
Why: CSS @property animates the numeric value natively, producing smoother interpolation than CSS keyframing the entire font-variation-settings string. Browser support: Chrome 85+, Edge 85+, Safari 16.4+.

### S2: Scroll-driven animation in editorial.html
Replace JS scroll listener with CSS scroll-timeline for header opsz animation.
Pattern: `animation-timeline: view(); animation-range: entry 0% cover 50%;`
Why: Eliminates JS scroll handler + layout reads entirely. GPU-composited.

### S3: Consistent interaction hints
Add a subtle "click to freeze" hint that fades after 3s on all pieces.
Currently: some pieces have hints, others don't. Audio pieces say "click to start" but don't mention freeze.

## Feature Additions (from Coldtype community)

### F1: Per-glyph callback visualization
New piece: `callback.html` — Shows Coldtype's signature pattern (per-glyph functions) translated to web.
A sentence where each letter's weight is a function of its index: `wght = 200 + (index * 50)`.
Slider controls the function: linear, sine, random, cascade.
Educational: bridges Coldtype thinking to CSS/JS.

### F2: Multi-axis orchestrator
New piece: `orchestrate.html` — 3 sliders (or mouse X/Y + scroll) each mapped to a different axis.
Shows coprime cycle rates creating complex motion from simple inputs.
Inspired by Coldtype's functional composition.

### F3: MIDI input piece
New piece: `midi.html` — Web MIDI API. Each CC maps to a font axis.
CC1 (mod wheel) → wght, CC74 (brightness) → SOFT, pitch bend → slnt.
Niche but exactly what Coldtype's audio/music community wants.

### F4: Data-driven typography
New piece: `data.html` — Wavefont or Climate Crisis driven by real data.
Fetch a simple API (weather? bitcoin? word frequency?) and map values to font axes.
Typography as data visualization — the community's emerging frontier.

### F5: Code view toggle
On each piece: press `c` to see the source code overlaid (syntax highlighted).
Pattern: Read own innerHTML, display in a `<pre>` overlay.
Why: Coldtype users want to learn; showing code alongside the animation is the gallery's educational value prop.

## Consistency Fixes (LOW)

### C1: Expand prefers-reduced-motion to audio pieces
Files: waveform.html, heartbeat.html, chorus.html, speak.html
Currently: reduced-motion pauses CSS animations but JS rAF loops still run.
Fix: Check `window.matchMedia('(prefers-reduced-motion: reduce)')` in JS and show static fallback.

### C2: Standardize freeze interaction
neon.html uses single-click to cycle palette, double-click to freeze. Should match gallery standard (single click = freeze).
scatter.html uses Space to freeze, click to shuffle. Consider adding click-freeze too.

### C3: Audio context resume on iOS
Files: chorus.html, waveform.html, heartbeat.html, notepad.html
Add `if (ctx.state === 'suspended') await ctx.resume()` after AudioContext creation.

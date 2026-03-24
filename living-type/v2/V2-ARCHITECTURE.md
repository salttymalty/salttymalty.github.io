# Living Type v2 — Architecture

The gallery itself IS a variable font experience. Not a grid of links — a typographic environment.

## Philosophy

v1 = 33 isolated pieces, each a self-contained HTML file. Educational, beautiful, discrete.
v2 = one continuous surface. The navigation IS the art. Pieces aren't pages, they're states.

Rob Stenson's insight: "You create a single frame of content and let the physics engine do its thing. Aleatoric music for graphic design." v2 applies this to the gallery itself.

## Directory Structure

```
living-type/
  index.html          ← v1 (stays forever, untouched)
  v2/
    index.html         ← v2 entry point (single page)
    engine.js          ← shared animation/audio/transition engine
    pieces.json        ← piece metadata (fonts, axes, descriptions)
    pieces/            ← individual piece modules (ES module exports)
      breathe.js
      gravity.js
      ...
    style.css          ← shared tokens + layout
```

## Page Architecture — Single Entry Point

One HTML file. Pieces loaded as ES modules into an `<iframe>` or injected `<section>`. No page navigation — everything is state transitions within one document.

```html
<!-- v2/index.html -->
<div id="filmstrip"><!-- horizontal piece previews --></div>
<div id="stage"><!-- expanded piece renders here --></div>
<div id="ambient"><!-- persistent audio layer --></div>
<div id="scrubber"><!-- timeline control --></div>
<div id="playground"><!-- composition mode --></div>
```

## Navigation — The Film Strip

### Build first

A horizontal strip at the bottom (like a video editor timeline). Each piece is a thumbnail that breathes with its preview animation. Scroll horizontally to browse. Click to expand.

```
[breathe] [optical] [softness] [mono] [width] [slant] [grade] [crisis] ...
                                  ^
                          currently focused
```

- Keyboard: left/right arrows navigate, Enter expands, Escape contracts
- Touch: swipe horizontal, tap to expand
- Mouse: scroll wheel on filmstrip = horizontal scroll
- The filmstrip itself uses variable fonts — piece titles breathe at different rates

### The expansion

When a piece is selected, it expands from the filmstrip into the full viewport using the **View Transitions API**:

```javascript
document.startViewTransition(() => {
  stage.innerHTML = ''; // clear previous
  const piece = await import(`./pieces/${name}.js`);
  piece.mount(stage); // piece renders into stage
  filmstrip.classList.add('minimized'); // shrink to thin bar
});
```

The preview thumbnail morphs into the full piece. `view-transition-name` on the thumbnail and the stage creates the visual connection.

**Fallback:** browsers without View Transitions get a simple crossfade (opacity transition).

## Ambient Audio Layer

### Build second

A persistent `AudioContext` that runs across the whole gallery. Opt-in via a mic icon in the corner.

When enabled:
- Sub-bass energy drives a subtle weight shift on ALL visible text (filmstrip titles, piece text)
- Mid-range energy shifts SOFT axis
- The ambient layer is a CSS custom property pump:

```javascript
// ambient.js
const analyser = ctx.createAnalyser();
function pump() {
  const bands = getBands(analyser);
  document.documentElement.style.setProperty('--ambient-wght', bands.low * 2);
  document.documentElement.style.setProperty('--ambient-soft', bands.mid * 0.5);
  requestAnimationFrame(pump);
}
```

Pieces opt-in to ambient influence by referencing `var(--ambient-wght)` in their CSS. Pieces that don't reference it are unaffected.

## Timeline Scrubber

### Build first

A horizontal slider below the stage. Maps 0-100% to the piece's animation cycle.

**For CSS-animated pieces (breathe, softness, etc.):**
```javascript
// Pause all animations, set negative delay to scrub
const els = stage.querySelectorAll('*');
els.forEach(el => {
  const dur = parseFloat(getComputedStyle(el).animationDuration) * 1000;
  if (dur) {
    el.style.animationPlayState = 'paused';
    el.style.animationDelay = `${-scrubPos * dur}ms`;
  }
});
```

**For JS-animated pieces (gravity, waveform, etc.):**
Each piece module exports a `seek(t)` function that sets state to time `t` (0-1).

**For audio-reactive pieces:**
Scrubber is hidden (can't scrub real-time input).

## Split-Screen Code View

### Build second

Not a modal overlay (v1) — a resizable split panel.

```
┌──────────────────────┬──────────────────────┐
│                      │  // breathe.js       │
│     [piece runs]     │                      │
│                      │  @property --wght {  │
│                      │    syntax: '<num>';   │
│                      │    initial: 200;      │
│                      │  }                    │
│                      │                      │
│                      │  .word {              │
│                      │    animation: ...     │
│                      │  }                    │
└──────────────────────┴──────────────────────┘
```

The code panel is a `<pre contenteditable>` with syntax highlighting via a tiny tokenizer (no Prism/Highlight.js — hand-roll a 50-line regex tokenizer for CSS/JS).

**Live edit:** On `input` event, debounce 500ms, then `eval()` the modified code in the piece's context. Wrap in try/catch — errors show as a red flash on the code panel border, not a crash.

**What's editable:** Not the full source — a curated "key parameters" excerpt. Each piece module exports a `codeView` string that contains the interesting bits.

## Playground — Composition Mode

### Build second

A blank canvas where visitors build their own piece:

1. **Pick a font** — dropdown with the 6 gallery fonts, each previewed in its own face
2. **Type text** — contenteditable input, text appears on canvas
3. **Pick axes** — checkboxes for available axes, with range sliders
4. **Pick input** — radio buttons: mouse, scroll, audio, time, typing
5. **Map axes to inputs** — drag-connect UI: drag from "wght" to "mouse X"
6. **See it live** — the canvas updates in real time
7. **Export** — generates a single HTML file (like v1 pieces) and downloads it

The mapping UI is the hard part. Simplest approach: a grid.

```
           mouse-X  mouse-Y  time   audio-bass  audio-treble
  wght     [  x  ]  [     ]  [    ] [         ] [           ]
  opsz     [     ]  [  x  ]  [    ] [         ] [           ]
  SOFT     [     ]  [     ]  [ x  ] [         ] [           ]
```

Clicking a cell toggles the mapping. Multiple inputs can drive one axis (they add).

## 3D Depth Layer

### Stretch goal

CSS `perspective` + `transform-style: preserve-3d` for real z-depth:

```css
#stage {
  perspective: 1200px;
  perspective-origin: 50% 50%;
}

.word {
  transform: translateZ(var(--z));
  /* z determines: blur, opacity, scale, color warmth */
}
```

Mouse = camera tilt (subtle, +-3deg). Scroll = dolly (perspective-origin shift).
`dof.html` from v1 is the proof-of-concept — v2 applies this to the gallery shell itself.

## Multi-User Jam

### Stretch goal

Start simple: **BroadcastChannel API** (same browser, multiple tabs):

```javascript
const channel = new BroadcastChannel('living-type-jam');

// Tab 1: sends mouse position
channel.postMessage({ type: 'mouse', x: e.clientX, y: e.clientY });

// Tab 2: receives and maps to SOFT axis
channel.onmessage = (e) => {
  if (e.data.type === 'mouse') {
    document.documentElement.style.setProperty('--jam-soft', e.data.y / window.innerHeight * 100);
  }
};
```

Two tabs open side by side on the same machine. One controls weight, the other controls softness. Typography as ensemble.

**Upgrade path:** WebRTC DataChannel for cross-device. But BroadcastChannel is zero-infrastructure and works today.

## Vinyl Record Metaphor — The Sequencer

### Stretch goal

The filmstrip becomes a circular disc. Pieces are tracks arranged around the circumference. A "needle" (playhead) sweeps around, auto-advancing through pieces. Each piece plays for its natural cycle duration.

Audio-reactive pieces get audio from the user's music (file drop or mic). The gallery becomes a VJ tool — typography responding to music in sequence.

This is the most bonkers feature. It requires:
- CSS `conic-gradient` or canvas for the disc visual
- Auto-advance timer that respects each piece's natural duration
- Crossfade transitions between pieces
- Global audio routing to the active piece

## Performance Budget

| Component | Budget | Technique |
|-----------|--------|-----------|
| Filmstrip previews (33) | 16ms/frame | CSS-only animations, no JS per preview |
| Active piece | 16ms/frame | Piece's own budget (already tested in v1) |
| Ambient audio pump | 100ms interval | Not every frame — smooth with CSS transitions |
| Code view | On input only | Debounced 500ms, no per-frame cost |
| View Transitions | Native | Browser handles compositing |
| 3D depth | 16ms/frame | CSS transforms only, GPU-composited |
| Total idle | <5% CPU | Nothing runs when not interacting |

**Key rule:** Only ONE piece renders at full fidelity at a time. Filmstrip previews are CSS-only miniatures (like v1's index cards).

## Build Sequence

### Phase 1 — Navigation shell (build first)
- [ ] `v2/index.html` with filmstrip + stage
- [ ] `pieces.json` metadata
- [ ] Load pieces as iframes (simplest isolation)
- [ ] View Transitions between filmstrip and stage
- [ ] Timeline scrubber for CSS pieces
- [ ] Keyboard navigation (arrows, enter, escape)
- [ ] Mobile touch (swipe, tap)

### Phase 2 — Rich features (build second)
- [ ] Ambient audio layer with CSS custom property pump
- [ ] Split-screen code view (contenteditable + live eval)
- [ ] Playground composition mode (font picker + axis mapper + export)
- [ ] Story-driven descriptions in filmstrip (from v1 card rewrites)

### Phase 3 — Bonkers (stretch goals)
- [ ] 3D depth gallery shell (perspective + camera tilt)
- [ ] Multi-user BroadcastChannel jam
- [ ] Vinyl sequencer auto-advance
- [ ] WebRTC cross-device jam
- [ ] MIDI input integration
- [ ] Gyroscope input (mobile tilt → axis mapping)

## Vanilla JS Honest Assessment

| Feature | Vanilla feasible? | Notes |
|---------|------------------|-------|
| Filmstrip navigation | Yes | CSS scroll-snap + JS focus management |
| View Transitions | Yes | Native API, progressive enhancement |
| Timeline scrubber | Yes | Animation delay trick, no library needed |
| Audio analysis | Yes | Web Audio API is vanilla |
| Code view with live edit | Yes, with care | eval() in try/catch, hand-rolled tokenizer |
| Playground axis mapper | Yes but tedious | Grid of checkboxes, ~200 lines of JS |
| HTML export | Yes | Template literal → Blob → download |
| 3D depth | Yes | CSS perspective, no Three.js needed |
| BroadcastChannel | Yes | 10 lines of code |
| WebRTC | Barely | Signaling needs a server; use a free TURN service |
| Vinyl disc visual | Yes | Canvas 2D or CSS conic-gradient |

**What would benefit from a library:** Nothing critical. The hardest part is the playground's axis-to-input mapping UI, which is ~200 lines of DOM manipulation. A framework would save ~50 lines but add 50KB of bundle.

## What v2 Does NOT Do

- Replace v1 (v1 stays at `living-type/index.html` forever)
- Require a build step or bundler
- Use any JavaScript framework
- Require a server (static files on GitHub Pages)
- Load all pieces at once (lazy load on demand)
- Compromise on beauty for features

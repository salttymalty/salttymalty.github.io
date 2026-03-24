# Living Type — Generation Prompt

Use this prompt to generate divergent variable font animation pieces for the Living Type gallery at `salttymalty.github.io/living-type/`.

## Existing Pieces (12 total)

| File | Technique | Font | Axes | Interaction |
|------|-----------|------|------|-------------|
| categories.html | CSS breathing, 4 words at coprime cycles | Fraunces | wght, opsz, SOFT, WONK | mouse warms palette, click freezes |
| recursive.html | MONO/CASL axis morph | Recursive | MONO, CASL, wght, slnt, CRSV | CSS only |
| cursor.html | Mouse-driven instrument | Fraunces | wght, opsz, SOFT, WONK | X=weight, Y=softness |
| poem.html | Multi-line poem, per-line timing | Fraunces | wght, opsz, SOFT, WONK, ital | CSS only |
| earthquake.html | Per-letter random animation | Fraunces | all 4 + rotation/scale | JS per-letter |
| ripple.html | Mouse proximity grid | Fraunces | all 4 | JS physics |
| rave.html | Fast axis slamming, strobing | Fraunces + Recursive | all | CSS + mouse color |
| turbulence.html | SVG feTurbulence displacement | Fraunces | all + SVG filter | mouse distance |
| supernova.html | Canvas particles + curl noise | Fraunces + Canvas | all + particles | click burst |
| acid.html | 3 fonts, 14 axes, chromatic aberration | Anybody + Fraunces + Recursive | all | mouse + click glitch |
| worn.html | Per-letter variation, film grain, sun ornaments | Fraunces | all + SVG grain filter | CSS only |
| ledger.html | Journal page, ruled lines, red ink, stamps | Fraunces | all + feTurbulence | CSS only |

## Architecture Rules

Each piece MUST be:
- **Single HTML file**, self-contained, no external JS or CSS (fonts via Google Fonts CDN only)
- **Under 8KB** (ideally under 5KB) — beauty through constraint
- **No frameworks, no build step** — vanilla HTML/CSS/JS only
- **Click anywhere to freeze/unfreeze animation** — consistent interaction pattern
- **Responsive** — works at any viewport size, uses clamp(), vw/vh, auto-fit grids
- **`prefers-reduced-motion` respected** — animation-play-state: paused fallback
- **Title tag = piece name** (lowercase, one or two words)

### Color Approach
- Each piece can have its own palette — not limited to the gallery's terracotta
- Use CSS custom properties for all colors (`:root` vars)
- Full saturation is fine for maximalist pieces
- Dark backgrounds use `#0f0e0c` or similar warm darks, not pure black
- Light backgrounds use warm creams, not pure white
- OKLCH encouraged for perceptually uniform color work

### Marks (optional)
Available SVG marks in `marks/`: sun-organic.svg, sun-expressive.svg, sun-full.svg, sun-compact.svg, eye.svg, skull.svg.
Available ink scans in `ink/`: skull-cutout-on-wood.webp, eye-linocut-framed.webp, crescent-moon-star.webp.

## Variable Fonts to Draw From

### Already used
- **Fraunces** (wght 100-900, opsz 9-144, SOFT 0-100, WONK 0-1) — the workhorse
- **Recursive** (MONO 0-1, CASL 0-1, wght 300-1000, slnt -15-0, CRSV 0-1)
- **Anybody** (wght 100-900, wdth 75-150)

### New fonts to explore
- **Roboto Flex** (wght, wdth, opsz, GRAD, slnt, XTRA, XOPQ, YOPQ, YTLC, YTUC, YTAS, YTDE, YTFI — 13 axes!)
- **Recursive** CASL axis (monospace to casual handwriting — not yet featured standalone)
- **Playpen Sans** (wght — designed to look handwritten)
- **Climate Crisis** (YEAR 1979-2050 — letterforms degrade as "year" increases)
- **Nabla** (isometric 3D color font — EDPT, EHLT axes)
- **Wavefont** (WGHT drives data visualization — each glyph is a bar chart)
- **Foldit** (wght — geometric folded paper letterforms)
- **Jacquard 12** / **Silkscreen** (pixel fonts at variable weights)
- **Source Serif 4** (wght, opsz — refined editorial)
- **Inter** (wght, opsz, slnt — clean Swiss grid)

## Part 1: 20 Divergent Pieces

Generate these as complete HTML files. Each explores a distinct visual direction.

### Tier A — Single Technique Showcases (8 pieces)
These isolate ONE animation concept. Educational. Clear. Beautiful.

1. **breathe.html** — One giant word, one axis (wght), one speed. The simplest possible piece. Fraunces at 20vw, weight 200↔900 over 8s. Cream on dark.

2. **optical.html** — One word, opsz axis only. "reading" at body size morphs to display size. Show how optical size changes stroke contrast and x-height. Side-by-side: opsz 9 vs opsz 144 vs animated.

3. **softness.html** — Fraunces SOFT axis solo. "serif" repeated 5 times, each at a different SOFT keyframe phase. Watch serifs dissolve to rounds in a cascade.

4. **mono.html** — Recursive MONO axis solo. A paragraph where each word transitions from proportional to monospace at staggered delays. Watch the text reflow in slow motion.

5. **width.html** — Roboto Flex wdth axis. A single sentence that stretches and compresses, filling its container exactly. `font-stretch` animated from 75% to 150%.

6. **slant.html** — Recursive slnt axis. Words lean left, straighten, lean right. Like wind blowing through text. Staggered per-word.

7. **grade.html** — Roboto Flex GRAD axis. Text that gets optically heavier/lighter without reflowing. Demonstrate the difference between grade and weight. Two columns: left grades, right changes weight — same visual density shift, but grade doesn't change metrics.

8. **crisis.html** — Climate Crisis font. YEAR axis from 1979 to 2050. Letterforms gradually degrade as the year advances. Display a year counter. The typography IS the data visualization.

### Tier B — Compositional Pieces (6 pieces)
These combine techniques into distinct visual moods.

9. **editorial.html** — Source Serif 4 + Inter. Clean editorial layout. Headline breathes opsz, body text has subtle GRAD shifts on scroll-position (JS). Cream background with a thin rule. Magazine energy.

10. **neon.html** — Fraunces on dark, but with CSS `text-shadow` glow layers in saturated color. Weight pulses drive glow intensity. Three lines at different hues (coral, cyan, amber). Click cycles palette.

11. **watercolor.html** — Light background. SVG feTurbulence + feColorMatrix produce a watercolor bleed behind text. SOFT axis makes serifs dissolve. Colors shift slowly through warm earth tones. Marks float like splatter.

12. **brutalist.html** — Hard edges. System font stack (Helvetica/Arial). Huge weight. Black and white. No border-radius. Text fills the viewport edge to edge. Weight animation is abrupt (steps()), not smooth. Anti-beauty as beauty.

13. **ticker.html** — Horizontal marquee of words at different sizes/weights. CSS `@keyframes` translateX loop. Each word has its own axis animation. Like a news ticker from another dimension. Roboto Flex wdth keeps it filling the band.

14. **scatter.html** — Words scattered randomly across the viewport (CSS grid + random transforms via JS on load). Each word gets a random font, random axes, random cycle. Click reshuffles. Controlled chaos that still feels intentional because all fonts share the warm palette.

### Tier C — Technical Experiments (6 pieces)
These push into canvas, audio, and interactivity.

15. **gravity.html** — Physics simulation. Words fall from the top, bounce off the floor, and each collision changes their weight/softness. Heavier words = heavier font weight. Canvas collision detection, DOM text rendering.

16. **trail.html** — Mouse trail that leaves typographic marks. Moving the mouse stamps the current word at cursor position with variation settings that decay from bold to thin, opaque to transparent. Ghostly typographic wake.

17. **waveform.html** — Audio-reactive. Mic input (or file drop). A single large word where each letter's weight maps to a frequency band (sub→low weight, air→high weight). Real-time 5-band analyzer like sun-blast. Text becomes a graphic equalizer.

18. **heartbeat.html** — Audio-reactive. Mic or file. The entire page background color shifts with sub-bass. A single word scales with the kick drum. Minimal: one word, two inputs (color + scale). The simplest audio piece.

19. **speak.html** — **Speech-to-text reactive.** Uses Web Speech API (SpeechRecognition) to transcribe live mic input. Each recognized word appears on screen with random position and axis settings. Words accumulate, older words fade and drift. Your voice fills the screen with living type.

20. **chorus.html** — **Speech-to-text + audio analysis combined.** Transcription places words, but audio energy drives their animation intensity. Whisper = gentle breathing. Shout = earthquake per-letter chaos. Volume maps to animation amplitude. The text responds to HOW you speak, not just what you say.

## Part 2: Audio Transcription Reactive Pieces (Detail)

The `speak.html` and `chorus.html` pieces need the Web Speech API. Here's the technical pattern:

```javascript
// Speech recognition setup
const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
const recognition = new SpeechRecognition();
recognition.continuous = true;
recognition.interimResults = true;
recognition.lang = 'en-US';

recognition.onresult = (event) => {
  for (let i = event.resultIndex; i < event.results.length; i++) {
    if (event.results[i].isFinal) {
      const word = event.results[i][0].transcript.trim();
      placeWord(word); // position on screen with random variation settings
    }
  }
};

recognition.start();
```

### Audio analysis (from sun-blast pattern)
```javascript
// 5-band frequency analysis
const audioCtx = new AudioContext();
const analyser = audioCtx.createAnalyser();
analyser.fftSize = 1024;
const freqData = new Uint8Array(analyser.frequencyBinCount);

function getBands() {
  analyser.getByteFrequencyData(freqData);
  const nyquist = audioCtx.sampleRate / 2;
  const binHz = nyquist / freqData.length;
  function avg(lo, hi) { /* band average */ }
  return { sub: avg(20,60), low: avg(60,250), mid: avg(250,2000), high: avg(2000,8000), air: avg(8000,16000) };
}
```

### Mapping audio to font axes
| Band | Axis | Effect |
|------|------|--------|
| sub (20-60Hz) | scale | Kick drum makes text pulse |
| low (60-250Hz) | wght | Bass drives weight |
| mid (250-2kHz) | SOFT | Vocals drive softness |
| high (2-8kHz) | opsz | Hats/sibilants drive optical size |
| air (8kHz+) | WONK/CASL | Shimmer drives quirk |

### Particle text (sun-blast technique applied to type)
For pieces that want the sun-blast energy: use Canvas 2D for particle physics (curl noise, bloom compositing) but render the text as DOM elements positioned over the canvas. This keeps font rendering crisp while particles blur behind.

## Part 3: Index Card Conventions

Each new piece needs a card in `index.html`. Follow existing patterns:

- Preview div has a class like `.preview-{name}` with inline CSS that miniaturizes the piece's visual identity
- Card body has title, description, and meta tags (font name, technique, interaction type)
- Section dividers group pieces: "single axis", "compositional", "audio-reactive"
- All colors in previews use `:root` CSS custom properties

## Part 4: Responsive Patterns

All pieces should use:
```css
font-size: clamp(2rem, 8vw, 6rem);   /* fluid type */
padding: clamp(1rem, 4vw, 3rem);      /* fluid spacing */
min-height: 100dvh;                    /* dynamic viewport height */
```

For grid-based pieces:
```css
grid-template-columns: repeat(auto-fit, minmax(min(100%, 300px), 1fr));
```

For per-letter pieces, wrap in a container with `overflow-wrap: break-word` and ensure letter spans don't break layout on narrow screens.

## Execution Order

1. Build Tier A first (8 single-technique showcases) — these are quick, educational, CSS-only
2. Build Tier B next (6 compositional pieces) — these need more design judgment
3. Build Tier C last (6 technical experiments) — these need JS, audio, speech APIs
4. After each tier: add cards to index.html, commit, push
5. Screenshot each piece in Chrome for verification

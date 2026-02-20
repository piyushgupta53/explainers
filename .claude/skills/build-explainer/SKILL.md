---
name: build-explainer
description: >
  Build interactive educational explainers on ML, AI, DL, RL, math, and
  computer science topics as self-contained HTML files for a GitHub Pages
  blog. Use when creating a new explainer page, writing the prose for a
  technical topic, building interactive canvas demos, designing the section
  structure, or implementing quizzes, audio, and visualizations. Covers
  the full design system, 12 canvas component recipes (function plotters,
  distributions, heatmaps, step-through animators, grid worlds, waveforms,
  etc.), pedagogy guide, and pre-ship checklist.
---

Complete implementation guide for building explainer pages.
Covers the design system, page shell, every component and recipe type,
the audio system, and canvas conventions.

Read `CLAUDE.md` first for writing philosophy and topic structure.
This file is the how-to-build; CLAUDE.md is the how-to-think.

---

## File Structure

```
explainers/
  index.html              ← Hub — lists all published explainers
  CLAUDE.md               ← Writing + pedagogy guide (read first)
  SKILL.md                ← This file
  autoencoders/
    index.html
  backpropagation/
    index.html
  entropy/
    index.html
  ...
```

**One rule:** Every explainer is a **single self-contained `index.html`**.
No build tools, no npm, no frameworks. Pure HTML/CSS/JS.
Works offline. Zero-config deploy to GitHub Pages.

**How to write the file:** Do not try to compose it in your head before writing. Write it in one pass using the Write tool: CSS block → HTML prose (all sections, top to bottom) → JavaScript (all demos). Write prose and demo HTML together section by section, then all JavaScript at the end. Keep moving — fix imperfect sentences after the file exists. When rewriting an existing explainer, read the full file first, then write the replacement in one shot rather than editing dozens of locations incrementally.

---

## Design System

### Color Palette — Copy Exactly, Never Invent New Colors

```css
:root {
  /* Backgrounds — warm off-white, never pure white */
  --bg:           #faf9f6;   /* page background */
  --bg2:          #f3f1ec;   /* panels, callout backgrounds, interactive areas */
  --bg3:          #eceae4;   /* stronger inset, disabled/muted states */

  /* Text */
  --text:         #1c1c1a;   /* all body text */
  --muted:        #7a7870;   /* captions, labels, secondary info */
  --border:       #e2dfd8;   /* all borders and dividers */

  /* Accent — terracotta, use sparingly */
  --accent:       #c4622d;
  --accent-light: #fdf0e8;

  /* Semantic colors — for callout types and quiz feedback */
  --blue:         #2a5db0;   /* note / info / definition */
  --blue-light:   #eef3fc;
  --green:        #1a7a4a;   /* result / correct / success */
  --green-light:  #eaf5ee;
  --purple:       #6b3fa0;   /* math / equation */
  --purple-light: #f2ecfa;

  --code-bg:      #f5f3ee;
}
```

**Rules:**
- `--accent` only on: progress bar fill, section markers, category badges, button hover, active slider thumbs. Never on large areas.
- All body text: `--text` or `--muted`. Never colored text in prose paragraphs.
- Callout border: semantic color. Callout background: its light variant.
- Canvas/demo backgrounds: `white` inside a `--border`-bordered container.
- No dark backgrounds, anywhere. Light mode only.

### Typography — Load All Three, Never Substitute

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,500;0,600;1,400;1,500&family=DM+Sans:wght@300;400;500&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
```

| Role | Font | Size | Weight | Notes |
|------|------|------|--------|-------|
| Body prose | Lora | 1rem (18px base) | 400 | line-height 1.78 |
| h1 title | Lora | clamp(2rem,5vw,2.9rem) | 500 | letter-spacing −0.02em |
| h2 section | Lora | 1.65rem | 500 | letter-spacing −0.015em |
| h3 subsection | Lora | 1.2rem | 500 | font-style italic |
| UI labels, buttons, nav | DM Sans | 0.78–0.88rem | 300–500 | |
| Section markers, badges, mono labels | DM Mono | 0.62–0.72rem | 400 | uppercase, letter-spacing 0.08–0.1em |
| Math, code, numerical values | DM Mono | 0.82–0.9rem | 400 | |
| Captions, footnotes | DM Sans | 0.78–0.85rem | 300 | color: --muted |

`html { font-size: 18px }` — all rem values are relative to this.

### Layout

```css
.container { max-width: 720px; margin: 0 auto; padding: 0 2rem; }  /* prose column */
.wide      { max-width: 960px; margin: 0 auto; padding: 0 2rem; }  /* wide content */
```

Interactive demos "bleed" outside the text column with negative margins:
```css
.interactive-block { margin: 2.5rem -1rem; }             /* mobile */
@media(min-width: 760px) {
  .interactive-block { margin: 2.5rem -2rem; }           /* desktop */
}
```

### When to Break the Design System

The design system exists for consistency across the collection. But some topics will benefit from breaking the grid. Legitimate breaks include:

- A full-bleed diagram that takes over the page width
- A section with no prose at all — just an interactive and its controls
- Stacking multiple small demos side-by-side instead of one large one
- A before/after comparison that uses the full viewport
- An interactive that starts BEFORE any section heading — as the very first thing the reader sees
- A section that's a single image or diagram with a caption, no prose

If the topic demands it, break the grid. The constraint is: it must still feel like the same collection — same fonts, same colors, same warmth. The layout can flex; the identity can't.

---

## Complete CSS Block

Paste this entire block into every new explainer. Add only page-specific styles below it.

```css
:root{--bg:#faf9f6;--bg2:#f3f1ec;--bg3:#eceae4;--text:#1c1c1a;--muted:#7a7870;--border:#e2dfd8;--accent:#c4622d;--accent-light:#fdf0e8;--blue:#2a5db0;--blue-light:#eef3fc;--green:#1a7a4a;--green-light:#eaf5ee;--purple:#6b3fa0;--purple-light:#f2ecfa;--code-bg:#f5f3ee;}
*{margin:0;padding:0;box-sizing:border-box;}
html{font-size:18px;scroll-behavior:smooth;}
body{background:var(--bg);color:var(--text);font-family:'Lora',serif;-webkit-font-smoothing:antialiased;line-height:1;}
.container{max-width:720px;margin:0 auto;padding:0 2rem;}
.wide{max-width:960px;margin:0 auto;padding:0 2rem;}

/* HEADER */
header{border-bottom:1px solid var(--border);padding:0 2rem;position:sticky;top:0;z-index:100;background:rgba(250,249,246,0.95);backdrop-filter:blur(6px);}
.header-inner{max-width:960px;margin:0 auto;padding:1rem 0;display:flex;align-items:center;justify-content:space-between;gap:1rem;}
.back-link{font-family:'DM Sans',sans-serif;font-size:0.78rem;color:var(--muted);text-decoration:none;transition:color .15s;}
.back-link:hover{color:var(--accent);}

/* PROGRESS BAR */
.progress-bar{height:2px;background:var(--border);position:fixed;top:0;left:0;right:0;z-index:200;}
.progress-fill{height:100%;background:var(--accent);width:0%;transition:width .1s;}

/* MASTHEAD */
.masthead{padding:5rem 0 3rem;border-bottom:1px solid var(--border);}
.post-meta{display:flex;align-items:center;gap:1rem;margin-bottom:1.5rem;font-family:'DM Sans',sans-serif;}
.post-category{font-size:0.72rem;color:var(--accent);background:var(--accent-light);padding:3px 9px;border-radius:3px;font-family:'DM Mono',monospace;letter-spacing:.05em;text-transform:uppercase;}
.post-date,.post-read{font-size:0.8rem;color:var(--muted);}
h1.post-title{font-family:'Lora',serif;font-size:clamp(2rem,5vw,2.9rem);font-weight:500;line-height:1.15;letter-spacing:-.02em;margin-bottom:1.2rem;}
.post-subtitle{font-size:1.1rem;line-height:1.65;color:var(--muted);font-style:italic;max-width:560px;}

/* TABLE OF CONTENTS */
.toc{background:var(--bg2);border:1px solid var(--border);padding:1.5rem 2rem;margin:3rem 0;}
.toc-title{font-family:'DM Mono',monospace;font-size:0.65rem;color:var(--muted);letter-spacing:.1em;text-transform:uppercase;margin-bottom:.8rem;}
.toc ol{list-style:none;counter-reset:toc;}
.toc li{counter-increment:toc;margin-bottom:.3rem;}
.toc li::before{content:counter(toc,upper-roman)". ";font-family:'DM Mono',monospace;font-size:0.72rem;color:var(--muted);margin-right:.3rem;}
.toc a{font-family:'DM Sans',sans-serif;font-size:0.88rem;color:var(--blue);text-decoration:none;}
.toc a:hover{text-decoration:underline;}

/* PROSE */
.prose{padding:2.5rem 0;}
.section-marker{font-family:'DM Mono',monospace;font-size:0.65rem;color:var(--muted);letter-spacing:.1em;text-transform:uppercase;margin-bottom:.8rem;margin-top:4rem;display:block;}
h2{font-family:'Lora',serif;font-size:1.65rem;font-weight:500;line-height:1.3;letter-spacing:-.015em;margin-bottom:1.2rem;color:var(--text);}
h3{font-family:'Lora',serif;font-size:1.2rem;font-weight:500;font-style:italic;margin-top:2.2rem;margin-bottom:.8rem;color:var(--text);}
p{font-size:1rem;line-height:1.78;margin-bottom:1.2rem;color:#2a2a28;}
strong{font-weight:600;color:var(--text);}
em{font-style:italic;}
code{font-family:'DM Mono',monospace;font-size:.82em;background:var(--code-bg);padding:1px 5px;border-radius:3px;color:var(--accent);}
.math{font-family:'DM Mono',monospace;font-size:.9rem;color:var(--purple);background:var(--purple-light);padding:1px 6px;border-radius:3px;}
.section-sep{border:none;border-top:1px solid var(--border);margin:4rem 0;}
sup{font-size:.65em;color:var(--blue);cursor:pointer;}

/* CALLOUTS */
.callout{border-left:3px solid var(--border);padding:.8rem 1.2rem;margin:1.8rem 0;background:var(--bg2);}
.callout.note{border-color:var(--blue);background:var(--blue-light);}
.callout.insight{border-color:var(--accent);background:var(--accent-light);}
.callout.math-callout{border-color:var(--purple);background:var(--purple-light);}
.callout.result{border-color:var(--green);background:var(--green-light);}
.callout-label{font-family:'DM Mono',monospace;font-size:.65rem;letter-spacing:.1em;text-transform:uppercase;color:var(--muted);margin-bottom:.4rem;}
.callout p{font-size:.92rem;margin-bottom:0;line-height:1.65;}

/* MATH BLOCK */
.math-block{background:var(--purple-light);border:1px solid rgba(107,63,160,.15);padding:1.5rem 2rem;margin:2rem 0;font-family:'DM Mono',monospace;font-size:.88rem;line-height:2;color:var(--purple);}
.math-block .eq-label{font-size:.65rem;color:var(--muted);text-transform:uppercase;letter-spacing:.1em;display:block;margin-bottom:.5rem;}
.math-block .comment{color:var(--muted);font-size:.78rem;display:block;margin-top:.3rem;}

/* INTERACTIVE BLOCKS */
.interactive-block{margin:2.5rem -1rem;background:var(--bg2);border:1px solid var(--border);padding:1.5rem;}
@media(min-width:760px){.interactive-block{margin:2.5rem -2rem;padding:2rem;}}
.interactive-label{font-family:'DM Mono',monospace;font-size:.62rem;color:var(--accent);letter-spacing:.1em;text-transform:uppercase;margin-bottom:1rem;display:flex;align-items:center;gap:.5rem;}
.interactive-label::after{content:'';flex:1;height:1px;background:var(--border);}
.interactive-setup{font-family:'DM Sans',sans-serif;font-size:.82rem;color:var(--muted);line-height:1.6;margin-bottom:1rem;}

/* BUTTONS */
.btn{font-family:'DM Sans',sans-serif;font-size:.78rem;font-weight:500;padding:7px 16px;border:1px solid var(--border);background:white;color:var(--text);cursor:pointer;border-radius:3px;transition:all .15s;margin:.3rem;}
.btn:hover{border-color:var(--accent);color:var(--accent);background:var(--accent-light);}
.btn.primary{background:var(--accent);color:white;border-color:var(--accent);}
.btn.primary:hover{background:#a84f25;}
.btn:disabled{opacity:.45;cursor:not-allowed;}

/* SLIDERS */
.slider-group{display:flex;align-items:center;gap:.5rem;font-family:'DM Sans',sans-serif;font-size:.78rem;color:var(--muted);flex:1;min-width:180px;}
input[type=range]{-webkit-appearance:none;height:2px;background:var(--border);outline:none;cursor:pointer;flex:1;border-radius:1px;}
input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;width:14px;height:14px;background:var(--accent);border-radius:50%;cursor:pointer;transition:transform .1s;}
input[type=range]::-webkit-slider-thumb:active{transform:scale(1.3);}
.slider-val{font-family:'DM Mono',monospace;font-size:.78rem;color:var(--accent);min-width:4ch;text-align:right;}

/* QUIZZES */
.quiz-block{background:white;border:1px solid var(--border);padding:1.5rem;margin:2rem 0;}
.quiz-q{font-size:.95rem;font-weight:600;margin-bottom:1rem;line-height:1.5;color:var(--text);}
.quiz-options{display:flex;flex-direction:column;gap:.4rem;}
.quiz-opt{text-align:left;font-family:'DM Sans',sans-serif;font-size:.85rem;padding:.7rem 1rem;border:1px solid var(--border);background:var(--bg);color:var(--text);cursor:pointer;transition:all .15s;border-radius:2px;}
.quiz-opt:hover:not(:disabled){border-color:var(--accent);color:var(--accent);background:var(--accent-light);}
.quiz-opt.correct{border-color:var(--green);background:var(--green-light);color:var(--green);}
.quiz-opt.wrong{border-color:#c0392b;background:#fef0ee;color:#c0392b;}
.quiz-feedback{margin-top:.8rem;font-family:'DM Sans',sans-serif;font-size:.85rem;line-height:1.6;display:none;padding:.8rem;border-radius:2px;}
.quiz-feedback.show{display:block;}
.quiz-feedback.good{background:var(--green-light);color:var(--green);}
.quiz-feedback.bad{background:#fef0ee;color:#c0392b;}

/* FOOTNOTES */
.footnotes{border-top:1px solid var(--border);padding-top:2rem;margin-top:4rem;}
.footnotes-title{font-family:'DM Mono',monospace;font-size:.65rem;color:var(--muted);letter-spacing:.1em;text-transform:uppercase;margin-bottom:1rem;}
.footnote{font-size:.82rem;color:var(--muted);line-height:1.6;margin-bottom:.6rem;display:flex;gap:.5rem;}
.fn-num{font-family:'DM Mono',monospace;font-size:.72rem;color:var(--blue);flex-shrink:0;}

/* FOOTER */
footer{border-top:1px solid var(--border);padding:2rem;margin-top:4rem;}
.footer-inner{max-width:720px;margin:0 auto;display:flex;justify-content:space-between;align-items:center;font-family:'DM Sans',sans-serif;font-size:.78rem;color:var(--muted);flex-wrap:wrap;gap:.8rem;}
.footer-inner a{color:var(--muted);text-decoration:none;}
.footer-inner a:hover{color:var(--accent);}

::-webkit-scrollbar{width:4px;}
::-webkit-scrollbar-track{background:var(--bg);}
::-webkit-scrollbar-thumb{background:var(--border);}
```

---

## Page Shell

Every explainer follows this exact HTML skeleton:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Topic] — [One-line subtitle]</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,500;0,600;1,400;1,500&family=DM+Sans:wght@300;400;500&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
  <style>
    /* PASTE FULL CSS BLOCK */
    /* THEN PAGE-SPECIFIC STYLES */
  </style>
</head>
<body>

<div class="progress-bar"><div class="progress-fill" id="progress"></div></div>

<header>
  <div class="header-inner">
    <a href="../index.html" class="back-link">← Distilled</a>
    <span style="font-family:'DM Mono',monospace;font-size:.65rem;color:var(--muted)">
      Category · 0N
    </span>
  </div>
</header>

<div class="masthead">
  <div class="container">
    <div class="post-meta">
      <span class="post-category">Category</span>
      <span class="post-date">2025</span>
      <span class="post-read">~N min read</span>
    </div>
    <h1 class="post-title">Topic — Subtitle</h1>
    <p class="post-subtitle">One evocative sentence that names the core mystery.</p>
  </div>
</div>

<div class="container">
  <div class="prose">

    <nav class="toc">
      <div class="toc-title">Contents</div>
      <ol>
        <li><a href="#s1">First Section</a></li>
        <!-- ... -->
      </ol>
    </nav>

    <span class="section-marker" id="s1">I. — Short Label</span>
    <h2>Full Section Title</h2>
    <p>...</p>
    <hr class="section-sep">

    <!-- more sections -->

    <div class="footnotes">
      <div class="footnotes-title">Notes</div>
      <div class="footnote"><span class="fn-num">[1]</span><span>...</span></div>
    </div>

  </div>
</div>

<footer>
  <div class="footer-inner">
    <a href="../index.html">← Distilled</a>
    <span>Distilled</span>
  </div>
</footer>

<script>
window.addEventListener('scroll', () => {
  const h = document.body.scrollHeight - window.innerHeight;
  document.getElementById('progress').style.width = (scrollY / h * 100) + '%';
});
// PASTE AUDIO MODULE
// PAGE-SPECIFIC JS
</script>
</body>
</html>
```

---

## Section Anatomy

```html
<span class="section-marker" id="sN">Roman. — Label</span>
<h2>Full Section Title</h2>

<p>...</p>

<!-- optional callout -->
<div class="callout [note|insight|math-callout|result]">
  <div class="callout-label">Label</div>
  <p>...</p>
</div>

<!-- optional math block -->
<div class="math-block">
  <span class="eq-label">Name of this equation</span>
  <div>f(x) = ...</div>
  <span class="comment">— plain English meaning</span>
</div>

<!-- optional subsection -->
<h3>Italic Subsection</h3>
<p>...</p>

<!-- optional interactive -->
<div class="interactive-block">
  <div class="interactive-label">Interactive — What This Shows</div>
  <p class="interactive-setup">What to do. What to notice.</p>
  <!-- component -->
</div>

<!-- optional quiz -->
<div class="quiz-block">...</div>

<hr class="section-sep">
```

Markers use Roman numerals: `I.`, `II.`, `III.`
Every `<h2>` gets `id="sN"` matching its TOC `<a href="#sN">`.

---

## Callout Types

```html
<div class="callout note">         <!-- factual clarification, definition, caveat -->
<div class="callout insight">      <!-- the single "aha" moment of the section -->
<div class="callout math-callout"> <!-- inline equation that interrupts prose flow -->
<div class="callout result">       <!-- conclusion, key distinction, what to remember -->
```

Max 2 per section. At most one `insight` per section, and only at the genuine peak.
Callouts punctuate prose; they don't replace it.

---

## Math Display Block

For multi-line equations with annotation:

```html
<div class="math-block">
  <span class="eq-label">The Bellman Equation</span>
  <div>Q(s,a) = r + γ · max_a' Q(s',a')</div>
  <span class="comment">— the value of taking action a in state s equals immediate reward r
  plus discounted best future value from next state s'</span>
  <div style="margin-top:.5rem">where γ ∈ [0,1] is the discount factor</div>
  <span class="comment">— γ=0 means only care about immediate reward; γ=1 means value all future equally</span>
</div>
```

Use Unicode math throughout: `∂ ∇ Σ Π ∫ √ ∞ ∈ ⊂ ≈ ≠ ≤ ≥ → ← ↔ · × ‖ μ σ θ φ λ α β γ`
Subscripts: `<sub>`, superscripts: `<sup>`. No MathJax — keep it readable.

---

## Audio System

Include this module in every explainer for consistency — but sound is optional per-interaction. If your explainer has many rapid interactions (sliders, step-throughs), sound adds ambient texture. If your explainer is primarily prose-driven with occasional demos, sound on every interaction can feel intrusive. Use your judgment: include the audio module always (so the infrastructure is there), but don't force sound onto interactions where it adds nothing.

```javascript
// ═══════════════════════════════════════
// AUDIO — include in every explainer
// ═══════════════════════════════════════
const AC = window.AudioContext || window.webkitAudioContext;
let ac;
function getAC() { if (!ac) ac = new AC(); return ac; }

function tone(freq, type = 'sine', dur = 0.12, vol = 0.06) {
  try {
    const ctx = getAC();
    const o = ctx.createOscillator(), g = ctx.createGain();
    o.connect(g); g.connect(ctx.destination);
    o.type = type; o.frequency.value = freq;
    g.gain.setValueAtTime(0, ctx.currentTime);
    g.gain.linearRampToValueAtTime(vol, ctx.currentTime + 0.01);
    g.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + dur);
    o.start(); o.stop(ctx.currentTime + dur);
  } catch(e) {}
}

function chime(freqs, spacing = 70) {
  freqs.forEach((f, i) => setTimeout(() => tone(f, 'sine', 0.25, 0.05), i * spacing));
}

function playOK()     { chime([523, 659, 784, 1047]); }         // correct / success
function playFail()   { tone(220, 'sawtooth', 0.3, 0.06); }     // wrong / error
function playClick()  { tone(440, 'triangle', 0.08, 0.04); }    // button / toggle
function playStep()   { tone(392, 'sine', 0.08, 0.04); }        // algorithm step
function playReveal() { chime([392, 494, 587, 740], 90); }      // expand / reveal
```

**Oscillator types and when to use them:**

| Type | Character | Use for |
|------|-----------|---------|
| `sine` | Clean, warm | Data flow, correct answers, generic events |
| `triangle` | Softer | Slider movement, value changes |
| `sawtooth` | Slightly harsh | Errors, wrong answers, constraint violations |
| `square` | Retro, digital | Only when thematically fitting (binary, discrete) |

**Slider pitch formula:** `tone(baseFreq + value * scale, 'triangle', 0.08, 0.04)`
Higher param → higher pitch. This is always the direction. It feels natural.

**Volume:** keep `vol` at 0.04–0.08. These are ambient cues, not alerts.
Never play sound without a user gesture triggering it. `getAC()` defers creation safely.

**Frequency reference (musical anchors):**
```
220 Hz — low, gravity, error
330 Hz — mild, neutral
440 Hz — A4, middle ground
523 Hz — C5, pleasant
659 Hz — E5, bright
784 Hz — G5, vivid
880 Hz — A5, high
1047 Hz — C6, success
```

---

## Canvas Foundation

Every canvas-based component shares this setup. Copy it exactly.

```javascript
const c   = document.getElementById('my-canvas');
const ctx = c.getContext('2d');

function resizeCanvas() {
  // devicePixelRatio is what keeps drawings sharp on retina/HiDPI screens
  c.width  = c.offsetWidth  * devicePixelRatio;
  c.height = HEIGHT_PX      * devicePixelRatio;
  ctx.scale(devicePixelRatio, devicePixelRatio);
  draw(); // redraw immediately after resize
}
resizeCanvas();
window.addEventListener('resize', resizeCanvas);

function draw() {
  // ALWAYS use logical pixel dimensions for drawing coordinates
  const W = c.offsetWidth;   // NOT c.width
  const H = HEIGHT_PX;       // NOT c.height
  ctx.clearRect(0, 0, W, H);
  // ... drawing code using W and H ...
}
```

**Critical rule:** Draw coordinates always use `c.offsetWidth` / logical pixels.
`c.width` (physical pixels) is only used by `clearRect`. Mixing them causes blurry rendering.

**Glow effect — always reset shadowBlur:**
```javascript
ctx.shadowColor = '#c4622d';
ctx.shadowBlur  = 8;
ctx.fill();
ctx.shadowBlur  = 0;  // MUST reset — otherwise every subsequent draw inherits it
```

**Canvas visual conventions:**

| Element | Style |
|---------|-------|
| Background | white or `var(--bg)` — always light |
| Grid lines | `rgba(0,0,0,0.06)` — barely visible |
| Axis lines | `#e2dfd8` = `var(--border)` |
| Axis labels | `var(--muted)` #7a7870, 9–10px DM Mono |
| Data, primary | semantic color at 70–90% opacity |
| Data, secondary | semantic color at 30–50% opacity |
| Highlighted | full opacity + shadowBlur 4–10 |
| Connection lines | `rgba(..., 0.05–0.15)` — very faint |
| Text on canvas | DM Mono for values, DM Sans for labels |

---

## Quiz Handler

One universal function. Include once, call from every quiz.

```javascript
function handleQuiz(btn, correct, feedbackId, goodMsg, badMsg) {
  const parent = btn.parentElement;
  if (parent.dataset.done) return;
  parent.dataset.done = '1';
  parent.querySelectorAll('.quiz-opt').forEach(o => o.disabled = true);
  btn.classList.add(correct ? 'correct' : 'wrong');
  const fb = document.getElementById(feedbackId);
  fb.textContent = correct ? goodMsg : badMsg;
  fb.className = 'quiz-feedback show ' + (correct ? 'good' : 'bad');
  correct ? playOK() : playFail();
}
```

```html
<div class="quiz-block">
  <div class="quiz-q">Question text. Tests understanding, not recall.</div>
  <div class="quiz-options">
    <button class="quiz-opt"
      onclick="handleQuiz(this, false, 'qf1',
        '✓ Right. Here is why, plus a nuance.',
        '✗ This is wrong because...')">
      Plausible wrong answer — a real misconception
    </button>
    <button class="quiz-opt"
      onclick="handleQuiz(this, true, 'qf1',
        '✓ Right. Here is why, plus a nuance.',
        '✗ This is wrong because...')">
      Correct answer
    </button>
    <!-- 2–3 total options -->
  </div>
  <div class="quiz-feedback" id="qf1"></div>
</div>
```

**Quiz writing rules:**
- Test understanding: "What would happen if X?" not "What is the definition of X?"
- Wrong answers must be the actual misconceptions a skimmer would hold
- Good feedback (✓): adds a nuance or implication beyond just confirming correctness
- Bad feedback (✗): addresses the specific wrong thinking, not just the right answer

---

## Interactive Component Recipes

These 12 recipes cover the most common visualization needs. But your topic will sometimes need something none of these provide. When that happens, build it. The recipes are ingredients, not a menu. Combine them, modify them, or invent something new. The only hard rules: use the same color palette, the same fonts, and keep interactions responsive (< 50ms feedback).

### Interactive Density

Not every section needs an interactive. Some sections are better as pure prose — a story, a derivation, an argument that builds across paragraphs. An interactive in a section like that would interrupt the flow.

Conversely, some concepts are better experienced than explained. If you find yourself writing three paragraphs to describe what a slider could show in 2 seconds, build the slider and cut the paragraphs.

The right density of interactives is whatever the topic demands. For a mathematical concept like entropy, you might have 8 interactives because every property is best understood by manipulating it. For a historical/conceptual piece, you might have 2. For a worked-example-heavy section, you might have none.

### Which Recipe For Which Topic?

| Topic type | Primary recipes to use |
|-----------|------------------------|
| Algorithm (backprop, gradient descent, sorting) | Step-Through Animator, Convergence Curve, Coordinate Plane |
| Mathematical concept (entropy, Fourier, KL) | Distribution Visualizer, Function Plotter, Slider→Output |
| Architecture (transformers, CNNs, RNNs) | Heatmap/Matrix, Node Graph, Step-Through Animator |
| Learning paradigm (RL, contrastive) | Grid World / State Visualizer, Convergence Curve, Scatter Plot |
| Principle (bias-variance, double descent) | Function Plotter, Scatter Plot, Slider→Output |

---

### Recipe 1: Slider → Live Output (Universal)

The most versatile recipe. One parameter changes; something visible responds.

```javascript
// HTML
// <div class="slider-group">
//   <span>Learning Rate:</span>
//   <input type="range" id="lr-slider" min="0.001" max="1" step="0.001" value="0.1"
//          oninput="onSlider(this.value)">
//   <span class="slider-val" id="lr-val">0.1</span>
// </div>

function onSlider(v) {
  v = parseFloat(v);
  document.getElementById('lr-val').textContent = v;
  tone(150 + v * 400, 'triangle', 0.08, 0.04);  // pitch tracks value
  redraw(v);
}
```

Always show the current value next to the slider in DM Mono with `--accent` color.
Always play a tone on input — pitch correlates with value direction.

---

### Recipe 2: Coordinate Plane / Function Plotter

For: gradient descent trajectories, loss landscapes, decision boundaries,
function curves (sigmoid, ReLU, tanh), eigenvalue geometry, Fourier basis functions.

```javascript
function drawCoordinatePlane(c, ctx, {
  xMin = -2, xMax = 2, yMin = -2, yMax = 2,
  gridStep = 0.5
} = {}) {
  const W = c.offsetWidth, H = c.offsetHeight || 260;
  const pad = { l: 40, r: 16, t: 16, b: 36 };
  const gw = W - pad.l - pad.r, gh = H - pad.t - pad.b;

  // Map data coords → pixel coords
  function px(x) { return pad.l + (x - xMin) / (xMax - xMin) * gw; }
  function py(y) { return pad.t + (1 - (y - yMin) / (yMax - yMin)) * gh; }

  // Grid lines
  ctx.strokeStyle = 'rgba(0,0,0,0.06)'; ctx.lineWidth = 0.8;
  for (let x = Math.ceil(xMin / gridStep) * gridStep; x <= xMax; x += gridStep) {
    ctx.beginPath(); ctx.moveTo(px(x), pad.t); ctx.lineTo(px(x), pad.t + gh); ctx.stroke();
  }
  for (let y = Math.ceil(yMin / gridStep) * gridStep; y <= yMax; y += gridStep) {
    ctx.beginPath(); ctx.moveTo(pad.l, py(y)); ctx.lineTo(pad.l + gw, py(y)); ctx.stroke();
  }

  // Axes
  ctx.strokeStyle = '#c8c4bc'; ctx.lineWidth = 1.5;
  if (yMin < 0 && yMax > 0) {
    ctx.beginPath(); ctx.moveTo(pad.l, py(0)); ctx.lineTo(pad.l + gw, py(0)); ctx.stroke();
  }
  if (xMin < 0 && xMax > 0) {
    ctx.beginPath(); ctx.moveTo(px(0), pad.t); ctx.lineTo(px(0), pad.t + gh); ctx.stroke();
  }

  // Axis labels (sample — add as needed)
  ctx.fillStyle = '#a0998e'; ctx.font = "9px 'DM Mono'"; ctx.textAlign = 'center';
  [xMin, 0, xMax].filter(x => x >= xMin && x <= xMax).forEach(x => {
    ctx.fillText(x, px(x), pad.t + gh + 16);
  });
  ctx.textAlign = 'right';
  [yMin, 0, yMax].filter(y => y >= yMin && y <= yMax).forEach(y => {
    ctx.fillText(y, pad.l - 6, py(y) + 3);
  });

  return { px, py, gw, gh, pad }; // return helpers for drawing on top
}

// Plot a function curve on top of the plane:
function plotFunction(ctx, fn, xMin, xMax, { px, py }, color = '#c4622d', steps = 300) {
  ctx.beginPath();
  for (let i = 0; i <= steps; i++) {
    const x = xMin + (xMax - xMin) * i / steps;
    const y = fn(x);
    i === 0 ? ctx.moveTo(px(x), py(y)) : ctx.lineTo(px(x), py(y));
  }
  ctx.strokeStyle = color; ctx.lineWidth = 2.5; ctx.stroke();
}

// Plot a point:
function plotPoint(ctx, x, y, { px, py }, color = '#c4622d', r = 5) {
  ctx.beginPath(); ctx.arc(px(x), py(y), r, 0, Math.PI * 2);
  ctx.fillStyle = color; ctx.fill();
}

// Example: plot sigmoid with slider-controlled temperature
function drawSigmoid(temp) {
  const helpers = drawCoordinatePlane(c, ctx, { xMin: -6, xMax: 6, yMin: -0.1, yMax: 1.1 });
  plotFunction(ctx, x => 1 / (1 + Math.exp(-x * temp)), -6, 6, helpers, '#2a5db0');
}
```

---

### Recipe 3: Distribution Visualizer

For: entropy, KL divergence, Bayesian inference, softmax outputs, probability distributions,
sampling, the bias-variance tradeoff, any concept involving probability mass.

```javascript
// Render a discrete probability distribution as a bar chart
function drawDistribution(c, ctx, probs, {
  labels = null,
  color = '#2a5db0',
  highlight = null,    // index to highlight differently
  showEntropy = false
} = {}) {
  const W = c.offsetWidth, H = 180;
  const pad = { l: 36, r: 16, t: 24, b: 40 };
  const gw = W - pad.l - pad.r, gh = H - pad.t - pad.b;
  const n = probs.length;
  const barW = gw / n;
  const maxP = Math.max(...probs, 0.01);

  // Background gridlines
  [0.25, 0.5, 0.75, 1.0].forEach(v => {
    const y = pad.t + gh * (1 - v / maxP);
    if (y >= pad.t) {
      ctx.beginPath(); ctx.moveTo(pad.l, y); ctx.lineTo(pad.l + gw, y);
      ctx.strokeStyle = 'rgba(0,0,0,0.06)'; ctx.lineWidth = 0.8; ctx.stroke();
      ctx.fillStyle = '#a0998e'; ctx.font = "9px 'DM Mono'"; ctx.textAlign = 'right';
      ctx.fillText(v.toFixed(2), pad.l - 4, y + 3);
    }
  });

  // Bars
  probs.forEach((p, i) => {
    const barH = gh * (p / maxP);
    const x = pad.l + i * barW + barW * 0.1;
    const w = barW * 0.8;
    const y = pad.t + gh - barH;
    const isHot = highlight === i;
    ctx.fillStyle = isHot ? '#c4622d' : color + 'cc';
    ctx.fillRect(x, y, w, barH);

    // Value label
    ctx.fillStyle = '#7a7870'; ctx.font = "9px 'DM Mono'"; ctx.textAlign = 'center';
    ctx.fillText(p.toFixed(2), pad.l + i * barW + barW / 2, y - 4);

    // Category label
    if (labels) ctx.fillText(labels[i], pad.l + i * barW + barW / 2, pad.t + gh + 16);
  });

  // Entropy readout
  if (showEntropy) {
    const H_val = -probs.reduce((s, p) => s + (p > 0 ? p * Math.log2(p) : 0), 0);
    ctx.fillStyle = '#6b3fa0'; ctx.font = "10px 'DM Mono'"; ctx.textAlign = 'left';
    ctx.fillText(`H = ${H_val.toFixed(3)} bits`, pad.l, pad.t - 6);
  }
}

// Interactive: user drags sliders to set probabilities, sees entropy update live
// Normalize: function normalize(probs) { const s = probs.reduce((a,b)=>a+b,0); return probs.map(p=>p/s); }
```

---

### Recipe 4: Heatmap / Matrix Visualizer

For: attention matrices, Q-tables, confusion matrices, convolutional kernels,
correlation matrices, weight matrices, any 2D grid of values.

```javascript
function drawHeatmap(c, ctx, matrix, {
  rowLabels = null, colLabels = null,
  colorLow  = [238,243,252],   // blue-light for low values (or swap for diverging)
  colorHigh = [42,93,176],     // blue for high values
  showValues = true
} = {}) {
  const rows = matrix.length, cols = matrix[0].length;
  const W = c.offsetWidth, H = c.offsetHeight || rows * 40 + 40;
  const pad = { l: colLabels ? 60 : 8, r: 8, t: rowLabels ? 28 : 8, b: 8 };
  const cellW = (W - pad.l - pad.r) / cols;
  const cellH = (H - pad.t - pad.b) / rows;

  const flat = matrix.flat();
  const min = Math.min(...flat), max = Math.max(...flat);

  function lerp(a, b, t) { return a + (b - a) * t; }
  function cellColor(v) {
    const t = max === min ? 0.5 : (v - min) / (max - min);
    return `rgb(${Math.round(lerp(colorLow[0],colorHigh[0],t))},${Math.round(lerp(colorLow[1],colorHigh[1],t))},${Math.round(lerp(colorLow[2],colorHigh[2],t))})`;
  }

  matrix.forEach((row, r) => {
    row.forEach((val, col) => {
      const x = pad.l + col * cellW, y = pad.t + r * cellH;
      ctx.fillStyle = cellColor(val);
      ctx.fillRect(x, y, cellW - 1, cellH - 1);
      if (showValues && cellW > 28) {
        const t = max === min ? 0.5 : (val - min) / (max - min);
        ctx.fillStyle = t > 0.5 ? 'rgba(255,255,255,0.9)' : 'rgba(0,0,0,0.6)';
        ctx.font = `${Math.min(11, cellW * 0.3)}px 'DM Mono'`;
        ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
        ctx.fillText(val.toFixed ? val.toFixed(2) : val, x + cellW/2, y + cellH/2);
      }
    });
  });

  // Row labels
  if (rowLabels) {
    ctx.fillStyle = '#7a7870'; ctx.font = "9px 'DM Sans'"; ctx.textAlign = 'right'; ctx.textBaseline = 'middle';
    rowLabels.forEach((lbl, r) => ctx.fillText(lbl, pad.l - 4, pad.t + r * cellH + cellH / 2));
  }
  // Col labels
  if (colLabels) {
    ctx.fillStyle = '#7a7870'; ctx.font = "9px 'DM Sans'"; ctx.textAlign = 'center'; ctx.textBaseline = 'bottom';
    colLabels.forEach((lbl, col) => ctx.fillText(lbl, pad.l + col * cellW + cellW / 2, pad.t - 4));
  }
}
```

---

### Recipe 5: Step-Through Algorithm Animator

For: backpropagation, gradient descent steps, dynamic programming, sorting,
any algorithm with discrete, inspectable steps.

```javascript
// State machine: an array of "frames" the user steps through
let frames = [];
let currentFrame = 0;

// Build frames during algorithm simulation
function buildFrames(/* algorithm inputs */) {
  frames = [];
  // Run the algorithm, pushing a snapshot at each meaningful step:
  // frames.push({ description: "...", highlight: [...], values: {...} });
}

function renderFrame(i) {
  const f = frames[Math.min(i, frames.length - 1)];
  // Draw the current state using f.highlight, f.values, etc.
  drawCurrentState(f);
  // Update the text description
  document.getElementById('step-desc').textContent = f.description;
  document.getElementById('step-counter').textContent = `Step ${i + 1} / ${frames.length}`;
  playStep();
}

function stepForward() {
  if (currentFrame < frames.length - 1) { currentFrame++; renderFrame(currentFrame); }
}
function stepBack() {
  if (currentFrame > 0) { currentFrame--; renderFrame(currentFrame); }
}
function resetAlgo() {
  currentFrame = 0; buildFrames(); renderFrame(0); playClick();
}

// Auto-play with configurable speed
let autoTimer = null;
function toggleAuto(ms = 600) {
  if (autoTimer) { clearInterval(autoTimer); autoTimer = null; return; }
  autoTimer = setInterval(() => {
    if (currentFrame >= frames.length - 1) { clearInterval(autoTimer); autoTimer = null; playOK(); return; }
    currentFrame++; renderFrame(currentFrame);
  }, ms);
}
```

```html
<!-- Controls template for step-through demos -->
<div style="display:flex;align-items:center;gap:.5rem;flex-wrap:wrap;margin-top:.8rem;">
  <button class="btn" onclick="resetAlgo()">↺ Reset</button>
  <button class="btn" onclick="stepBack()">← Back</button>
  <button class="btn" onclick="stepForward()">Forward →</button>
  <button class="btn primary" onclick="toggleAuto()">▶ Auto-play</button>
  <span id="step-counter" style="font-family:'DM Mono',monospace;font-size:.75rem;color:var(--muted);margin-left:.5rem;"></span>
</div>
<div id="step-desc" style="margin-top:.8rem;font-family:'DM Sans',sans-serif;font-size:.82rem;color:var(--muted);line-height:1.6;min-height:2.5em;"></div>
```

---

### Recipe 6: Convergence / Training Curve

For: loss curves, reward curves (RL), accuracy over epochs, any quantity that changes
as an iterative algorithm runs.

```javascript
// Tune these for each topic:
const curveConfig = {
  A:       0.9,   // starting value
  tau:     20,    // decay constant (steps to half)
  floor:   0.08,  // residual (never reaches 0)
  noise:   0.02,  // stochastic noise
  maxStep: 80,    // total steps to animate
  frameMs: 55,    // ms per frame
  yLabel:  'Loss',
  rising:  false, // set true for reward curves (increase instead of decrease)
};

let curvePoints = [];
let curveRunning = false;

function startCurve() {
  if (curveRunning) return;
  curveRunning = true;
  curvePoints = [];
  let step = 0;
  const { A, tau, floor, noise, maxStep, frameMs, rising } = curveConfig;

  function tick() {
    const base = A * Math.exp(-step / tau) + floor + noise * (Math.random() - 0.5);
    const val = rising ? 1 - base : base;
    curvePoints.push(val);
    drawCurve(curvePoints);
    const f = rising ? 550 - val * 400 : 200 + val * 500;
    tone(f, 'sine', 0.04, 0.015);
    step++;
    if (step < maxStep) setTimeout(tick, frameMs);
    else { curveRunning = false; playOK(); }
  }
  tick();
}

function drawCurve(data) {
  const W = c.offsetWidth, H = 160;
  ctx.clearRect(0, 0, W, H);
  if (data.length < 2) return;

  const pad = { l: 44, r: 16, t: 16, b: 32 };
  const gw = W - pad.l - pad.r, gh = H - pad.t - pad.b;
  const mn = Math.min(...data) * 0.95, mx = Math.max(...data) * 1.05;
  const py = v => pad.t + gh * (1 - (v - mn) / (mx - mn));
  const px = i => pad.l + gw * i / (data.length - 1);

  // Grid lines
  [0.25, 0.5, 0.75, 1].forEach(t => {
    const y = pad.t + t * gh;
    ctx.beginPath(); ctx.moveTo(pad.l, y); ctx.lineTo(pad.l + gw, y);
    ctx.strokeStyle = 'rgba(0,0,0,0.06)'; ctx.lineWidth = 0.8; ctx.stroke();
  });

  // Area fill
  ctx.beginPath();
  data.forEach((v, i) => i === 0 ? ctx.moveTo(px(i), py(v)) : ctx.lineTo(px(i), py(v)));
  ctx.lineTo(px(data.length - 1), pad.t + gh);
  ctx.lineTo(pad.l, pad.t + gh); ctx.closePath();
  ctx.fillStyle = 'rgba(196,98,45,0.07)'; ctx.fill();

  // Line
  ctx.beginPath();
  data.forEach((v, i) => i === 0 ? ctx.moveTo(px(i), py(v)) : ctx.lineTo(px(i), py(v)));
  ctx.strokeStyle = '#c4622d'; ctx.lineWidth = 2; ctx.stroke();

  // Current value dot + readout
  const last = data[data.length - 1];
  ctx.beginPath(); ctx.arc(px(data.length - 1), py(last), 4, 0, Math.PI * 2);
  ctx.fillStyle = '#c4622d'; ctx.fill();
  document.getElementById('curve-readout').textContent =
    `${curveConfig.yLabel}: ${last.toFixed(4)} | Step: ${data.length}`;
}
```

---

### Recipe 7: Scatter Plot / 2D Embedding Visualizer

For: dimensionality reduction (t-SNE, PCA, autoencoders), clustering, classification
boundaries, showing how representations change, any 2D data cloud.

```javascript
function drawScatter(c, ctx, points, {
  colorFn = () => '#2a5db0',   // (point, index) => color string
  rFn     = () => 4,           // (point, index) => radius
  xMin = null, xMax = null,
  yMin = null, yMax = null,
  labels = null                // optional {x,y,text} label overlays
} = {}) {
  const W = c.offsetWidth, H = c.offsetHeight || 260;
  const pad = { l: 36, r: 16, t: 16, b: 36 };
  const gw = W - pad.l - pad.r, gh = H - pad.t - pad.b;

  const xs = points.map(p => p.x);
  const ys = points.map(p => p.y);
  const x0 = xMin ?? (Math.min(...xs) - 0.5);
  const x1 = xMax ?? (Math.max(...xs) + 0.5);
  const y0 = yMin ?? (Math.min(...ys) - 0.5);
  const y1 = yMax ?? (Math.max(...ys) + 0.5);

  function px(x) { return pad.l + (x - x0) / (x1 - x0) * gw; }
  function py(y) { return pad.t + (1 - (y - y0) / (y1 - y0)) * gh; }

  // Axes
  ctx.strokeStyle = '#e2dfd8'; ctx.lineWidth = 1;
  ctx.beginPath(); ctx.moveTo(pad.l, pad.t + gh); ctx.lineTo(pad.l + gw, pad.t + gh); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(pad.l, pad.t); ctx.lineTo(pad.l, pad.t + gh); ctx.stroke();

  // Points
  points.forEach((p, i) => {
    const color = colorFn(p, i);
    const r = rFn(p, i);
    ctx.beginPath(); ctx.arc(px(p.x), py(p.y), r, 0, Math.PI * 2);
    ctx.fillStyle = color + 'cc'; ctx.fill();
    ctx.strokeStyle = color; ctx.lineWidth = 1; ctx.stroke();
  });

  // Labels
  if (labels) {
    ctx.font = "9px 'DM Sans'"; ctx.fillStyle = '#7a7870';
    labels.forEach(l => { ctx.fillText(l.text, px(l.x) + 6, py(l.y) + 3); });
  }
}

// Color by class — for classification / clustering visualizations
const CLASS_COLORS = ['#2a5db0', '#c4622d', '#1a7a4a', '#6b3fa0', '#8b5e0a', '#a0290a'];
function classColor(classIndex) { return CLASS_COLORS[classIndex % CLASS_COLORS.length]; }
```

---

### Recipe 8: Waveform / Signal Visualizer

For: Fourier transform, convolution, filtering, signal processing, any time-series
or frequency-domain concept.

```javascript
// Draw a waveform in time domain
function drawWaveform(c, ctx, signalFn, {
  tMin = 0, tMax = 4 * Math.PI,
  yMin = -1.5, yMax = 1.5,
  color = '#2a5db0',
  steps = 800,
  label = ''
} = {}) {
  const W = c.offsetWidth, H = c.offsetHeight || 120;
  const pad = { l: 8, r: 8, t: 12, b: 12 };
  const gw = W - pad.l - pad.r, gh = H - pad.t - pad.b;
  const py = y => pad.t + gh * (1 - (y - yMin) / (yMax - yMin));

  // Zero line
  ctx.beginPath(); ctx.moveTo(pad.l, py(0)); ctx.lineTo(pad.l + gw, py(0));
  ctx.strokeStyle = 'rgba(0,0,0,0.1)'; ctx.lineWidth = 0.8; ctx.stroke();

  // Signal
  ctx.beginPath();
  for (let i = 0; i <= steps; i++) {
    const t = tMin + (tMax - tMin) * i / steps;
    const y = signalFn(t);
    const x = pad.l + gw * i / steps;
    i === 0 ? ctx.moveTo(x, py(y)) : ctx.lineTo(x, py(y));
  }
  ctx.strokeStyle = color; ctx.lineWidth = 2; ctx.stroke();

  if (label) {
    ctx.fillStyle = color + 'aa'; ctx.font = "9px 'DM Mono'";
    ctx.textAlign = 'left'; ctx.fillText(label, pad.l + 4, pad.t + 12);
  }
}

// Draw frequency spectrum (magnitude vs frequency)
function drawSpectrum(c, ctx, magnitudes, freqLabels = null, color = '#6b3fa0') {
  const W = c.offsetWidth, H = c.offsetHeight || 120;
  const pad = { l: 8, r: 8, t: 12, b: 24 };
  const n = magnitudes.length;
  const gw = W - pad.l - pad.r, gh = H - pad.t - pad.b;
  const maxMag = Math.max(...magnitudes, 0.01);

  magnitudes.forEach((m, i) => {
    const barW = gw / n * 0.7;
    const x = pad.l + gw * i / n + (gw / n - barW) / 2;
    const barH = gh * (m / maxMag);
    ctx.fillStyle = color + 'cc';
    ctx.fillRect(x, pad.t + gh - barH, barW, barH);
    if (freqLabels && gw / n > 20) {
      ctx.fillStyle = '#7a7870'; ctx.font = "8px 'DM Mono'"; ctx.textAlign = 'center';
      ctx.fillText(freqLabels[i], x + barW / 2, pad.t + gh + 14);
    }
  });
}

// Example: interactive frequency mixer
// Slider sets amplitude of each harmonic; sum waveform updates live
function computeMixedSignal(amplitudes, t) {
  return amplitudes.reduce((sum, a, k) => sum + a * Math.sin((k + 1) * t), 0);
}
```

---

### Recipe 9: Node Graph Visualizer

For: computation graphs, dependency graphs, Markov chains, decision trees,
parse trees, any directed or undirected graph with nodes and edges.
(Also applies to neural networks, but written generally here.)

```javascript
// General node graph — NOT specific to neural networks
// Nodes: [{ id, x, y, label, color, r }]
// Edges: [{ from, to, label, weight, directed }]

function drawGraph(c, ctx, nodes, edges, { t = 0, highlightIds = new Set() } = {}) {
  const W = c.offsetWidth, H = c.offsetHeight || 300;

  // Edges first (drawn under nodes)
  edges.forEach(e => {
    const a = nodes.find(n => n.id === e.from);
    const b = nodes.find(n => n.id === e.to);
    if (!a || !b) return;

    ctx.beginPath(); ctx.moveTo(a.x * W, a.y * H); ctx.lineTo(b.x * W, b.y * H);
    const hot = highlightIds.has(e.from) && highlightIds.has(e.to);
    ctx.strokeStyle = hot ? '#c4622d88' : 'rgba(0,0,0,0.08)';
    ctx.lineWidth = e.weight ? Math.abs(e.weight) * 2 + 0.5 : 1;
    ctx.stroke();

    // Arrow for directed edges
    if (e.directed) {
      const angle = Math.atan2(b.y - a.y, b.x - a.x);
      const tx = b.x * W - (b.r || 16) * Math.cos(angle);
      const ty = b.y * H - (b.r || 16) * Math.sin(angle);
      ctx.beginPath();
      ctx.moveTo(tx, ty);
      ctx.lineTo(tx - 10 * Math.cos(angle - 0.3), ty - 10 * Math.sin(angle - 0.3));
      ctx.lineTo(tx - 10 * Math.cos(angle + 0.3), ty - 10 * Math.sin(angle + 0.3));
      ctx.closePath(); ctx.fillStyle = hot ? '#c4622daa' : 'rgba(0,0,0,0.15)'; ctx.fill();
    }

    // Edge label
    if (e.label) {
      const mx = (a.x + b.x) / 2 * W, my = (a.y + b.y) / 2 * H;
      ctx.fillStyle = '#7a7870'; ctx.font = "9px 'DM Mono'"; ctx.textAlign = 'center';
      ctx.fillText(e.label, mx, my - 6);
    }
  });

  // Nodes
  nodes.forEach(n => {
    const nx = n.x * W, ny = n.y * H, r = n.r || 18;
    const hot = highlightIds.has(n.id);
    const pulse = hot ? 1 + 0.08 * Math.sin(t * 0.06) : 1;

    ctx.beginPath(); ctx.arc(nx, ny, r * pulse + 4, 0, Math.PI * 2);
    ctx.fillStyle = (n.color || '#2a5db0') + '18'; ctx.fill();

    ctx.beginPath(); ctx.arc(nx, ny, r * pulse, 0, Math.PI * 2);
    ctx.fillStyle = (n.color || '#2a5db0') + (hot ? 'ee' : '88');
    ctx.strokeStyle = n.color || '#2a5db0'; ctx.lineWidth = 1.5;
    if (hot) { ctx.shadowColor = n.color || '#2a5db0'; ctx.shadowBlur = 10; }
    ctx.fill(); ctx.stroke(); ctx.shadowBlur = 0;

    ctx.fillStyle = hot ? 'white' : (n.color || '#2a5db0');
    ctx.font = `bold ${Math.min(11, r * 0.65)}px 'DM Sans'`;
    ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
    ctx.fillText(n.label || n.id, nx, ny);
  });
}
```

---

### Recipe 10: Grid World / State Visualizer

For: reinforcement learning (MDPs, value functions, Q-tables, policy visualization),
cellular automata, dynamic programming, any problem with a discrete state grid.

```javascript
function drawGridWorld(c, ctx, grid, {
  cellPx = 60,
  colorFn = (v) => {  // maps cell value to color
    if (v === 'wall') return '#2a2a28';
    if (v === 'goal') return '#1a7a4a';
    if (v === 'agent') return '#c4622d';
    return null;
  },
  valueFn = null,    // optional: (row, col) => number to display
  arrowFn = null,    // optional: (row, col) => 'up'|'down'|'left'|'right'|null
} = {}) {
  const rows = grid.length, cols = grid[0].length;
  const W = cols * cellPx, H = rows * cellPx;

  grid.forEach((row, r) => {
    row.forEach((cell, col) => {
      const x = col * cellPx, y = r * cellPx;
      const bg = colorFn(cell);
      ctx.fillStyle = bg || '#f3f1ec';
      ctx.fillRect(x, y, cellPx, cellPx);
      ctx.strokeStyle = '#e2dfd8'; ctx.lineWidth = 1;
      ctx.strokeRect(x, y, cellPx, cellPx);

      // Value overlay
      if (valueFn) {
        const v = valueFn(r, col);
        if (v !== null) {
          const absMax = 1;
          const norm = Math.max(-1, Math.min(1, v / absMax));
          if (norm > 0) ctx.fillStyle = `rgba(42,93,176,${norm * 0.4})`;
          else ctx.fillStyle = `rgba(196,98,45,${-norm * 0.4})`;
          ctx.fillRect(x, y, cellPx, cellPx);
          ctx.fillStyle = '#2a2a28'; ctx.font = `${cellPx * 0.22}px 'DM Mono'`;
          ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
          ctx.fillText(v.toFixed(1), x + cellPx / 2, y + cellPx / 2);
        }
      }

      // Policy arrows
      if (arrowFn) {
        const dir = arrowFn(r, col);
        if (dir) {
          const cx = x + cellPx / 2, cy = y + cellPx / 2;
          const len = cellPx * 0.28;
          const dx = { right:1,left:-1,up:0,down:0 }[dir] * len;
          const dy = { down:1,up:-1,left:0,right:0 }[dir] * len;
          ctx.strokeStyle = '#2a5db0'; ctx.lineWidth = 2;
          ctx.beginPath(); ctx.moveTo(cx - dx * 0.5, cy - dy * 0.5);
          ctx.lineTo(cx + dx * 0.5, cy + dy * 0.5); ctx.stroke();
          // Arrowhead
          ctx.fillStyle = '#2a5db0';
          const ang = Math.atan2(dy, dx);
          ctx.beginPath();
          ctx.moveTo(cx + dx * 0.5, cy + dy * 0.5);
          ctx.lineTo(cx + dx*0.5 - 8*Math.cos(ang-0.4), cy + dy*0.5 - 8*Math.sin(ang-0.4));
          ctx.lineTo(cx + dx*0.5 - 8*Math.cos(ang+0.4), cy + dy*0.5 - 8*Math.sin(ang+0.4));
          ctx.closePath(); ctx.fill();
        }
      }
    });
  });
}
```

---

### Recipe 11: Drawing Canvas (User Input)

For: any demo where the reader creates an input — drawing a digit, a curve, a shape —
that the system then transforms. Use when making input-output relationships tangible.

```javascript
const dc = document.getElementById('draw-canvas');
const dctx = dc.getContext('2d');
let isDrawing = false;

dc.addEventListener('mousedown', e => { isDrawing = true; paint(e); });
dc.addEventListener('mousemove', e => { if (isDrawing) paint(e); });
dc.addEventListener('mouseup',   () => { isDrawing = false; onDrawEnd(); });
dc.addEventListener('mouseleave',() => { isDrawing = false; onDrawEnd(); });
// Touch support — always include
dc.addEventListener('touchstart', e => { isDrawing = true; paint(e.touches[0]); }, { passive: true });
dc.addEventListener('touchmove',  e => { if (isDrawing) paint(e.touches[0]); },   { passive: true });
dc.addEventListener('touchend',   () => { isDrawing = false; onDrawEnd(); });

function paint(e) {
  const r = dc.getBoundingClientRect();
  const x = e.clientX - r.left, y = e.clientY - r.top;
  dctx.fillStyle = '#1c1c1a';
  dctx.beginPath(); dctx.arc(x, y, 6, 0, Math.PI * 2); dctx.fill();
  tone(300 + y * 1.2, 'sine', 0.05, 0.02);  // pitch tracks pen position
}

function clearCanvas() { dctx.clearRect(0, 0, dc.width, dc.height); playClick(); }

// Get pixel data as a flat normalized array (useful for any processing)
function getPixels() {
  const id = dctx.getImageData(0, 0, dc.width, dc.height);
  const px = [];
  for (let i = 0; i < id.data.length; i += 4) px.push(id.data[i + 3] / 255);
  return px;
}
```

---

### Recipe 12: Toggle A/B

For: before/after comparisons, method A vs. method B, showing what changes
when you add or remove one ingredient.

```javascript
let mode = 'a';

const modeExplanations = {
  a: 'Explanation of what mode A shows and why.',
  b: 'Explanation of what mode B shows and why.',
};

function setMode(m) {
  mode = m;
  document.getElementById('btn-a').className = m === 'a' ? 'btn primary' : 'btn';
  document.getElementById('btn-b').className = m === 'b' ? 'btn primary' : 'btn';
  document.getElementById('mode-explanation').innerHTML = modeExplanations[m];
  redraw();
  playClick();
}
```

```html
<div style="display:flex;gap:.4rem;flex-wrap:wrap;margin-top:.8rem;">
  <button class="btn primary" id="btn-a" onclick="setMode('a')">Mode A</button>
  <button class="btn"         id="btn-b" onclick="setMode('b')">Mode B</button>
</div>
<div id="mode-explanation" style="margin-top:.8rem;font-family:'DM Sans',sans-serif;
  font-size:.8rem;color:var(--muted);line-height:1.6;"></div>
```

---

## Responsive Design

- Mobile breakpoint: `@media(max-width: 640px)`
- Multi-column demo layouts: collapse to `grid-template-columns: 1fr` on mobile
- Canvas heights: fixed px — canvases reflow naturally via `width: 100%`
- Touch events: always add alongside mouse events (Recipe 11 shows the pattern)
- Some demos need `overflow-x: auto` on very small screens — add as needed
- Test at 375px minimum width

---

## Button Conventions

```html
<button class="btn primary" onclick="fn()">▶ Start</button>   <!-- primary CTA -->
<button class="btn"         onclick="fn()">↺ Reset</button>   <!-- secondary -->
```

Unicode icons only — no emoji:
- `▶` — play, start, trigger
- `↺` — reset, restart
- `←` `→` — back, forward (for step-through)
- `◈` — toggle mode

---

## Footnote Pattern

```html
<!-- Inline in prose -->
<sup title="Brief preview">[N]</sup>

<!-- At end of .prose -->
<div class="footnotes">
  <div class="footnotes-title">Notes</div>
  <div class="footnote">
    <span class="fn-num">[1]</span>
    <span>Full text. Author, Year. "Title." <em>Journal</em>.
    Footnotes add real value: a source, a nuance, a correction, an interesting aside.</span>
  </div>
</div>
```

Every explainer should have footnotes — this is a rigorous project. The count depends on the topic.
Use for: citations, mathematical asides, caveats to simplifications, tangents that would
break the prose rhythm.

---

## Hub Page — Adding a New Explainer Card

```html
<a href="./topic-slug/index.html" class="explainer-card">
  <div class="card-num">0N</div>
  <div>
    <div class="card-meta">
      <span class="card-category">Category</span>
      <span class="card-date">2025</span>
    </div>
    <div class="card-title">Topic — Subtitle</div>
    <p class="card-desc">
      2–3 sentences that earn the click. What is the question this answers?
      What will the reader understand after reading that they couldn't derive before?
    </p>
    <div class="card-footer">
      <div class="card-tags">
        <span class="tag">Tag 1</span>
        <span class="tag">Tag 2</span>
      </div>
      <span class="card-arrow">→</span>
    </div>
  </div>
</a>
```

Move the corresponding "Coming Soon" entry to the published list. Renumber `card-num`.

---

## Pre-Ship Checklist

**Canvas / Rendering**
- [ ] All canvases use `devicePixelRatio` scaling (no blurry drawings on retina)
- [ ] `window.addEventListener('resize', resizeCanvas)` on every canvas
- [ ] `ctx.shadowBlur = 0` reset immediately after every glow effect

**Interaction**
- [ ] Audio module included (even if not all interactions use sound)
- [ ] Sound on interactions where it adds texture (sliders, step-throughs); omitted where it would intrude
- [ ] Touch events on all drawing/draggable surfaces
- [ ] Immediate feedback (< 50ms) on all user actions
- [ ] All interactive demos have a meaningful default state before any interaction

**Prose / Structure**
- [ ] Quiz feedback explains *why*, not just right/wrong
- [ ] TOC `href="#sN"` anchors match `id="sN"` on every `<h2>`
- [ ] Footnotes exist and add real value (not repeated prose)
- [ ] Progress bar wired up correctly
- [ ] Back link points to `../index.html`

**Design**
- [ ] No dark backgrounds anywhere
- [ ] Not using Inter, Roboto, Arial, or any font other than the three specified
- [ ] No color outside the defined palette
- [ ] Mobile layout tested at 375px width

**Shipping**
- [ ] Hub `index.html` updated with new card
- [ ] "Coming Soon" placeholder removed for this topic

---

*Companion file: `CLAUDE.md` — how to think about, structure, and write an explainer.*

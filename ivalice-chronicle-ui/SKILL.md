---
name: ivalice-chronicle-ui
description: Build frontends in a hand-painted "illuminated chronicle" style inspired by Final Fantasy Tactics — Akihiko Yoshida's watercolor illustration work and the Brave Story journal pages, NOT the in-game battle menus. Aged paper, watercolor blooms, ink-line borders, manuscript typography, storybook copywriting. Use this skill whenever the user asks for FFT-style, Ivalice-style, watercolor, parchment, chronicle, journal, storybook, illuminated manuscript, or medieval-fantasy UI — or whenever they reference Final Fantasy Tactics artwork as a design direction, even vaguely. Also use it when redesigning an existing app into this aesthetic.
---

# Ivalice Chronicle UI

A design language that makes a web app look like a page from a hand-illustrated medieval chronicle: watercolor pigment settling into aged paper, thin hand-ruled ink lines, manuscript typography. The reference is FFT's *illustration* art (Yoshida's watercolor portraits, the War of the Lions animated cutscenes, the Brave Story journal) — explicitly NOT the in-game battle UI (no hard gold double-borders, no black stone menu bars, no cursor-arrow tabs, no equipment stat grids). If you catch yourself building game menus, stop and repaint.

## Core principle

Every surface is paper, every line is ink, every color is a watercolor wash. Nothing should look machined, stamped, or backlit. Ask of each element: "could a scribe with a quill and a small watercolor set have made this?" If no, soften it until yes.

## Palette

Aged paper bases:

```css
--paper:  #F4ECD8;   /* main canvas */
--paper2: #EDE2C8;
--paper3: #E2D4B2;
```

Ink browns (never pure black, never pure gray):

```css
--ink:       #3A2A1A;             /* primary text */
--ink2:      #5C4630;             /* secondary text, button borders */
--ink3:      #8C7456;             /* muted text, labels */
--ink-line:  rgba(74,54,34,.35);  /* ruled borders */
--ink-faint: rgba(74,54,34,.15);  /* inner second rule, hairlines */
```

Yoshida watercolor accents — muted, desaturated, never neon. Each has a mid tone and a deep tone:

```css
--w-sage:  #8FA882;  --w-sage-d:  #5F7A54;   /* primary action color */
--w-blue:  #7E96A8;  --w-blue-d:  #4E6A80;
--w-ochre: #C8A35C;  --w-ochre-d: #9A7430;
--w-rust:  #B07050;  --w-rust-d:  #8A4A2E;   /* warnings, active indicators */
--w-plum:  #9A7E96;
```

Semantic mapping when categorizing items (medications, file types, statuses, anything): assign one watercolor hue per category and keep it consistent — sage for one, blue for another, ochre, rust, plum. Deep tone for text/glyphs, mid tone at low opacity for fills.

## The paper itself

The body background is never flat. Layer three things:

1. Base paper color
2. Watercolor blooms — 3 to 4 large radial-gradient ellipses in different accent hues at 8 to 13 percent opacity, placed asymmetrically at corners/edges, like pigment that bled into wet paper:

```css
background-image:
  radial-gradient(ellipse 60% 38% at 8% 4%,   rgba(143,168,130,.13) 0%, transparent 70%),
  radial-gradient(ellipse 48% 30% at 95% 14%, rgba(200,163,92,.11) 0%, transparent 70%),
  radial-gradient(ellipse 55% 35% at 12% 88%, rgba(126,150,168,.10) 0%, transparent 70%);
```

3. Paper grain — a fixed full-viewport SVG fractal-noise overlay at ~4-5 percent opacity, `pointer-events:none`:

```css
body::before{
  content:'';position:fixed;inset:0;pointer-events:none;z-index:1;
  background-image:url("data:image/svg+xml,%3Csvg viewBox='0 0 240 240' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.045'/%3E%3C/svg%3E");
}
```

App content sits above the grain (`position:relative;z-index:2`).

## Typography

Three faces, all from Google Fonts:

- **Cormorant Garamond** (400–600, plus italics) — display headings, large numbers, entry titles. Italic weight 400–500 for emotive headings.
- **IM Fell English** (regular + italic) — body text. The italic carries most descriptive/secondary text; it reads as a scribe's annotation. This face IS the manuscript feel — don't substitute Georgia.
- **IM Fell English SC** (small caps) — labels, section markers, buttons, tab text. Use with letter-spacing .1em–.3em. This replaces the bold-uppercase-sans-label reflex entirely.

No sans-serif anywhere. No font-weight 700+ — emphasis comes from size, italics, and small caps, not boldness.

## Component recipes

### Pages (cards/panels)

Translucent paper rectangle, one outer ink rule plus one faint inner rule (hand-ruled double line), soft warm shadow. No border-radius, or 1–2px at most.

```css
.page{
  background:rgba(248,241,222,.62);
  border:1px solid var(--ink-line);
  box-shadow:0 1px 0 rgba(255,252,240,.6) inset, 2px 3px 14px rgba(74,54,34,.10);
  position:relative; padding:20px;
}
.page::after{
  content:'';position:absolute;inset:5px;
  border:1px solid var(--ink-faint);pointer-events:none;
}
```

Optional title treatment: small-caps centered title + italic subtitle, like a chapter heading.

### Watercolor blobs (icon backgrounds, category markers)

Two stacked pseudo-element layers with *irregular* border-radius so no two look identical, low opacity, slight blur on the outer one:

```css
/* outer wash */  border-radius:52% 48% 56% 44% / 48% 54% 46% 52%; opacity:.35; filter:blur(1.5px);
/* inner wash */  border-radius:46% 54% 44% 56% / 54% 46% 56% 44%; opacity:.45;
```

Vary the radius percentages per instance. A perfectly round or identical blob breaks the hand-painted illusion.

### Wash strips

A 6–8px vertical gradient strip (`linear-gradient(180deg, mid-hue, deep-hue)`) at ~50 percent opacity down the left edge of a card — a painter's color key in the margin. Use for category-coded list cards.

### Ink rules (dividers)

Centered fleuron between two fading hairlines:

```css
.ink-rule::before,.ink-rule::after{
  content:'';flex:1;max-width:90px;height:1px;
  background:linear-gradient(90deg,transparent,var(--ink-line),transparent);
}
```

Fleuron glyphs: ❦ ☙ ❧ ✦ ◉ ☷ ※. Use ※ for marginal warnings.

### Buttons

Quiet ink-stamped outlines, small caps, letter-spaced. Primary action gets a solid deep-sage fill. Hover: faint ink tint plus soft shadow — never a glow.

```css
.btn-ink{ border:1.5px solid var(--ink2); background:transparent; color:var(--ink2);
  font-family:'IM Fell English SC'; letter-spacing:.14em; padding:13px 20px; }
.btn-ink.primary{ background:var(--w-sage-d); border-color:var(--w-sage-d); color:#F2EEDC; }
```

### Tabs

Journal-index words, NOT boxed buttons. Small caps, muted ink, gap-separated, centered. Active tab gets a short rust-colored 2px underline stroke that scales in (`transform:scaleX`) — like a pen stroke under a word. No backgrounds, no borders, no cursor arrows.

### Timeline / schedule entries

Each row: a large Cormorant time numeral with a tiny italic am/pm beneath, a small watercolor dot in the category hue, then the entry text (title in Cormorant 600, detail line in Fell italic). Rows divided by `--ink-faint` hairlines, not boxes.

### Bar charts (progress, tapers, quantities)

Ink-wash bars: watercolor hue at ~65 percent opacity with an organic top edge —

```css
border-radius:40% 45% 8% 10% / 30% 35% 8% 8%;
```

Past = desaturated tan (#B5A582), current = deep accent at higher opacity, future = mid accent. Label the chart in small caps with narrative phrasing ("The Lessening, day by day" rather than "Taper schedule").

### Notices and warnings

Marginalia, not alert boxes: 2px left border in rust, 5–7 percent rust background tint, italic Fell text in deep rust, ※ glyph. Never red banners, never icons-in-filled-circles.

### Bottom sheets (settings, chat)

Paper continues — same background and blooms as the body, top edge is a 1.5px ink rule, soft upward shadow. Slide up with `cubic-bezier(.32,.72,0,1)`. A thin ink handle bar, centered small-caps title.

### Chat as correspondence

If the app has a chat/assistant, frame it as letters: user messages in faint sage-tinted paper, assistant messages on near-white paper with faint ink border, both squared. Input is an underline-only field (no box) with a quill 🖋 send glyph. "Clear chat" becomes "burn." Suggested prompts are em-dash-prefixed italic lines with a hairline underneath, not pill buttons.

### Loading states

Ink blooming: 2–3 concentric irregular-radius rings expanding and fading (`@keyframes` scale .3→2.4 with opacity to 0), a gently bobbing 🖋 in the center. Rotating narrative status lines every ~2.4s ("The scribe takes up the quill...").

## Motion

Sparse and soft. Screen transitions: 350–400ms fade-up (`translateY(10px)→0`) with `cubic-bezier(.16,1,.3,1)`. Hovers: 200–250ms. Nothing bounces, nothing spins, nothing pulses except loading states. Honor `prefers-reduced-motion` when the project warrants it.

## Voice and copywriting

In-world storybook phrasing for chrome and labels: "This Day" / "Days Ahead", "The First Leaf", "set down by the hand of", addressing the user as "traveller." Two hard rules:

1. **Critical information stays plain.** Anything the user acts on — dosages, amounts, times, prices, errors that need fixing — is written in modern plain language. The costume is for labels and flourishes, never for instructions. "Take 2 tablets after breakfast" is never rewritten into Middle English.
2. **Offer a plain-language toggle if the audience is vulnerable or non-native-English.** For medical, financial, or emergency-adjacent apps, suggest to the user that the themed copy be toggleable or used only on non-critical surfaces, and note this tradeoff explicitly in your response.

## Anti-patterns (the FFT trap)

These read as "game menu," not "painted chronicle" — do not use them:

- Hard gold/brass double borders with corner ornaments
- Black or dark stone header bars with gold text
- ► cursor arrows on tabs or buttons
- Equipment-card stat grids with key/value cells and cell borders
- Pixel fonts, bold condensed sans, or any font-weight ≥700
- Pure black (#000), pure white (#FFF), saturated RGB primaries
- Border-radius above ~2px on panels (blobs are the exception)
- Glows, neon, gradients-as-decoration, glassmorphism, drop shadows in cool gray

Also avoid generic-AI tells: purple-blue gradients, Inter/Roboto, emoji-as-icons in headers (a few thematic glyphs ❦ ✍ 🖋 are fine as accents).

## Workflow

1. If a `frontend-design` or similar baseline skill is available, read it first for general craft rules; this skill overrides its aesthetic choices.
2. Define the category→hue mapping for the project's domain objects before writing components.
3. Build the paper (background blooms + grain) before any component — it sets the canvas everything else must harmonize with.
4. Build one full screen, then audit it against the anti-pattern list above before continuing.
5. If porting this into React/JSX for deployment (e.g., Amplify), keep all styling inline or in a single `<style>` block — this language doesn't need Tailwind and fights with it.

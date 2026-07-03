# Handoff: ALTERA — Y2K Fashion Brand Site (Hero → About → Playground → Showcase → Waitlist)

## Overview
ALTERA is a **real Y2K-inspired clothing brand for real people** with a digital / pixel / Y2K
visual identity that must read as a **premium fashion brand — NOT a game**. This handoff covers a
single long-scroll marketing site with a cinematic, scroll-driven narrative that runs continuously
from a video hero, through an "About/Essence" section, a "Build Your Alter Ego" styling studio, a
3D image-globe "Showcase," and a "Join the Waitlist" CTA, ending in a big glowing wordmark footer.

**No game/player/avatar/level/stats/user language. No emojis. No colorful backgrounds. No
SVG-drawn characters/mascots.** Fashion language only: look, drop, piece, archive, collection,
detail, silhouette, release, selected look.

## About the Design Files
The files in this bundle are **design references authored in HTML** — a working prototype that
demonstrates the intended look, motion, and behavior. They are **not production code to copy
verbatim.** The task is to **recreate this experience in the target codebase's environment** using
its established patterns (e.g. React + a scroll/animation lib such as GSAP ScrollTrigger or
Framer Motion + Lenis for smooth scroll). If no environment exists yet, pick the best fit —
this design leans on custom canvas-free 3D math + `requestAnimationFrame`, which ports cleanly to
React with refs + a single rAF loop, or to GSAP timelines.

> Note on the prototype's tech: it is built as a "Design Component" (`.dc.html`) that runs on a
> proprietary in-house runtime (`support.js`) — a small React-like template renderer. **Do not try
> to reuse `support.js` or the `.dc.html` template syntax in production.** Read the file for markup
> structure, exact styles, and the logic class (all the animation math lives there in plain JS),
> then reimplement in your stack. `support.js` is included only so the prototype opens in a browser.

## Fidelity
**High-fidelity (hifi).** Final colors, typography, spacing, motion timing, and interactions are all
intentional. Recreate pixel-accurately, then re-wire the scroll choreography using the target
stack's animation tools. Exact values are documented below and inline in the HTML (styles are all
inline on elements).

---

## How to open the prototype
Open `ALTERA Hero.dc.html` in a browser (it self-loads `support.js`). Scroll slowly top-to-bottom
to see the full choreography. Videos are blob-loaded for reliable scroll-scrubbing; first load
takes a few seconds (~30 MB of video).

---

## Global systems (apply across all sections)

### Fonts
- **`Space Mono`** — body / monospace UI (labels, nav, paragraphs). Loaded via Google Fonts.
- **`Pixelify Sans`** — pixel accent font, used ONLY for select highlight words
  ("EVERY VERSION OF YOU" in the hero headline, "RED SIGNAL" typewriter, "case" in SHOWCASE,
  "Altera Drop." in the waitlist headline). Loaded via Google Fonts.

### Color tokens
| Token | Hex | Use |
|---|---|---|
| Red accent | `#E23B3E` | primary accent, corner brackets, active states, glow |
| Rose highlight | `#FF5A5C` | occasional lighter red in glow blobs |
| Off-white | `#FFFCFC` / `#F2F2F4` | primary text on dark |
| Light text | `#B6B6BA` / `#9A9A9F` | secondary text on dark |
| Dark text | `#161618` / `#1A1A1C` | text on the light Playground |
| Dark bg stack | `#1A1A1E → #101012 → #060608` | About, Showcase sphere, Waitlist, Footer |
| Light studio bg | `#F3F3F5 → #E7E7EA → #DEDEDF` | Playground + waterfall intro |
| Card border (dark) | `rgba(120,120,126,.5)` | archive card borders |
| Grid/line grey | `#C9C9CD`, `#2A2A30`, `#34343A` | dividers, frames |

Keep red usage small and intentional. Backgrounds are grey/dark gradients; never introduce new hues.

### TV / scanline texture (global overlay)
Two fixed full-viewport overlays sit above content (below nav/dock), `pointer-events:none`:
- `#alt-scanlines`: `repeating-linear-gradient(to bottom, rgba(255,255,255,.05) 0 1px, rgba(0,0,0,.10) 1px 3px)`, `mix-blend-mode:overlay`, `opacity:.5`, `z-index:118`.
- `#alt-scanflicker`: finer lines, `opacity:.06`, `z-index:117`, animated with a `tvRoll` keyframe
  (`@keyframes tvRoll{0%{transform:translateY(0)}100%{transform:translateY(6px)}}`, `.5s steps(3) infinite`) for a subtle CRT roll.

### Pixel particles (free-flying, cursor-fleeing)
Small pixel squares / sparkle-stars drift on constant velocity, bounce off their container edges,
and **flee the cursor** (within a 150px radius they accelerate away, capped at 4.5px/frame, then
ease back to base drift). Present in: Hero, About/Essence, Playground, **Showcase**, **Waitlist**.
Physics: one `stepSet(list, boundsW, boundsH, cursorX, cursorY)` function per rAF tick; each
particle stores `{x,y,vx,vy,ox,oy,w,h,sp}` and applies `transform: translate(x-ox, y-oy)`.
Disabled on mobile (no cursor). Keep density subtle — premium, not busy.

### Fixed chrome
- **Top nav (`#alt-nav`)**: fixed, appears only near the very top (hero). Once scrolled past ~520px
  it hides (`translateY(-102%)`) and **stays hidden even when scrolling back up**. On the hero it is
  transparent; when scrolled it condenses (padding 30px→15px) with a translucent blur backdrop.
  Links: About → `#alt-essence`, Playground → `#alt-playground`, center logo → `#alt-pin` (top),
  Collection → `#alt-showcase`, Waitlist → `#alt-waitlist`.
- **Right-edge vertical dock (`#alt-dock`)**: fixed, **hidden on the hero, fades in once scrolled
  past ~520px, then stays visible through everything (including full-screen transitions — it sits at
  the top z-layer, `z-index:2147483000`).** Compact vertical bar pinned to the right-middle. Nav
  labels are set sideways (`writing-mode:vertical-rl`, 9px, letter-spacing .24em): Home, About,
  Studio, Showcase, Waitlist — active section highlights red. Below a divider, an **icon-only sound
  toggle** (3-bar EQ glyph; no text label). Sound: `<audio src="altera/ambient.mp3" loop>` — no
  autoplay; only plays after the user clicks the toggle (file may not exist yet — guarded). When on,
  the EQ bars animate via `@keyframes eqPulse`.
- **Loader (`#alt-loader`)**: fixed, right side, vertically centered, small. Only visible during the
  Showcase waterfall-in (fades in at scroll-progress ~0, out by ~0.4). Shows `LOADING` + a
  zero-padded percentage (`000%`→`100%`) mapped to showcase scroll progress 0→0.30, plus an 88px red
  progress bar. Positioned left of the dock so they never overlap.

### Buttons (`.alt-btn`)
Dark transparent fill, thin border (white or `#E23B3E`), label on one line, with **red corner
brackets** (four small L-shaped `<span>`s at the corners). On hover the brackets push slightly
OUTSIDE the frame and a red glow + red text-shadow appears. All CTAs link to real anchors:
"Explore The Drop" → `#alt-showcase`, "Join Waitlist" / "Join the Waitlist" → `#alt-waitlist`,
"Discover Collection" → `#alt-showcase`. Social links (Instagram/TikTok/Pinterest) open the
platforms in a new tab.

---

## Section-by-section

### 1. HERO (`#alt-pin` → pinned scrub, `#alt-hero`)
Full-viewport video of three girls (`altera/hero-video.mp4`). The hero is `position:sticky` and
pinned while a spacer (`#alt-pin-driver`, ~2 viewport-heights) provides scroll runway. On scroll the
hero **video scrubs forward** and the UI fades. Layout:
- **Nav** (see global chrome).
- **Left block (`#alt-left`)**: small pixel label + red square, headline
  "DRESS YOUR ALTER EGO" with **"EVERY VERSION OF YOU"** set in Pixelify Sans, a white paragraph,
  and two buttons (Explore The Drop / Join Waitlist).
- **Right block (`#alt-right`)**: DROP 001 card — "RED SIGNAL" typewriter effect (types, holds,
  erases, loops; blinking red cursor block) in Pixelify Sans, animated loader bars, and a
  signature-detail image card (`altera/detail-sunglasses-sm.jpg`).
- **Bottom**: social links + a scroll indicator with an animated dot.
- Free-flying pixel particles (`#alt-artifacts`).

### 2. TRANSITION: Hero → About (`#alt-transition`)
A `position:fixed`, full-viewport video (`altera/transition.mp4`, a camera move from the 3 girls
into a red-sunglasses closeup). It fades in mid-scroll and **scrubs with scroll progress**, then
dissolves as the About section rises. Wrapper background is a radial grey→dark gradient; the video
uses `mix-blend-mode:multiply` + `filter:brightness(1.18) contrast(1.1) saturate(.92)`; plus a
vignette overlay, scanline overlay (`opacity:.3`), and pixel-branding sparkles that stay ON so the
treatment never drops out. Position-driven (no extra scroll section) → **seamless, no gap.**

### 3. ABOUT / ESSENCE (`#alt-essence`)
Full-bleed looping video background (`altera/about-blink.mp4`, blinking closeup) with dark
legibility gradients. Content (`#essence-content`): "OUR ESSENCE" label, headline
"MADE TO EXPRESS. MADE TO TRANSFORM.", paragraphs, values list, and a "DISCOVER COLLECTION" button
→ `#alt-showcase`. **Marker (`#essence-marker`) is horizontal, pinned top-right**: `002 — ALTERA — 24SS`
(numbers/dividers red, laid out in a row). Free-flying particles. Color grade matched to the videos
(`brightness ~1.18 contrast 1.1 saturate .92`).

### 4. TRANSITION: About → Playground (`#alt-pgt`)
Same mechanism and treatment as Hero→About: a `position:fixed` full-viewport video
(`altera/pg-transition.mp4`) with the **same radial grey→dark gradient, multiply blend, matching
color filter, vignette, scanlines, and pixel sparkles.** Driven by the Playground's rise so it
fades in, scrubs, and dissolves into the Playground with **no scroll gap** (feels like one video).

### 5. PLAYGROUND — "Build Your Alter Ego" (`#alt-playground`)
Light studio fitting-room on a **7-column grid** (margin 60px, gutter ~24–30px, `align-items:stretch`
so the three columns bottom-align). This is the interactive styling studio.
- **Columns 1–2 — model frame (`#pg-model`)**: framed card, far left. Shows a **360° turntable
  video** (`altera/pg-model.mp4`) **only for the default look** (Lace Cami + Star/Red Denim + Red
  Chrome). Selecting any other combination hides the video and shows that combination's **still
  render** (`altera/pg/model_<top>_<bottom>_<glasses>.png`). Drag horizontally (or sideways
  scroll/swipe) over the frame to **spin the turntable** — a full frame-width drag ≈ one full turn,
  smoothed with inertia, wraps seamlessly. A "DRAG TO ROTATE" indicator sits centered below the
  frame (no arrow buttons). The `003 — PLAYGROUND — 24SS` marker sits on the image's left edge
  (light text). Very subtle floor grid. No cursor parallax on the video.
- **Columns 3–4 — center (`#pg-center`)**: small red "STYLE THE DROP" label, big headline
  "BUILD YOUR ALTER EGO.", a paragraph, a "DROP 001 | RED SIGNAL" line, a **SELECTED LOOK** card
  (updates live with the current TOP / BOTTOM / SUNGLASSES selection), and a single
  "JOIN THE WAITLIST" button → `#alt-waitlist`. No Save/Discover/extra CTA.
- **Columns 5–7 — styling options (`#pg-sections`)**: three categories only — **TOPS, BOTTOMS,
  SUNGLASSES** (no accessories). Each is a row of option cards: light box, thin grey border, small
  product placeholder + name; the active card has a red border and red corner brackets. Defaults:
  Lace Cami / Red (Star) Denim / Red Chrome. Clicking an option updates the active state, the
  SELECTED LOOK card text, and swaps the model render/video.
- Subtle pixel artifacts mostly around the edges.

**Selection data structure** (recreate this):
```js
const groups = [
  { key:'tops',      label:'TOPS',       options:['Lace Cami','Corset Top','Bell Top'] },
  { key:'bottoms',   label:'BOTTOMS',    options:['Red Denim','Flare Pant','Ruffle Skirt'] },
  { key:'sunglasses',label:'SUNGLASSES', options:['Red Chrome','Star Lens','Spike Frame'] },
];
// selection = [topIdx, bottomIdx, glassesIdx], default [0,0,0]
// model render path: `altera/pg/model_${top}_${bottom}_${glasses}.png`
//   token maps → tops: cami|corset|bell · bottoms: redjean|flare|skirt · glasses: chrome|star|spike
// video shows only when selection === [0,0,0]; else show the still render.
```

### 6. SHOWCASE — waterfall ⇄ 3D image globe ⇄ CTA hand-off (`#alt-showcase`)
This is the centerpiece: **one tall pinned stage (~440vh driver, sticky 100vh) runs the entire
storyboard on a single scroll progress `p` (0→1), using ONE shared set of 30 cards** animated
through every phase (never duplicated or swapped). All cards are **projected in JS** (manual
perspective projection with a per-card tangent frame) — NOT CSS `preserve-3d` billboards — so the
net↔sphere morph is one continuous space and cards sit flat ON the sphere surface (turning edge-on
at the silhouette). Background cross-fades light→dark for the sphere and hands off dark→dark into
the Waitlist (no black gap).

**The 30 cards**: grey placeholder fill + real ALTERA product photos (garments & sunglasses:
`top_*`, `bottom_*`, `glasses_*` from `altera/pg/`). Every 3rd card is **1:1 square**; the rest keep
a taller lookbook ratio. Card #0 = **LOOK 01**, the "character" card, carries the live Playground
selection and is the one that lands in the CTA. Some cards have small labels (LOOK 01, DETAIL 01,
ARCHIVE, LOOK 02, DETAIL 02).

**Timeline (scroll progress `p`)**:
- `.00–.14` **Waterfall in** — cards stream down 4 even vertical lanes (X at 15% / 38% / 62% / 85%
  of width) on a fixed pitch, fading up from transparency **the instant the Showcase enters** (right
  after the Playground). Focal scaling: cards grow through the center band, shrink at top/bottom;
  soft edge-fade top & bottom. A fixed **ghost** of the Playground character frame descends from its
  on-screen spot into LOOK 01's lane (transform+opacity only) so the character visibly "joins the
  archive" with no gap.
- `.14–.30` **Form sphere** — the same cards collect and fold onto an evenly-distributed
  (Fibonacci) globe; bg cross-fades to dark; SHOWCASE copy + header + red corner ticks fade in;
  front cards bright, back cards dim + blurred.
- `.30–.44` **Sphere expands** — globe radius grows ~60–77% for one scroll beat.
- `.44–.58` **Sphere contracts** back to normal radius.
- `.14–.78` **Rotation** — the globe rotates smoothly on scroll (to ~240° Y, up to −14° X tilt),
  smoothed. **Also drag-to-rotate**: grab the stage (grab/grabbing cursor) and spin it on both axes;
  the drag offset adds to the scroll rotation and carries **inertia** after release. Disabled on
  mobile.
- `.58–.78` **Waterfall out** — the same cards unfold back into the same lanes and stream down.
- `.80–1.0` **CTA landing** — LOOK 01 flies large to the Waitlist's left image slot while the other
  29 cards fall away and fade. A second fixed **ghost** continues from the landed card into the
  Waitlist image as that section rises, landing pixel-aligned on it (transform+opacity only) — the
  final CTA image visibly *comes from* the card.
- Left "SHOWCASE" copy block (`005`, headline "Showcase" with "case" in Pixelify) fades in only while
  the sphere is active. Free-flying particles (`#show-artifacts`) bounded to the sticky viewport.
- Waterfall framing gradients: light gradient top during the intro, dark gradient bottom throughout,
  to blend into the Playground above and the Waitlist below.

**Sphere math reference** (from the prototype logic class — reimplement equivalently):
```
// even distribution: Fibonacci sphere → unit vector u per card
// tangent frame per card: right = normalize(cross((0,1,0), u)); down = -cross(u, right)  // negate so images sit upright
// each frame: rotate u, right, down by (scrollRotY+dragRotY) about Y then (tilt+dragRotX) about X
// project center C = u*Reff with perspective focal = max(W,H)*0.82, R = min(W,H)*0.46
// build a 2x2 matrix from projected right/down deltas → CSS transform: translate(cx,cy) matrix(a,b,c,d,0,0)
// depth = (rotated u.z + 1)/2 → opacity lerp(0.22,1,depth), blur (1-depth)*2.2px, z-index by C.z
// net(waterfall) state is a separate target; blend net→sphere by sphereMix (0..1)
```

### 7. WAITLIST (`#alt-waitlist`)
**Dark** section (radial `#1A1A1E → #101012 → #060608`). 7-col grid, vertically centered.
- **Left (cols 1–3, `#wl-image`)**: the "FINAL CTA IMAGE / SELECTED LOOK" card — dark frame with red
  corner brackets, shows the current selected-look render. This is where the Showcase card lands.
- **Right (cols 4–7, `#wl-copy`)**: `006 — EARLY ACCESS` marker, headline "JOIN THE FIRST" +
  "ALTERA DROP." (Pixelify), paragraph, three info lines (`DROP 001 / RED SIGNAL`, `LIMITED RELEASE +`,
  `COMING SOON +`) with dark dividers, an email input + "JOIN THE WAITLIST" button, and bottom
  microcopy `ALTERA / DRESS YOUR ALTER EGO / 24SS`.
- Free-flying particles behind the content (`#wl-artifacts`, `z-index:0`; grid is `z-index:2`).

### 8. FOOTER (`#alt-footer`)
Dark, sharing the Waitlist palette (top edge `#060608` so there's no seam). Top row: nav column
(ABOUT/PLAYGROUND/COLLECTION/WAITLIST) + right-aligned meta (`DROP 001 / RED SIGNAL`, `24SS
COLLECTION`, `@ALTERA.WORLD`). Then a **big ALTERA wordmark** (`altera/logo.svg`) with a
**cursor-reactive lava-lamp glow**: three blurred red radial "blobs" drift autonomously and swell +
drift toward the cursor, plus an animated SVG **noise/grain overlay** (`mix-blend-mode:overlay`) and
an occasional Y2K **blink/flicker**. Bottom row: `© 2026 ALTERA`, `DRESS YOUR ALTER EGO_`,
`MADE TO EXPRESS. MADE TO TRANSFORM.`.

---

## Interactions & behavior summary
- **Scroll-scrubbed videos** (hero, both transitions): pause the video and set `currentTime` from
  scroll progress, smoothed (`cur += (target-cur)*~0.22`). Videos are **blob-loaded**
  (`fetch(src)→blob→createObjectURL`) because progressive MP4s don't reliably seek. Required for the
  scrub to work.
- **Playground turntable**: horizontal drag / sideways wheel scrubs `pg-model.mp4`; inertia; wraps.
- **Showcase**: scroll drives the whole net→sphere→net→CTA timeline; **drag rotates** the sphere
  (both axes) with inertia on top of scroll rotation.
- **Two continuity ghosts** (fixed, transform+opacity only): Playground character → first waterfall
  card; landed LOOK 01 card → Waitlist image. Both position-driven so they start the instant you
  cross the boundary — no dead scroll.
- **Nav** hides after the hero and does not return on scroll-up. **Dock** appears after the hero and
  persists (top z-layer). **Loader** only during the waterfall.
- **Sound**: click-to-play only, `altera/ambient.mp3` (user will supply the track).
- All parallax / 3D / scrub is **disabled on mobile**; sections stack, Showcase becomes a
  dark section with a horizontal card carousel, ghosts hidden.

## State
- `pgSel: [topIdx, bottomIdx, glassesIdx]` (default `[0,0,0]`) — drives model render/video +
  SELECTED LOOK card + Waitlist image + Showcase LOOK 01 card.
- `pgLoad` — brief loading veil while a new render preloads.
- Scroll progress per pinned section — drives all choreography (derive from ScrollTrigger or a rAF
  reading `getBoundingClientRect().top`).
- Transient animation state (rotation, drag offsets, particle velocities, typewriter index, glow) —
  kept in refs / a single rAF loop, not React state, to avoid re-renders.

## Design tokens (quick reference)
- Colors: see the table above. Accent `#E23B3E`; dark stack `#1A1A1E/#101012/#060608`; light studio
  `#F3F3F5/#E7E7EA/#DEDEDF`; text `#FFFCFC/#F2F2F4/#B6B6BA/#161618`.
- Spacing: section padding ~60px desktop / 18px mobile; grid gutter 20–30px; margin 60px.
- Type: Space Mono (UI/body) + Pixelify Sans (accents). Labels ~9–13px, letter-spacing .16–.28em,
  uppercase. Hero/section headlines `clamp(28px,~4vw,64px)`, weight 700, line-height ~1.0.
- Borders: 1px; corner brackets ~9–16px L-shapes in `#E23B3E`.
- Radius: mostly 0–2px (sharp/editorial); dock/nav 2px.
- Shadows: card `0 6–8px 20–26px rgba(0,0,0,.16–.4)`; red glow `0 0 8–70px rgba(226,59,62,.26–.7)`.
- Motion: video scrub smoothing ~0.22; rotation smoothing ~0.09; particle scare R=150, accel=1.4,
  Vmax=4.5; glow blink cadence ~1.8–5s.

## Assets (in `altera/`)
- `hero-video.mp4` — hero (3 girls).
- `transition.mp4` — Hero→About camera move (3 girls → sunglasses closeup).
- `pg-transition.mp4` — About→Playground transition.
- `about-blink.mp4` — About background (blinking closeup). `about-loop.mp4` alt.
- `pg-model.mp4` — Playground 360° turntable (default look).
- `pg/model_<top>_<bottom>_<glasses>.png` — 27 pre-rendered look combinations
  (top: cami|corset|bell · bottom: redjean|flare|skirt · glasses: chrome|star|spike).
- `pg/top_*.png`, `pg/bottom_*.png`, `pg/glasses_*.png` — product photos used as Showcase cards.
- `closeup-girl-sm.jpg`, `detail-sunglasses-sm.jpg` — small stills (posters / detail card).
- `closeup-girl.png`, `detail-sunglasses.png`, `hero-girls.png` — full-res stills.
- `logo.svg` — ALTERA pixel wordmark.
- `ambient.mp3` — **not included / to be supplied by the client**; the sound toggle is wired for it.

All imagery is real client fashion photography — keep the shared desaturated Y2K color grade
(`brightness(1.18) contrast(1.1) saturate(0.92)` on videos) consistent if assets are swapped.

## Files in this bundle
- `ALTERA Hero.dc.html` — the full prototype (markup + inline styles + the logic class with ALL the
  animation math). **Primary reference.**
- `support.js` — the prototype runtime (do not port to production; included so the file opens).
- `altera/` — all media + logo assets.

## Known constraints (from the prototype environment)
- Single-file offline export isn't possible (videos total ~23 MB / ~31 MB base64, over the inline
  ceiling). In production, serve videos normally and lazy-load; consider compressed/hevc + webm.
- Screenshot tooling renders `position:fixed` videos + the sticky rAF stage as blank; verify motion
  in a real browser.

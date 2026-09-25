# 進撃の巨人 — ATTACK ON TITAN

A scroll-driven tribute site. No build step, no dependencies — plain HTML/CSS/JS.

---

## Live Demo

Open `index.html` directly in a browser, or serve locally for proper image decoding and caching:

```bash
# Any static server works
npx serve .
# or
python -m http.server 5173
```

---

## Overview

Three cinematic acts driven by scroll progress, each a distinct technical showcase:

| Act | Section | Frames | Core Effect |
|-----|---------|--------|-------------|
| **I** | `#wall` | 71 | Colossal Titan rising behind Wall Maria. Screen shake, chromatic ghosting, steam particles, and a shockwave at the breach point. |
| **II** | `#eren` | 32 | Attack Titan standing up out of Eren. Procedural lightning bolts, flash frames, and decrypted text tied to scroll progress. |
| **III** | `#reveal` | — | Two stacked plates (`top.jpg` + `bottom.jpg`) with a trailing, feathered spotlight that carves one out of the other on cursor/touch movement. |

**Between acts:**
- Scroll-velocity marquee (marquee speed/direction follows scroll velocity)
- Four-layer parallax wall field with depth-based brick/mortar patterns
- CountUp stat ledger (intersection-observer triggered)
- WebGL aurora finale with ember sparks and color ramp

---

## Assets

```
assets/
├── frames/
│   ├── wall/         71 × 1280×720   (desktop)
│   ├── wall-sm/      71 ×  720×405   (< 820px viewports)
│   ├── eren/         32 × 1280×720
│   └── eren-sm/      32 ×  720×405
└── reveal/
    ├── top.jpg       1600×900  (the image revealed by the spotlight)
    └── bottom.jpg    1600×900  (always-visible base plate — you supply)
```

**Regenerate frames** from source PNG zips with `../tools/convert.ps1` if you re-render.

> **`bottom.jpg`** — Until it exists, the site derives a desaturated, blood-tinted stand-in from `top.jpg` so the act still reads. Best results when `bottom.jpg` matches `top.jpg`'s framing (1600×900, same crop/focal point) — the reveal is most convincing when the two plates are pixel-aligned.

---

## ReactBits Components (Ported to Vanilla JS)

Each component in `main.js` is marked with a banner comment:

| Component | Description |
|-----------|-------------|
| **Aurora** | Raw WebGL fragment shader — rising curtains, fbm noise, color ramp, ember sparks |
| **ScrollVelocity** | Marquee whose speed & direction follow scroll velocity |
| **DecryptedText** | Character-by-character "decryption" with katakana glyphs on scroll reveal |
| **SplitText** | Word-split stagger animation on intersection |
| **CountUp** | Eased number count-up on intersection |
| **ClickSpark** | Radial burst on pointerdown |
| **Magnet** | Magnetic pull toward pointer within a field radius |
| **TiltedCard** | 3D tilt following pointer (perspective transform) |
| **SpotlightCard** | Radial gradient spotlight tracking pointer |

---

## Architecture Highlights

- **Single rAF ticker** — all subsystems (scrubbers, parallax, particles, WebGL) subscribe to one shared loop
- **Scroll-scrubbed frame sequences** — `Scrub` class measures progress within a sticky section, draws current frame to canvas, handles shake/ghosting/parallax offsets
- **Progressive loading** — Preloader shows task queue; releases once opening frames (first 12/24) are ready; rest stream in background
- **Responsive frame sets** — Automatically loads `-sm` (720×405) frames on viewports `< 820px`
- **`prefers-reduced-motion`** — Disables scrubbing, shake, cursor, lightning, dust, cursor-trail; renders each sequence's final frame statically
- **No dependencies** — Zero npm packages, no build tooling

---

## Tuning Knobs

| What | Where |
|------|-------|
| Scroll length per act | `data-vh` attribute on `<section class="scrub">` |
| Phase caption cut points | `phases` array passed to `new Scrub(...)` in `main.js` |
| Shake onset | `shakeFrom` option (0–1 progress) in `Scrub` options |
| Spotlight radius / trail length | `radius` / `TRAIL` constants in Act III reveal block |
| Aurora amplitude / colors | `uAmp`, `uA`/`uB`/`uC` uniforms in WebGL fragment shader |
| Steam particle count / lifetime | `want` calculation + `decay` in steam module |
| Lightning strike rate / spread | `rate` lerp + `makePath` spread in bolts module |

---

## Browser Support

- Modern browsers with `IntersectionObserver`, `requestAnimationFrame`, `WebGL`
- `prefers-reduced-motion` and `pointer: coarse` media queries respected
- Tested in Chrome, Firefox, Safari, Edge (desktop & mobile)

---

## Credits

- **Source material**: *Attack on Titan* / *進撃の巨人* by Hajime Isayama (講談社 / MAPPA / WIT Studio)
- **Component inspirations**: [reactbits.dev](https://reactbits.dev) — components reimplemented in dependency-free vanilla JS
- **Fonts**: Shippori Mincho B1, Zen Kaku Gothic New, Oswald, Cormorant Garamond, Space Mono (via Google Fonts)
- **No external libraries, frameworks, or build tools used**

---

## License

This is a non-commercial fan tribute. All *Attack on Titan* IP belongs to its respective owners. Code is provided as-is for educational/reference purposes.
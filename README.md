# inforu — scroll-world landing page

An immersive, scroll-scrubbed "fly through the world" landing page for **inforu**
(inforu.co.il), built with the [`scroll-world`](https://github.com/oso95/scroll-world)
technique: as the visitor scrolls, a camera flies into each scene of inforu's
customer-communication world, one scene handing off to the next.

Hebrew-native, RTL, desktop-first (degrades gracefully on mobile).

## Run it

```bash
python -m http.server 8099 --directory .
# open http://localhost:8099/index.html
```

(A repo-level `.claude/launch.json` config named `inforu-scroll-world` also serves it.)

## The journey (6 scenes)

| # | id            | scene                          | beat |
|---|---------------|--------------------------------|------|
| 1 | city          | Israeli city of 30,000 businesses, message-threads | הפלטפורמה הנבחרת בישראל |
| 2 | channels      | control hub → SMS / WhatsApp / Email / Push / Voice | כל הערוצים במקום אחד |
| 3 | conversations | WhatsApp service room / call-center | שיחה אמיתית בערוץ שהלקוח אוהב |
| 4 | automation    | if/then journey machine, OTP, eCommerce | מסעות לקוח אוטומטיים |
| 5 | insights      | analytics observatory, dashboards | נתונים ותובנות בזמן אמת |
| 6 | finale        | the platform core + CTA | פלטפורמה אחת לכל קשר עם הלקוח |

## Files

```
index.html            – the page: engine config, Hebrew copy, inforu palette, RTL mirroring
scrub-engine.js       – the portable scroll-scrub engine (from the scroll-world skill, unmodified)
assets/scene-0N.webp  – lightweight scene posters / lazy-load fallbacks (~30–120 KB)
assets/scene-0N.png   – 4K master stills (Gemini 3 Pro / nano-banana-pro), source of truth
assets/vid/scene-0N.mp4 – the camera-flight "dive" clips (1080p, crf22, -g8, faststart, muted)
_work/                – prompts, prescaled sources, logs (not needed to serve)
```

## Art direction

Soft matte low-poly **clay diorama**, isometric, tilt-shift miniature, warm light.
Palette (also the CSS theme in `index.html`):
`#EDF1FB` cloud bg · `#0E1430` navy ink · `#2E6BFF` inforu blue · `#12B5C9` teal ·
`#17B26A` green · `#6C5CE7` violet · `#0EA5C4` cyan · `#FF6A5F` coral.
Per-section `accent` colours the eyebrow, tags, nav pill and route dot.

## How the camera clips were made — and the upgrade path

The scroll-world skill renders the dives with an **AI video model** (Higgsfield /
Seedance). Here the fal.ai account balance was exhausted at build time, so the dives
were produced with **ffmpeg** instead — a real slow push-in toward each scene's focal
point, encoded to the exact scrub spec. The scenes themselves are AI-generated
(Gemini 3 Pro Image). Result: a genuine scroll-driven camera flight, at zero video cost.

**To upgrade to true AI 3D fly-throughs** (the full skill output), once fal.ai (or
Higgsfield) has credits:

1. Generate one `image-to-video` "dive" per scene from `assets/scene-0N.png`
   (fal Seedance runner: `~/.claude/skills/fal-video/generate.mjs`; prompts in
   `_work/prompts/DIVE-0N.txt`).
2. (Optional, architecture B) generate seamless fly-over **connectors** between scenes
   using the neighbouring dives' actual first/last frames, and list them in the
   `connectors: []` array in `index.html`.
3. Re-encode with the scrub settings (`-crf 20 -g 8 -movflags +faststart -an`) and
   drop them in `assets/vid/`. **No other change** — the engine config is identical.

## Notes

- The engine loads each clip as a **Blob** (always seekable), so any static host works —
  no HTTP byte-range support required.
- RTL is handled by `dir="rtl"` + a small unlayered override block in `index.html`
  (copy pinned right, scrim + route rail mirrored, numeric index kept LTR).

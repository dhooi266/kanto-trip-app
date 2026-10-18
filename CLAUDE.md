# Kanto trip app (慢游旧时光 · Old Japan, Slowly)

A single-file, mobile-first travel itinerary app for an 8-day trip, 23–30 Jan 2027:
Haneda → Asakusa/Yanaka → Kamakura → Shuzenji (Izu) → Atami → Ginza → Haneda.
Two travellers, Vietnam Airlines business class (RM10,000 for both).

Originally built on claude.ai as an Artifact, now maintained here.

## Files
- `index.html` — the whole app: HTML, CSS and JS in one file, no build step, no dependencies.
  The only external resource is Google Fonts (Noto Sans SC, Noto Serif SC, JetBrains Mono).
- `README.md` — how to run and host it.
- `img/` — header collage, `day1..8.webp` day-card photos, `stay1..7.webp` per-night stay photos.

## How to run
Open `index.html` in a browser, or serve the folder:
`npx serve .`  (or `python -m http.server 8000`)

## Code map (all inside the one `<script>` in index.html)
Every user-facing string is bilingual: `_(chinese, english)` makes `{z,e}`, and `t(x)` picks
the current language. Always add BOTH languages when you add or change text.

| Constant | What it holds |
|---|---|
| `UI` | Interface labels |
| `THEME` | Per-day colour (`c`, `c2`) and emoji, index 0–7 |
| `DAYS` | The itinerary. Each day: date, weekday, title, intro, km/drive/stay pills, and `stops[]`. A stop has `t` (time), `k` (`spot` / `drive` / `stay`), `n` (name), `jp` (Japanese name), `p` (description), optional `q` (Google Maps query), `tel`, `tips[]`, `book` (booking warning) |
| `EXTRA` | Per day: `eat[]` (meal, name, description, price text), `stay` (area, type, price, notes) or `null`, and `cost[]` = `[[label, yen]]` per person |
| `CAR` | Whole-car costs in yen (rental, tolls, fuel, parking), split between travellers |
| `FLIGHT_RM` | Flight cost per person in RM (fixed, not converted) |
| `PREP` | Checklist groups and items (`[id, label, sub]`); ids are localStorage keys, keep them stable |
| `SOS`, `DRIVE_TIPS`, `TIPS` | Info tab content |
| `PL` | Named places used by the route legs (`n` label, `q` Google Maps query) |
| `MODE` | Leg modes: `d` drive, `t` train, `w` walk |
| `LEGS` | Per day: `legs[]` = `[mode, fromPlace, toPlace]`, optional `full[]` whole-day driving chain |
| `PHOTO` | Stop photos: key `"day-stopIndex"` → `[imageUrl, creditPageUrl]` (Wikimedia Commons, credited on the photo). Stop indexes count every entry in `stops[]`, so update keys if you insert or remove a stop |

Rendering functions: `renderHero`, `renderStrip`, `renderDay` (timeline, map, legs, food, stay,
costs), `renderPrep`, `renderInfo`, `dayMapInner` / `embedUrl` / `wireMap` (keyless Google Maps embed: whole-day route by default, one leg when tapped),
`yen()` and `fx()` (¥ / RM / both currency display), `renderAll()`.

Day count is 8 in several places (`cur > 7`, `i > 7`, countdown end date 2027-01-31, `/8`
in the hero). Update all of them if days are added or removed.

## State (browser localStorage, prefix `kanto27:`)
`lang` (zh/en), `money` (jpy/myr/both), `rate` (MYR per JPY, default 0.0259 = 16 Oct 2026
mid-rate), `day` (selected day), `done-<day>` (visited stops), `prep` (checklist ticks).
All reads/writes are wrapped in try/catch; the app must work with storage unavailable.

## Conventions
- Mobile-first, ~380px wide; no horizontal scroll. Light and dark themes via CSS tokens on `:root`
  plus `@media (prefers-color-scheme: dark)` and `[data-theme="dark"]` blocks; never use a colour
  that only exists in one theme.
- Keep it a single static file unless there is a strong reason to split.
- Prices: `EXTRA[].cost` numbers are yen per person; free-text price strings like "约1,500日元"
  or "~¥1,500–2,000" are auto-converted to RM by `fx()`, so keep that format.
- Phone numbers in stops are used for Japanese car-navigation search; only add ones you have verified.

## Header, map and photos
- Stay card: per-night photo `img/stay{N}.webp` (gpt-image-2.5), night badge, price, night-of-stay dots and a Google Maps lodging search. `STAYN` holds [night, nights in stay, search query] per day.
- Voice translator (`#voice`, `paintVoice`): two-way turns. Record with MediaRecorder → ElevenLabs `scribe_v2` speech-to-text → MyMemory → ElevenLabs TTS (`eleven_v4_turbo`, fallback `eleven_flash_v2_5`) played through an AudioContext unlocked on tap. The ElevenLabs key is typed in by the user and stored only in localStorage `kanto27:elkey`; NEVER put a key in index.html (public repo). Voice choice per language in `kanto27:elv`. No key = browser SpeechRecognition + speechSynthesis.
- Translate tab (`renderTalk`): MyMemory free API (no key, CORS ok) for zh/en ↔ ja, history in `trhist`, browser speech for mic and playback, `PHR` offline phrasebook, `#showja` full-screen show-staff overlay.
- Day card (`.dayhero`): full-bleed photo `img/day{N}.webp` (gpt-image-2.5-sunburst, 1024², photoreal per-day scene), Ken Burns drift, story-style progress bars (tap to jump), swipe left/right to change day (`wireDayHero`), neighbouring days preloaded. Falls back to the day colour gradient if an image is missing.
- Header backdrop: `img/hero-portrait.webp` (phones) and `img/hero-wide.webp` (≥700px), generated with OpenAI `gpt-image-2.5-sunburst` (18 Oct 2026). Navy scrim in `.hero-bg::after` keeps the title readable; motion = slow Ken Burns drift (`kb`), fade-in, title rise, and the snow canvas. All disabled under prefers-reduced-motion.
- The map is a Google Maps iframe using `maps.google.com/maps?...&output=embed` (no API key). Leg modes map to `dirflg` d/r/w; transit routes take only two points.
- Photos load from upload/thumb.wikimedia.org; a failed image removes itself.

## Hosting
GitHub Pages: repo dhooi266/kanto-trip-app (public, branch main) → https://dhooi266.github.io/kanto-trip-app/. Push to main to update. The repo is public: no booking numbers or passport details.
Local preview: `node .serve.js` (port 3030, git-ignored), or the `kanto-trip-app` entry in the parent .claude/launch.json.

## Ideas
- Syncing checklist/visited state across devices (needs a small backend or a service like Supabase).

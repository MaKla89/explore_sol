# Solar System Explorer

> ⚠️ **This project was vibe-coded with AI.** It was built entirely (or almost
> entirely) with AI assistance, by prompting and steering rather than careful
> hand-crafting. Use it **as-is, with no guarantees of any kind** — no warranty
> that it works, is bug-free, or is suitable for anything in particular
> (see [LICENSE](LICENSE)). It's meant for fun and learning; expect the odd
> rough edge. PRs to un-vibe it are welcome. 🙂

A 3D, browser-based solar system for kids: fly through space with WASD + mouse,
tap or click any planet to travel to it, and watch a real solar eclipse play out
over Earth. Works on desktop **and on phones/tablets** — the layout adapts to
small screens and all controls have touch equivalents (see below).

By default everything is generated **procedurally in the browser** — no image files,
no server. The optional **📷 Photos** mode swaps in real planet photographs, and it
is **fully offline**: the 3D engine (`vendor/`) and all photos (`textures/`) are
bundled right here, so a served copy needs no internet at all. The three tiers —
🌱 procedural drawings, ⚖️ photos in GPU-saver mode (≤ 2048 px) and 💎 full-detail
photos (≤ 4096 px) — differ only in memory use, not download size. If an image can't
be loaded, that body simply keeps its procedural texture, so you never see broken images.

> **🚀 Just want to play around?** Try the live demo — no download needed:
> <https://solar-explorer-demo.netlify.app/>

![Solar System Explorer in 📷 photo mode — all eight planets on their real elliptical orbits, the asteroid belt and Saturn's rings](demo.png)

![Compare mode (📏) — all eight planets lined up side by side at their true relative sizes, with the Sun above as light source](compare.png)

## Run it

Serve this folder with any static web server — **no internet needed**:

```bash
cd explore_sol
python3 -m http.server 8000
# then open http://localhost:8000
```

No build step, no dependencies to install.

> **Why a server?** The engine is loaded as a local ES module, and browsers refuse
> to load modules from `file://` — double-clicking `index.html` won't work. Any
> static server does (`python3 -m http.server`, `npx serve`, or plain web hosting).
>
> **Just want the single file?** Download only `index.html` and open it served over
> https (or the live demo below): anything missing locally — the engine, a photo —
> automatically falls back to its original CDN / texture URL. So one HTML file still
> works, it just needs internet for those parts.
>
> **Integrity:** whether the 3D engine comes from `vendor/` or the CDN, its SHA-384
> checksum is verified *before* it runs, so a corrupted or tampered copy is never
> executed. A corrupt local file is reported instead of silently replaced by the CDN.

### What's bundled here

| Folder | Contents |
|---|---|
| `vendor/` | `three.module.js` (≈ 1.3 MB) — byte-identical to the official three@0.160.0 release, MIT license |
| `textures/` | All 12 planet / Moon / cloud / Milky-Way photos at ≤ 4096 px, jpg q90 (≈ 16 MB) — CC BY 4.0, [Solar-Wanderer](https://github.com/hyqzz/Solar-Wanderer) / [solarsystemscope.com](https://solarsystemscope.com/textures/) |

## Controls

| Key / Action | What it does |
|---|---|
| Mouse drag | look around |
| Touch & drag (one finger) | look around |
| Pinch (two fingers) | zoom in / out |
| Drag with two fingers | fly in the direction you drag (map-style pan) |
| Tap a planet / the Sun / the Moon | fly to it and follow it |
| `W` `A` `S` `D` | fly forward / left / back / right |
| `Space` | fly up |
| `Ctrl` | fly down |
| `Shift` (hold) | fly faster |
| Mouse wheel | zoom in / out |
| Click a planet / the Sun / the Moon | fly to it and follow it |
| `Esc` | stop following / close window |
| `F3` | show/hide the performance stats overlay — frame rate, JS time, real render size & pixel-ratio, draw calls/triangles and live shader/geometry/texture counts. Handy for diagnosing sluggishness or high GPU load (on some laptops press `Fn` + `F3`) |
| Bottom bar | pause, or set the time speed on a continuous slider (0.001 … 100 days per second; releasing near the marker snaps back to the default 0.02 d/s) |
| 🌑 Eclipse button | start a solar eclipse over Earth |
| `?` | help / controls |
| EN / DE button | switch language (remembered) |
| 📷 Photos button | pick the image source: 🌱 built-in drawings / ⚖️ real photos, GPU-saver (≤ 2048 px) / 💎 real photos, full detail (≤ 4096 px) — all bundled, works offline (remembered) |
| 🔬 Real size button | show every planet at its true size relative to the Sun — they get tiny! (remembered) |
| 📏 Compare button | line up all 8 planets side by side at their true relative sizes, with the Sun above as light source (remembered) |
| 🗺 Places button | show/hide the list of all places you can fly to (remembered) |
| Labels button | show/hide name labels |

## Features

- **Sun + all 8 planets** with realistic relative sizes, orbital periods and tilts
  (Uranus rolls on its side, Venus spins backwards), plus Earth's Moon.
- **Three image-quality tiers** (📷 menu) — all photos are bundled locally, so the
  tiers differ in GPU memory use, not download size:
  - *🌱 Low — procedural (default)*: real-looking continents, oceans, ice caps and
    drifting clouds on Earth; Jupiter's bands and Great Red Spot; Saturn's rings with
    the Cassini division; craters on the Moon; a churning solar surface. All computed
    in the browser.
  - *⚖️ Medium — real photos, GPU-saver*: the bundled photographs downscaled to
    ≤ 2048 px in JS (sky keeps ≤ 4096) — ≈ 10× less GPU memory than full detail,
    for weaker devices.
  - *💎 High — real photos, full detail*: the bundled photographs as they are
    (≤ 4096 px) — what the old online "full size" tier actually displayed after its
    own downscale.
  Your choice is remembered; any photo that fails to load falls back to its
  procedural texture automatically. All photos come from
  [Solar-Wanderer](https://github.com/hyqzz/Solar-Wanderer)
  (solarsystemscope.com, CC-BY-4.0 — NASA/USGS/SDO data), including a real NASA
  cloud photo for Earth's drifting cloud layer. In-app credits are shown in the help window (?).
- **🌌 Milky Way backdrop**: in the photo tiers, the procedural starfield is replaced by a
  real equirectangular Milky Way panorama on a camera-following sphere, oriented with
  correct galactic coordinates (galactic centre and north pole where they really are).
  Falls back to the procedural stars when unavailable or in 🌱 low mode.
    help window (?).
- **Correct lighting & shadows**: one light at the Sun — every planet shows a crisp
  day/night terminator. The Moon's and the rings' shadows are computed *analytically*
  in the shaders (each surface point ray-marches toward the Sun and tests it against
  the Moon / the ring plane), so they are resolution-independent: the Cassini gap
  casts a razor-sharp line, eclipses get physically correct umbra + penumbra, and
  Saturn's rings cast *and* receive shadows (the planet darkens the far side of the
  rings). No shadow maps at all — cheaper and always crisp.
- **Asteroid belt** between Mars and Jupiter (4500 instanced rocks in two layers: 3000 main
  rocks + 1500 tiny dust fragments).
- **Time controls**: pause, or run at any speed from 0.001 to 100 days per second on a
  logarithmic slider (releasing near the marker snaps back to the default 0.02 d/s —
  the slow speeds are great for watching the Moon's shadow cross Earth).
- **🔬 Real size mode**: every planet at its true radius relative to the Sun —
  Mercury becomes a speck, Jupiter still dwarfs everything else.
- **📏 Compare mode**: all 8 planets lined up side by side (largest → smallest) at
  their true *relative* sizes, with the Sun above the row at its true scale too — so
  vast that only its lower limb fits on screen. It acts as the light source,
  so each planet keeps a proper day/night terminator. Orbits and the asteroid belt
  are hidden; the name labels form one neat line above the row. Great for seeing how
  big the planets really are compared to each other.
- **🗺 Places list**: a hideable panel listing every place you can visit — click an entry
  to fly there, no hunting for dark dots. The place you're following is highlighted.
- **📊 Performance overlay (`F3`)**: a tiny corner readout of frame rate, per-frame JS time, the real
  render resolution & pixel-ratio, draw calls/triangles and live shader/geometry/texture counts —
  everything needed to tell whether a stutter is GPU fill-rate, CPU, or something else. Costs nothing
  when hidden (no blur, updates only while shown).
- **Bilingual UI** (English / German), language choice is remembered.
- **Phone & tablet support**: the interface adapts to small screens — compact
  emoji buttons, the info card and the places list become bottom sheets, and all
  controls work with touch (one finger = look around, tap = fly there, pinch =
  zoom, two-finger drag = fly). The help window (?) automatically shows the
  touch instructions on touch devices.

## The eclipse mode 🌑

Press the **🌑 Eclipse** button and the camera flies to a viewing spot above Earth:

1. The Moon is placed exactly between Sun and Earth (a "new moon at a node" —
   in reality the Moon's orbit is tilted ~5°, so this only happens near the
   points where its orbit crosses the Sun–Earth plane).
2. Time slows to 0.1 days/second so you can watch the shadow travel across Earth.
3. Two cones are drawn from the Moon:
   - **Umbra** (dark) — the small spot of *totality*, where the Sun is fully covered.
     People standing in this spot see a total solar eclipse: day turns to night for
     a few minutes.
   - **Penumbra** (blue, wider) — the partial-shadow zone around it, where only part
     of the Sun is covered.
4. Because the Moon and Earth keep moving, the umbra spot wanders across the planet
   from west to east — just like a real eclipse path.

Press **🌑 Eclipse** again (or `Esc`, or any speed button) to return to normal time
and the Moon's usual tilted orbit.

## Notes

- Sizes and distances are compressed for playability (a true-scale solar system
  would be mostly empty black), but relative ordering, periods and tilts are real.
- **Orbits are real ellipses**: each planet follows its actual Keplerian orbit —
  eccentricity, inclination to the ecliptic, ascending node and perihelion
  direction are the NASA "Planetary Fact Sheet" mean elements for J2000. So the
  orbits are visibly non-circular (Mercury's most) and each lies in its own
  slightly tilted plane instead of one flat disc. Kepler's equation is solved
  every frame, so planets also speed up near perihelion and slow down at
  aphelion, as they really do. At t=0 the planets sit where they actually were
  on Jan 1, 2000.
- The whole app is a single self-contained `index.html` (~2500 lines) — including the
  favicon (an inline SVG data URI, no extra file).

## License

The code in this repository is licensed under the **MIT License** (see [LICENSE](LICENSE)).

The optional 📷 Photos mode loads third-party textures from the internet at runtime
(they are not part of this repository): all planet photos come from
[Solar-Wanderer](https://github.com/hyqzz/Solar-Wanderer) based on
[solarsystemscope.com](https://solarsystemscope.com/textures/) (NASA/USGS/SDO data,
CC-BY-4.0), and the 3D engine is [Three.js](https://threejs.org) (MIT). Full credits
are shown in-app in the help window (`?`).

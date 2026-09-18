# Solar System Explorer / Sonnensystem-Entdecker

A 3D, browser-based solar system for kids: fly through space with WASD + mouse,
click any planet to travel to it, and watch a real solar eclipse play out over Earth.

By default everything (textures included) is generated **procedurally in the browser**
— no image files, no server. There is also an optional **📷 Photos**
mode that swaps in real planet photographs (loaded from the internet); if a photo can't
be loaded, that body simply keeps its procedural texture, so you never see broken images.

![Solar System Explorer — all eight planets on their real elliptical orbits, the asteroid belt and Saturn's rings](demo.png)

## Run it

Just open `index.html` in a modern browser (Chrome, Edge, Firefox, Safari).

That's it. No build step, no dependencies to install.

> **Internet needed to start:** the page loads the 3D engine (Three.js) from a CDN
> (`cdn.jsdelivr.net`) on startup, so an internet connection is required for the app
> to launch. The download is verified against a SHA-384 checksum (Subresource Integrity)
> *before* it runs, so tampered or altered content is never executed.
> If the CDN is unreachable, a friendly error screen explains what happened.
> (Your browser may keep the file in its cache after the first visit, which can let
> a reload work offline — but that's not guaranteed.)
>
> The optional 📷 Photos mode additionally downloads real planet textures (from
> `raw.githubusercontent.com`). Any photo that fails to load simply keeps its
> procedural texture.

Optional — serve it statically if you prefer:

```bash
cd explore_sol
python3 -m http.server 8000
# then open http://localhost:8000
```

## Controls / Steuerung

| Key / Action | EN | DE |
|---|---|---|
| Mouse drag | look around | Umschauen |
| `W` `A` `S` `D` | fly forward / left / back / right | Vorwärts / links / rückwärts / rechts fliegen |
| `Space` | fly up | Nach oben fliegen |
| `Ctrl` | fly down | Nach unten fliegen |
| `Shift` (hold) | fly faster | Schneller fliegen |
| Mouse wheel | zoom in / out | Hinein- / herauszoomen |
| Click a planet / the Sun / the Moon | fly to it and follow it | Dorthin fliegen und ihm folgen |
| `Esc` | stop following / close window | Folgen beenden / Fenster schließen |
| Bottom bar | pause or set time speed (0.02/0.05/0.1/0.5/1/5/20/60 days per second) | Zeit anhalten oder Geschwindigkeit wählen (0,02/0,05/0,1/0,5/1/5/20/60 Tage pro Sekunde) |
| 🌑 Eclipse button | start a solar eclipse over Earth | Startet eine Sonnenfinsternis über der Erde! |
| `?` | help / controls | Hilfe / Steuerung |
| EN / DE button | switch language (remembered) | Sprache wechseln (wird gemerkt) |
| 📷 Photos button | switch between real photos and built-in drawings (remembered) | Zwischen echten Fotos und eingebauten Zeichnungen wechseln (wird gemerkt) |
| 🗺 Places button | show/hide the list of all places you can fly to (remembered) | Liste aller Orte ein-/ausblenden, zu denen du fliegen kannst (wird gemerkt) |
| Labels button | show/hide name labels | Namen ein-/ausblenden |

## Features

- **Sun + all 8 planets** with realistic relative sizes, orbital periods and tilts
  (Uranus rolls on its side, Venus spins backwards), plus Earth's Moon.
- **Two texture modes**:
  - *Procedural (default, offline)*: real-looking continents, oceans, ice caps and
    drifting clouds on Earth; Jupiter's bands and Great Red Spot; Saturn's rings with
    the Cassini division; craters on the Moon; a churning solar surface.
  - *📷 Photos*: real planet photographs for the Sun, all 8 planets and the Moon,
    loaded from the internet. Your choice is remembered; any photo that fails to load
    (e.g. offline) falls back to its procedural texture automatically. All photos
    come from [Solar-Wanderer](https://github.com/hyqzz/Solar-Wanderer)
    (solarsystemscope.com, CC-BY-4.0 — NASA/USGS/SDO data), including a real NASA
    cloud photo for Earth's drifting cloud layer. In-app credits are shown in the
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
- **Time controls**: pause, or run at 0.02 / 0.05 / 0.1 / 0.5 / 1 / 5 / 20 / 60 days
  per second (the slow settings are great for watching the Moon's shadow cross Earth).
- **🗺 Places list**: a hideable panel listing every place you can visit — click an entry
  to fly there, no hunting for dark dots. The place you're following is highlighted.
- **Bilingual UI** (English / German), language choice is remembered.

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
- The whole app is a single self-contained `index.html` (~1700 lines).

## License

The code in this repository is licensed under the **MIT License** (see [LICENSE](LICENSE)).

The optional 📷 Photos mode loads third-party textures from the internet at runtime
(they are not part of this repository): all planet photos come from
[Solar-Wanderer](https://github.com/hyqzz/Solar-Wanderer) based on
[solarsystemscope.com](https://solarsystemscope.com/textures/) (NASA/USGS/SDO data,
CC-BY-4.0), and the 3D engine is [Three.js](https://threejs.org) (MIT). Full credits
are shown in-app in the help window (`?`).

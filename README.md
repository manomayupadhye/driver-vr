# Winter Morning Highway: a drivable, VR-ready 3D scene in one HTML file

Drive (or walk) along a hazy 8-lane Indian highway in a compact SUV with a fully modelled, interactive cabin. Traffic changes lanes, obeys signals, speed cameras flash, and highway police chase you if you go over 100 km/h.

Everything lives in **one self-contained `index.html`**: markup, CSS and JavaScript. There is no build step, no image files and no audio files. The graphics are built from Three.js geometry, the screens are drawn at run time on canvases, and the music is synthesised live with the Web Audio API.

> **Live demo:** `https://YOURNAME.github.io/REPONAME/`
> *(Open it in a desktop browser, or in the Meta Quest Browser and press **Enter VR**.)*

<!-- Add a screenshot or GIF here:  ![screenshot](screenshot.png) -->

---

## Features

**Driving and walking**
- Right-hand-drive compact SUV with a 5-seat cabin. Drive from seat 1, or teleport between seats with keys 1 to 5.
- Gears **P / R / N / D**. The car starts in Park; select D on the console to drive.
- Top speed **185 km/h**. Steering angle shrinks with speed so cornering stays believable.
- First and third person, walking on foot, and getting in and out of the car.

**Interactive dashboard** (mouse and VR controllers)
- Speedometer whose hand follows your real speed.
- 4-button central screen: **Home**, **Navigation** (live low-res map with a "you are here" marker), **Climate Control**, **Screen OFF**.
- Climate control: A/C fan speed, driver and passenger temperature, and seat heating and ventilation for both front seats. Also available through physical knobs and buttons, which share the same state as the touchscreen.
- **Emergency (hazard) button**: blinks the tail lamps and indicators, ticks, and flashes the cluster arrows.
- Screen and climate controls work from both front seats. The gear selector only works from the driver seat.

**World and traffic**
- An 8-lane highway that **winds through real bends**: it starts straight, then curves gently left and right (tightest bend about 385 m radius), so you have to steer. Dashed lane lines, a kerbed median and roadside trees all follow the curve.
- A proper landscape: a flat plain beside the road, rolling hills, foothills with **pine forest**, and three layers of **distant mountains** fading into winter haze. Thin clouds and a low morning sun.
- Traffic keeps its distance, signals before changing lane (blinking indicator), and slows for slower cars ahead.
- **Traffic signals** every 400 m: traffic stops on the line at red. Running a red costs a fine.
- **Speed cameras** every 700 m with a flash. Over the limit means a fine and police dispatched behind you.
- **Highway police** patrol your carriageway and start a chase if they see you above 100 km/h. They have a flashing light bar and a siren. Outrun them at high speed, or stop beside them to be busted.
- The central median is a normal drivable strip (rough, with solid shrubs), not an invisible wall.

**Physics**
- Cars are oriented rectangles with mass and spin. Collisions use the Separating Axis Theorem for detection and impulse-based responses (bounce, friction, rotation).
- Hit a car, tree, pole or pedestal and the response depends on where and how hard you hit.

**Audio**
- An original synthwave track in an "outrun" style (about 91 BPM): four-on-the-floor kick, gated clap, pulsing bass, detuned pad that ducks with the kick, arpeggio and a lead melody. All generated live. Starts on your first click or when you enter VR, and can be toggled.

---

## Controls

### Desktop

| Action | Keys |
|---|---|
| Drive or walk | `W` `S` or `↑` `↓` (throttle and brake, or forward and back) |
| Steer or strafe | `A` `D` or `←` `→` |
| Faster (run, or stronger acceleration) | `Shift` |
| Handbrake | `Space` |
| Look | Mouse (click to lock the pointer) or click-and-drag |
| Zoom | Scroll wheel |
| Seats | `1` driver, `2` front passenger, `3` rear left, `4` rear middle, `5` rear right |
| Get in or out | `E` |
| First or third person | `F` |
| Sound on or off | `M` |
| Pull the dashboard model to your face | `G` |
| Press a dashboard control | Aim the crosshair and click. Right-click or `Shift`+click steps down. Scroll over a knob to adjust it. |

### Meta Quest (VR)

| Action | Control |
|---|---|
| Move or drive | Left stick |
| Turn | Right stick (30-degree snap turns) |
| Press a dashboard control | Point the laser and pull the trigger. Grip steps down. |
| Sound on or off | `A` / `X` |
| Get in or out | `B` / `Y` |
| Next seat | Click a stick |
| Grab the dashboard model | Trigger, near the pedestal |

---

## Run it

**Easiest: use the hosted page** (see the link above).

**Locally (desktop only):** serve the folder over HTTP and open it.

```bash
python3 -m http.server 8000
# then open http://localhost:8000/
```

**On a Meta Quest:** WebXR needs **HTTPS**, so host it (GitHub Pages works well), open the link in the Meta Quest Browser, and press **Enter VR**.

### Deploy to GitHub Pages
1. Put the file in a public repository and name it `index.html`.
2. **Settings → Pages → Deploy from a branch → `main` / `(root)` → Save.**
3. After a minute or two it is live at `https://YOURNAME.github.io/REPONAME/`.

An internet connection is needed on first load, because Three.js comes from the jsdelivr CDN (pinned to version 0.160.0).

---

## How the code is organised

Everything is in `index.html`, in numbered, commented sections, so you can read it top to bottom:

| Section | What it does |
|---|---|
| 1 | Renderer, camera rig, fog (ACES tone mapping, soft shadows, `local-floor` for VR) |
| 2 to 3 | Helpers, sky, sun and clouds |
| 4 | **The curved road**: the centre-line maths (`roadAt`, `worldToRoad`) and the road surface ribbons |
| 5 | Terrain, pine forest, backdrop mountains, and the **instanced roadside trees** |
| 6 | **Cabin factory** and the live dashboard (canvas-texture screens, knobs, buttons) |
| 7 | **Car factory** (exterior, wheels, lights) and the police variant |
| 8 | The player's car, traffic, inspection pedestal, and the pooled signals and cameras |
| 9 | Game state and input (keyboard, mouse, pointer lock, seats) |
| 10 | Audio: the synth engine, siren and sound toggle |
| 11 | WebXR setup, VR controllers, thumbsticks |
| 12 | Collision physics, and the traffic AI, signals, cameras and police |
| 13 | The main loop |

**Ideas worth reading in the source**
- **Factory pattern.** Build an object once in a function, then place it many times with a loop (cars, signals, cameras, the cabin).
- **Road space vs world space.** The road is described by two numbers, *s* (distance along it) and *d* (distance across it). Traffic AI works on a "straightened" road in those coordinates and is bent onto the real curve each frame for physics and drawing.
- **Streaming a world.** The road ribbon, terrain and trees only exist near you and are rebuilt as you move. The terrain is rebuilt a few rows per frame so it never causes a stutter.
- **Instancing.** All roadside trees draw in two calls and all forest pines in one.
- **Geometry merging.** The cabin and cars collect pieces and merge them per material, so a whole interior is about 15 draw calls. This matters on a Quest 2.
- **Pooling.** Only 3 signal gantries and 3 cameras exist; they are re-placed along the road each frame.
- **Canvas textures.** The dashboard screens are drawn with the 2D canvas API and redrawn only when something changes.
- **One ray, one list.** Mouse and VR controller clicks use the same raycast and action code, so controls behave the same everywhere.

---

## Performance notes

Built with the Meta Quest 2 in mind: distant scenery is cheap and non-collidable, collision only runs on nearby objects, and static geometry is merged. If it stutters on a headset, try fewer traffic cars (the traffic loop in section 8) or remove the police livery.

## Known limitations

- Collision is 2D (cars cannot roll or tip) and the road itself is flat: there are bends but no climbs or dips. The hills are scenery beside a flat plain, and a soft wall keeps you within about 66 m of the road.
- The car is a stylised, approximate shape, not an exact scale model.
- All traffic signals share one colour at a time; police only patrol your carriageway.
- Fines do not end the game.
- Status text (fines, messages) is on the web page's overlay, which is not visible inside VR. In VR, the instrument cluster shows the limit and a flashing "POLICE!" warning.

---

## Credits and notes

- Built with [Three.js](https://threejs.org/) (MIT licence).
- The music is an original composition generated in code, in a synthwave style. It is not a recording or a copy of any existing track.
- This is an **unofficial student project**. It is not affiliated with, endorsed by or sponsored by Kia or any other car maker. Car names and badges appear only as a design reference.

## Licence

Choose a licence for your repository (for example MIT) and add a `LICENSE` file. Third-party code (Three.js) keeps its own licence.

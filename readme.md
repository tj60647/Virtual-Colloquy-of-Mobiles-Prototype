# Colloquy of Mobiles Simulation Prototype — four teaching demos and the behavior model that came before the live system

This repository is **archived**. It is the 2024–early-2026 TypeScript prototype for a virtual
simulation of Gordon Pask's _Colloquy of Mobiles_: four working browser demos, a pre-consolidation
behavior model in `lib/`, and a curriculum plan for demos that were never built. The live system —
the agent-based simulation engine, the simulation server, the display client, Scene Studio, the
guide, the clip editor, the simulation console, and the sensor, actuator and transmitter clients —
moved to **[Colloquy-of-Mobiles-Virtual-Simulation](https://github.com/tj60647/Colloquy-of-Mobiles-Virtual-Simulation)**
— a private repository — and runs, publicly, at <https://colloquyscenestudio.aroughidea.com>.
Go there for anything current.

What is kept here is worth keeping for two reasons. The four demos are good teaching objects: each
one isolates a single idea and shows it moving. And the position statement below — on what can and
cannot be known about the 1968 installation — still governs the whole project.

---

## What works today

Four demos are built and live. The gallery is at
<https://colloquy-of-mobiles-virtual-simulat.vercel.app> (Vercel project
`colloquy-of-mobiles-simulation-prototype`); all four demo pages answered on
2 September 2026.

| Demo                   | Directory                              | What it shows                                                                                                                                                                                                                                                                                         |
| ---------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 00 Schema Lab          | `apps/demo-TS-00-schema-view/`         | Schema-first workspace. Shows `simulation-config.schema.json` (legacy abstract) and `simulation-config-v2.schema.json` (runtime-oriented v2) side by side and reports whether the loaded config satisfies each. The config picker offers one option, `config_240812.json`.                            |
| 01 Transform Hierarchy | `apps/demo-TS-01-transform-hierarchy/` | Parent-child transforms in the scene graph: a static four-node chain — root, child, grandchild, great-grandchild — with per-node axes, labels, a polar grid and a live JSON view. The rotation code is still in `main.ts`; nothing calls it.                                                          |
| 02 Motion Profiles     | `apps/demo-TS-02-motion-profiles/`     | The trapezoidal profile generator in `lib/subsystems/MotionProfile.ts`, graphed as position, velocity, acceleration and jerk, with a circular rotation view and vector overlays.                                                                                                                      |
| 03 Oscillator Basics   | `apps/demo-TS-03-oscillator-basics/`   | Oscillator-driven transform animation: six mobiles — three females, two males, the beam — released into oscillation, a RELEASED/STOPPED toggle, and compound oscillation through parent-child hierarchies. Its info panel promises sine and triangle profiles; only the trapezoidal generator exists. |

`apps/demo-TS-template/` is the boilerplate the four were cut from.

The gallery page (`apps/public/index.html`) lays out **20 cards, numbered 00 through 19**. Four are
live links; the other sixteen carry the `disabled` class — dimmed, marked "Coming Soon" — but they
are still anchors with real `href`s, so clicking one lands on a 404. That is the honest shape of
this repository: a plan for twenty, four of them built.

## What was never built

Stated plainly, because the old README implied otherwise:

- **There is no server.** No WebSocket server, no state broadcast, no distributed anything. `lib/types/websocket.ts` declares a message protocol; nothing implements it.
- **There is no pulse communication.** No `lib/communication/`, no `Pulse*` class anywhere in `lib/`. Mobiles cannot signal each other.
- **Mobiles do not tick their drives.** `Mobile.update()` calls `horizontalControlSubsystem.act()`, optionally `verticalControlSubsystem.act()`, then re-derives orientation. That is all it does. `DriveSubsystem` exists and is unit-tested, but nothing in the update loop advances it — it advances itself, on a `setInterval` its own constructor starts (`DriveSubsystem.ts:70`, `:156`).
- **Sensors and actuators do not reach each other.** `SensorBase.isInFieldOfView()` does a real cone test and `LightSensor.sense()` takes a callback, but `Environment.update()` only iterates mobiles. No environment-level light or sound propagation exists.
- **No beam arbitration, no male or female behavior state machines.** The word "Beam" appears in `lib/Mobile.ts` only as a doc comment describing a structural component.

So: it is a scene graph, a motion-profile generator, an oscillator, a drive accumulator and a
sensor/actuator geometry library — not a running colloquy. The demos exercise the first three.

## What moved

On **2 September 2026** four directories left this repository for the flagship. The flagship
copies are committed and pushed; nothing was lost.

| Was here                                                                                                      | Now                                                    |
| ------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| `docs/reference/mcleish/system-design/` — the PlantUML diagram library and `DIAGRAM_ASSESSMENT_2026-07-18.md` | flagship `docs/colloquy/system-diagrams/`              |
| `docs/reference/mcleish/physical-build/` — photographs, elevations, light and sensor layout artwork           | flagship `docs/colloquy/physical-build/`               |
| `docs/reference/dowson/` — Dowson's recollections and correspondence                                          | flagship reference library (local-only, not committed) |
| `docs/development/level-of-effort/` — eight level-of-effort demo write-ups                                    | flagship `docs/archive/level-of-effort/`               |

The diagrams in particular kept being edited after this repository stopped being the working copy.
The flagship versions are the current ones.

The museum-installation vision that used to occupy most of this README — hardware lists, network
topology, deployment strategy, a six-component software architecture — has moved to
[`docs/archive/MUSEUM_INSTALLATION_VISION.md`](docs/archive/MUSEUM_INSTALLATION_VISION.md). Most of
what it describes now exists, built differently, in the flagship repository. That archive maps
each item to what actually shipped.

---

## Running it

Root scripts, quoted from `package.json`:

| Script                                      | Command                                         | What it does                                                                                                                                                             |
| ------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `npm run build:demos`                       | `node scripts/build-all-demos.mjs`              | Finds every `apps/demo-TS-*` with a `package.json`, runs `npm install` and `npm run build` in each, copies `apps/public/` and each demo's `dist/` into the root `dist/`. |
| `npm run dev:gallery`                       | `npm run build:demos && npx serve dist -p 3000` | Builds everything, then serves the gallery on port 3000.                                                                                                                 |
| `npm run dev:cleanup`                       | `powershell … ./scripts/kill-dev-servers.ps1`   | Kills stray node servers on ports 3000–3010. Windows only.                                                                                                               |
| `npm run build`                             | `tsc`                                           | Compiles `lib/` per `tsconfig.json`.                                                                                                                                     |
| `npm run type-check`                        | `tsc --noEmit`                                  | Type check without emit.                                                                                                                                                 |
| `npm run watch`                             | `tsc --watch`                                   |                                                                                                                                                                          |
| `npm run lint` / `lint:fix`                 | `eslint .`                                      |                                                                                                                                                                          |
| `npm run format` / `format:check`           | `prettier` over `**/*.{js,ts,json,md}`          |                                                                                                                                                                          |
| `npm test` / `test:watch` / `test:coverage` | `jest`                                          |                                                                                                                                                                          |
| `npm run deploy`                            | `npm run build:demos && vercel deploy --prod`   |                                                                                                                                                                          |

There is **no `npm start`** at the root and no root server. The only `server.js` and `Procfile` in
the repository sit inside `apps/demo-TS-01-transform-hierarchy/`, where `npm start` runs
`node server.js` — a small Express host for that one demo.

Earlier drafts of this README described a root `server.js` and a Heroku deployment. Both were real
once: a root `server.js` and `Procfile` existed from 27 August 2024 and were deleted on
9 February 2026 (commit `bf506c3`), and this README went on describing them until now. Deployment
has been Vercel static hosting since; the only Heroku `Procfile` left is demo 01's.

To work on a single demo, run it in its own directory:

```bash
cd apps/demo-TS-02-motion-profiles
npm install
npm run dev:vite     # or: npm run dev  (PowerShell wrapper that frees ports 3000-3002 first)
```

Each demo carries its own Vite build. **Vite is a devDependency of each demo, not of the root** —
the root has exactly one runtime dependency, `three ^0.168.0`.

## Tests

`npx jest`, run 2 September 2026:

```
Test Suites: 1 failed, 8 passed, 9 total
Tests:       1 failed, 34 passed, 35 total
```

**One test fails.** `lib/__tests__/MotionProfile.test.ts:19`,
`MotionProfile > Initialization > should calculate trapezoidal profile`:

```
Expected: 12.3
Received: 12
Expected precision:    1
Expected difference: < 0.05
Received difference:   0.3000000000000007
```

The test's own comment works the closed-form answer out to 12 s (2 s accelerating, 8 s cruising,
2 s decelerating) and then asserts 12.3 s, with the note "due to inclusive endpoints and 3 phases".
The generator returns 12. Whichever side is wrong, it is the assertion and the generator that
disagree, not the maths and the code. Neither `lib/subsystems/MotionProfile.ts` nor its test has
been edited since 11 February 2026, so this has been red at least that long. It is left as it
stands. The demo does not exercise the case: `apps/demo-TS-02-motion-profiles/` runs the generator
at distance 120, max velocity 15, max acceleration 15 and a 1/40 s timestep, in yo-yo mode — not
the test's 100 / 10 / 5 at the default 0.1 s.

Jest also warns that a worker failed to exit gracefully. The open handle is the drive timer:
`DriveSubsystem`'s constructor starts a `setInterval`, and `Environment.test.ts` is the one suite
that builds Mobiles without a `stopTimer()` teardown, so run on its own it never exits at all.

Config: `jest.config.js`, `ts-jest` preset, `node` environment, roots `lib/` and `apps/`, cache
disabled.

---

## Repository layout

```
apps/
  demo-TS-00-schema-view/          Schema Lab (built, deployed)
  demo-TS-01-transform-hierarchy/  Transform Hierarchy (built, deployed; has server.js + Procfile)
  demo-TS-02-motion-profiles/      Motion Profiles (built, deployed)
  demo-TS-03-oscillator-basics/    Oscillator Basics (built, deployed)
  demo-TS-template/                Boilerplate for a new demo
  public/                          Gallery landing page (index.html, gallery.css)
  SimulationConfigurationFiles/    config.json, config_v2.json, config_240812.json,
                                   config_v2_example.json + two JSON schemas
lib/
  Mobile.ts                        Autonomous entity; extends Transform
  Environment.ts                   Holds mobiles, ticks them, serializes the world
  Transform.ts                     Scene-graph node: local/world transform, parent-child
  SceneGraphLoader.ts              Two-phase loader for config v2
  math/Vector3.ts                  Minimal vector maths
  subsystems/                      Drive, DriveSubsystem, MotionProfile, Oscillator,
                                   HorizontalControlSubsystem, VerticalControlSubsystem
  components/                      SensorBase, ActuatorBase, LightSensor, SoundSensor,
                                   LightActuator, SoundActuator
  visualization/                   THREE.js wrappers (.js), plus TypeScript:
                                   renderers/ThreeJSRenderer.ts, utils/CameraController.ts,
                                   ui/ (CameraControlPanel, MotionProfilesPanel, icons, styles)
  types/                           drives, events, math, state, websocket (+ its own __tests__)
  legacy/                          Deprecated p5.js code, isolated to prevent import. Do not use.
  __tests__/                       8 suites: Drive, DriveSubsystem, Environment,
                                   HorizontalControlSubsystem, Mobile, MotionProfile,
                                   SceneGraphLoader, Transform
  tests/                           README only
docs/                              See below; includes demo-examples/ (README and card templates)
scripts/                           build-all-demos.mjs, kill-dev-servers.ps1
assets/                            One webfont (Roboto Regular)
test-debug.ts, test-loader.ts, test-serialize.ts    Root-level diagnostic scripts, not Jest suites
```

`lib/visualization/` is deliberately mixed: the older THREE.js wrappers are still `.js`, and the
newer renderer, camera controller and UI panels are `.ts`. The migration stopped there.

### Documentation

| File                                                          | What it is                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `docs/DEMO_REFACTORING_PLAN.md`                               | The curriculum. Its inventory enumerates Demos 1–19 across five tiers; a later specification section in the same file still uses an earlier 1–17 numbering and has two different sections both labeled "Demo 14". Demo 00 postdates the plan and is not in it. |
| `docs/REFACTORING_PLAN.md`                                    | TypeScript migration phases.                                                                                                                                                                                                                                   |
| `docs/PULSE_COMMUNICATION_ARCHITECTURE.md`                    | Design for the pulse communication that was never implemented.                                                                                                                                                                                                 |
| `docs/CAMERA_CONTROLLER_SPEC.md`                              | Specification for `lib/visualization/utils/CameraController.ts`.                                                                                                                                                                                               |
| `docs/UI_ARCHITECTURE.md`, `docs/UI_STANDARDS.md`             | Why the demos use no UI framework, and the visual standards they follow.                                                                                                                                                                                       |
| `docs/demo-examples/`                                         | Two worked examples — demo 01 and a hypothetical demo 4.5 — each as a demo README and a gallery card.                                                                                                                                                          |
| `docs/TESTING_AND_LINTING.md`, `docs/TYPESCRIPT_MIGRATION.md` | Tooling notes.                                                                                                                                                                                                                                                 |
| `docs/terminology.md`                                         | Canonical mapping between Pask's concepts and this codebase.                                                                                                                                                                                                   |
| `docs/archive/MUSEUM_INSTALLATION_VISION.md`                  | The archived installation vision, with a map to what the flagship actually built.                                                                                                                                                                              |
| `docs/reference/pask/`                                        | Pask's own writings on the Colloquy (1968).                                                                                                                                                                                                                    |
| `docs/reference/mcleish/`                                     | The 2018 reconstruction's "How It Works, Recollections, and Observations". The diagrams and build photographs that used to live here are now in the flagship.                                                                                                  |
| `DEPLOYMENT.md`, `TODO.md`                                    | Deployment guide and the last sprint board, both frozen at February 2026.                                                                                                                                                                                      |

---

## Simulation philosophy and architecture

### Position statement: simulating the 2018 interpretation

This simulation is strictly based on the **2018 physical reconstruction by McLeish**, which is an
_interpretation_ of the original work.

The dynamic experience of Gordon Pask's original 1968 installation (the motion, sound, and light
interactions) is primarily lost to history:

- There are no known recordings of the piece in motion.
- There is no surviving audio.
- Most photographs show the system powered off.
- The original 1968 schematic diagrams were not strictly followed in construction (as noted by Mark Dowson).

Therefore, this project simulates the **2018 interpretation of Pask's intent**. Where the 1968
record is static or ambiguous, the specific behavioral logic, timing, and sensory interactions
visualized here are derived entirely from the choices made in the 2018 reconstruction project.

### Simulating analog concurrency

Pask's installation was a system of **analog, concurrent entities**. To simulate this digitally:

1. **Orchestrated updates**: We use a **synchronous simulation loop**, updating every Mobile once per "tick".
2. **Apparent autonomy**: From the user's perspective, Mobiles appear to act independently and simultaneously, preserving the social dynamics Pask intended.

**Why not true parallel threads?**
While modern web technologies (Web Workers) allow parallelism, they introduce significant
complexity (race conditions, shared state management) that distracts from the core goal:
**modeling the social cybernetics**. Our synchronous loop provides the necessary reliability to
explore the _interaction logic_ defined in the 2018 reconstruction.

### Architecture patterns (the "how")

- **Agent-based**: Each Mobile is a self-contained entity with private state (drives, position).
- **Composition**: Mobiles obey the "has-a" relationship (a Mobile _has a_ DriveSubsystem), allowing modular upgrades.
- **Hierarchical state machines**: Behavior is organized in nested layers (Alive → Unsatisfied → Searching) to enforce valid logic.

The third pattern is aspirational in this repository. `lib/subsystems/DriveSubsystem.ts` computes a
satisfied/unsatisfied state and a dominant drive; nothing above it consumes that state. The full
hierarchy was built in the flagship, not here.

---

## The model this was aiming at

Three agent types, from the 2018 implementation diagrams. Only the parts marked below reached code
in this repository; the rest is here so the config schema and the half-built subsystems make sense.

**Shared drive system (males and females).** Two internal drives, **O** and **P** — Orange and
Puce, Pask's own colour labels, carried into the config as `orange` and `puce`. They increment
continuously. Below their lower limit the agent is satisfied and inert; above it, unsatisfied and
searching, prioritizing whichever drive is higher. Successful engagement with a partner decrements
that specific drive back under the threshold. _In code:_ `lib/subsystems/Drive.ts` and
`DriveSubsystem.ts` implement the accumulation, the thresholds and the dominant-drive resolution,
including the equal-drives edge case. `DriveSubsystem.getDriveState()` says it follows
`DriveManager.js` "exactly"; that file was deleted from `lib/` by the same commit that added the
TypeScript port, so the claim can no longer be checked against anything in this repository. No tick
calls any of it: `Mobile.update()` never touches the drive subsystem. The drives still creep,
because every `DriveSubsystem` starts a `setInterval` in its constructor and every `Mobile`
constructs one — but nothing reads the state that results.

**Male mobiles.** Drive subsystem plus a horizontal subsystem that rotates the body to search for a
partner; oscillate while unsatisfied, lock on and exchange signals to reduce drive. _In code:_
`HorizontalControlSubsystem` drives the oscillation. The searching and engaging behavior does not
exist.

**Female mobiles.** The same, plus a vertical reflector subsystem — an independently moving mirror
used to negotiate the connection. _In code:_ `VerticalControlSubsystem` moves the reflector frame.
The negotiation does not exist.

**The beam.** A reactive arbitrator with no drives of its own. It compares the dominant drive
reported by Male I against Male II; the higher one takes control of the beam, which otherwise
oscillates in search. _In code:_ nothing — and in `config_v2.json` the beam is not a drive-less
arbitrator either. It is just another mobile: a base, a horizontal control frame, a full O/P drive
block identical to the others, and a speaker and microphone. Nothing reads any of it.

The state, sequence and activity diagrams that specify all of this now live in the flagship
repository under `docs/colloquy/system-diagrams/`.

---

## Configuration

The simulation is data-driven, defined by a strict JSON configuration file.

### Config format (v2)

A **hybrid scene graph**: one file defines both the spatial hierarchy (transform nodes) and the
logical hierarchy (mobiles, subsystems, components).

- **Mobiles**: top-level entities. `config_v2.json` declares six — three females, two males, one beam.
- **Coordinate systems**: a flat list of spatial transforms with parent references, avoiding deep nesting. World, Armature, Plinth, then a base and one or two control frames per mobile.
- **Subsystems**: logical parts of a mobile (`horizontal_control`, `vertical_control`) that drive specific coordinate systems.
- **Components**: attachments (`sound_actuator`, `sound_sensor`, `light_sensor`, …) positioned relative to the mobile.

Key file: `apps/SimulationConfigurationFiles/config_v2.json`, validated against
`simulation-config-v2.schema.json`.

### SceneGraphLoader

`lib/SceneGraphLoader.ts` parses the config in two phases:

1. Build the `Transform` hierarchy from `coordinateSystems`.
2. Instantiate `Mobile` objects, attach subsystems (oscillators), create sensors and actuators.

`lib/__tests__/SceneGraphLoader.test.ts` loads the real `config_v2.json` and asserts all six
mobiles come back. It passes.

---

## Technology

- **TypeScript** — simulation logic and the newer visualization layer.
- **Three.js `^0.168.0`** — the root's only runtime dependency.
- **Vite** — per-demo build and dev server. A devDependency of each demo, not of the root.
- **Jest + ts-jest** — unit tests.
- **ESLint + Prettier** — quality and formatting.
- **Vanilla TypeScript and custom CSS in the demos** — no React, Vue, Tailwind or Bootstrap. The demos put their weight into the 3D simulation, not the interface, and the UI in each is small enough that a framework would be pure overhead. See [`docs/UI_ARCHITECTURE.md`](docs/UI_ARCHITECTURE.md) and [`docs/UI_STANDARDS.md`](docs/UI_STANDARDS.md).
- **Vercel** — static hosting for the gallery and demos, via `vercel.json` (`buildCommand: npm run build:demos`, `outputDirectory: dist`, clean URLs, trailing slashes).

---

## Provenance

Commits run from **24 August 2024** to **21 July 2026**. Active development stopped in
March 2026; the four commits after that are diagram work that has since moved to the flagship.

Author: Thomas J McLeish, who built the 2018 physical reconstruction this simulation interprets.
License: MIT.

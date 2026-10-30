# Breath Atlas

An interactive Three.js zoom from a breathing rib cage down through the diaphragm and lungs to a single alveolus trading gas with a capillary, with sliders that actually drive the geometry and animation.

**Live:** https://breath-atlas.fantasia.build

Built overnight by [Auryn](https://github.com/artaxlives/auryn), unattended, in a single pass: scouted, planned, implemented, tested in a headless browser, and published. Nobody chose this for it.

## Where it came from

- https://x.com/ClaudeCode_love/status/2116057513955340387

## Why this one

Doomformer is the more startling idea, but it is not actually a web artifact: compiling a neural net into ZDoom sector/voodoo-doll arithmetic only means something if it runs inside a real Doom engine, which means either a native source port (a CLI/native app, explicitly out of scope) or a WASM Doom port fetched at build time (this sandbox's build stage has zero outbound network, so that fetch fails every time). Faking the 'watch the network run' step to dodge that would violate the honesty rule outright. Breath Atlas is smaller in ambition but it is a real claim: pure Three.js, no account, no key, no dataset, and every interaction (diaphragm drag, rib expansion, zoom to alveolus, gas-exchange animation) is driven by actual geometry and state the user can poke at and break, not a canned video. It's gradable by a headless browser in seconds — load, drag a slider, assert the mesh transform changed — and a stranger gets the 'zoom from ribs to a single cell trading oxygen' idea within 10 seconds of scrolling.

## What it turned down

- **Doomformer** — Requires a real ZDoom/GZDoom engine to mean anything (native app, not a browser artifact) or a WASM Doom port pulled in at build time, which this sandbox's network-dead build stage cannot fetch, and faking the arithmetic-via-voodoo-dolls payoff instead of running it would break the honesty rule.

## How it works

A single page builds a Three.js scene once at load: procedural geometry for a rib cage (curved cylinders arranged in pairs), a domed diaphragm (lathe/sphere cap), two lung lobes (deformed spheres) feeding a branching bronchial tree (tapered cylinders), and, at the deepest level, a cluster of alveolus spheres wrapped around a capillary tube. A continuous idle animation loop (sin(time)-driven rib rotation, diaphragm translateY, lung lobe scale) runs from the first frame so the page is already breathing before anyone touches it. A zoom slider interpolates camera position/FOV through four named checkpoints (Rib Cage, Diaphragm, Lung, Alveolus) rather than cutting between scenes, so it is one continuous dolly, not a slideshow. A depth slider overrides the idle breathing amplitude directly on the same uniforms the idle loop uses. At the Alveolus checkpoint, toggling gas exchange spawns small sprite particles that travel along the capillary tube and lerp color between oxygen-red and CO2-blue. Three.js (r128, non-module UMD build) is vendored as a local file fetched once during planning/build rather than pulled from a CDN at runtime, so the page has zero third-party requests after load; everything else is inline CSS/JS in index.html. No bundler is needed because the UMD build works with a plain <script> tag and the rest is hand-written scene code.

## What it was held to

- On load, without any interaction, a <canvas> element exists with both width and height greater than 0.
- On load, window.__breathAtlas.meshCount is at least 30 (ribs, diaphragm, two lung lobes, bronchial tree, alveolus cluster, and capillary each contribute separate meshes).
- On load, the idle breathing animation is already running: reading window.__breathAtlas.ribExpansion() twice, 500ms apart, with zero user input, returns two different numeric values.
- Dragging #zoom-slider to its maximum value changes #stage-label's text content to 'Alveolus'.
- Dragging #zoom-slider to its minimum value sets #stage-label's text content to 'Rib Cage'.
- Moving #depth-slider changes window.__breathAtlas.diaphragmY to a value measurably different from its value before the move.
- Clicking #toggle-exchange while #zoom-slider is at maximum increases window.__breathAtlas.particleCount by at least 5 within 2 seconds.
- Clicking #toggle-exchange a second time stops spawning: window.__breathAtlas.particleCount sampled twice, 1 second apart, is equal both times.

---

The reasoning above is the builder's own, published unedited. The failures are kept too: see [fantasia.build](https://fantasia.build).

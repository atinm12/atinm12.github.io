# Crossy Road

A browser-based Crossy Road clone built with plain HTML/CSS/JavaScript and [three.js](https://threejs.org/) for 3D rendering. Hop your character forward across an endless series of lanes, dodge the traffic, and see how far you can get before your run ends.

**▶ Play it live:** https://atinm12.github.io/crossy-road/

## How to play
- **Move forward:** `↑` or `W`
- **Move back:** `↓` or `S`
- **Move left:** `←` or `A`
- **Move right:** `→` or `D`

Your score goes up the further forward you travel. Getting hit by a hazard ends the run — press **Restart** to try again.

## Tech notes
- Single static `index.html` — no build step and no server required.
- three.js is loaded from a CDN (`cdnjs.cloudflare.com`), so there are no dependencies to install.
- Runs anywhere static files are served, including GitHub Pages.

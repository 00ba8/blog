---
layout: post
title: Notes on publishing on fxhash
---

##### Deps

All dependencies should be included locally with the main scripts, e.g. in case of
p5js, the p5.min.js should be included and referenced locally, no CDNs or other
networked locations.

This ensures the project will continue to work shall the CDN or other networked location
be updated or moved.

Example:

`<script src="./p5.min.js"></script>`

Note the `./` path.

##### Window resize and generative content

The pseudo random generator seed might not be always picked, especially on e.g. the
window resize event. Simple solution might be to re-initialize during `setup()`.

Example:
```
fxrand = sfc32(...hashes);
randomSeed(sfc32(...hashes));
noiseSeed(sfc32(...hashes));
```
Will ensure no artefacts or unexpected output on e.g. window resize events.

Example for resize:
```
function windowResized() {
  let sWindow = min(windowWidth, windowHeight);
  resizeCanvas(sWindow, sWindow, true);
  setup();
}
```
This is the simplest scenario when no `draw()` is used.

##### Save image and resize canvas for output

Somewhat not optimal, but does the trick as a quick fix:
```
function saveImage() {
  ww = windowWidth;
  wh = windowHeight;
  windowWidth = 1200;
  windowHeight = 1200;
  setup();
  save('OfLinesAndShapes-' + fxhash + '.png');
  // reset
  windowWidth = ww;
  windowHeight = wh;
  setup();
}
```
Note the resetting of the window as such and regenerating the content.
This is again the simplest scenario with no `draw()`, where canvas content
is fully generated within `setup()` for static, single frame output.

##### Testing

If access to different OS's and browsers is available, allow some time to test
on as many different browsers as possible.

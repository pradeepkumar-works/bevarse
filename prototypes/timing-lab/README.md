# Timing lab (throwaway)

A prototype for two decisions. It is not app code, and nothing imports it.

1. How long a key press takes to become sound in the browser, for touch, computer keys and a MIDI keyboard, plus a tap-along calibration.
2. Whether Canvas 2D or Skia (CanvasKit 0.39.1, the engine React Native Skia uses on web) draws falling notes more smoothly.

## Run it locally (needed for a MIDI keyboard)

The hosted copy cannot use Web MIDI, because its frame blocks device APIs. To test a MIDI keyboard, serve this folder on your machine and open it in Chrome or Edge:

```sh
npx serve prototypes/timing-lab
```

Skia loads CanvasKit from jsDelivr when no local `canvaskit.js` sits next to the page.

Press Copy results and paste the JSON into the project thread.

# Phone portrait-render test

This branch is an isolated test in `ForTesting`. It does not modify `CAM-ASCII-cpp/main`.

## What this test changes

- On small touch-first phone viewports only, the WebGL/GLFW window is set to **720 × 1280**.
- Later GLFW window-size requests are kept at that portrait size on phones, so the existing 16:9 resize correction cannot switch the phone drawing buffer back to landscape.
- The browser camera bridge center-crops the live phone camera feed to 9:16 before passing it through the app's legacy 16:9 camera buffer. The portrait render target reverses that temporary stretch, rather than cropping the already-rendered desktop image.
- PNG snapshots and video recordings capture the actual app canvas directly. There is no extra portrait export canvas and no post-render horizontal crop.
- Desktop browsers do not enter the phone-only branch. Their original 1280 × 720 window, camera frame handling, PNG capture, and video capture remain unchanged.

## Test checklist

1. Open the page on a phone in portrait orientation using HTTPS / GitHub Pages.
2. Confirm the live ASCII preview uses the full portrait frame.
3. Save a snapshot and confirm its dimensions are 720 × 1280, with no left/right content chopped off.
4. Record a short video and confirm it is 9:16 and keeps the full portrait composition.
5. Open the same page on a desktop browser and confirm the existing 16:9 preview and exports remain unchanged.

The WebAssembly binary is loaded read-only from `CAM-ASCII-cpp/main`; this branch changes only the test page and its browser-side JavaScript. A physical-phone run is still required to verify the browser/WebGL resize behavior.

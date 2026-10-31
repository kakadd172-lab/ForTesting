# Phone portrait-render test

This branch is an isolated test in `ForTesting`. It does not modify `CAM-ASCII-cpp/main`.

## What this test changes

- On small touch-first phone viewports only, the real GLFW/WebGL window is created at **720 × 1280** from the start.
- The portrait dimensions are passed into `GLFW.createWindow` before the generated runtime stores the window's logical width and height. This is important: resizing only after window creation can leave the app's logical render dimensions in landscape.
- Later GLFW size requests are kept portrait while the phone viewport matches the mobile condition. If the viewport changes to landscape, the test switches back to the existing 1280 × 720 window size.
- The browser camera bridge keeps its legacy 1280 × 720 input buffer. On phones it places a 9:16 center-cropped camera image into that buffer; the portrait render grid then scales the image into the matching 9:16 grid. This is part of the rendering path, not a crop of a finished landscape screenshot.
- TXT continues to come from the compiled app's portrait character grid. On phones, PNG now captures the actual WebGL canvas instead of the compiled app's older PNG byte export. Mobile video mirrors each rendered WebGL frame to a same-size 2D canvas before recording, so selected colour modes and effects are included; the downloaded extension is matched to the format the browser supports (MP4 when available, otherwise WebM).
- Desktop browsers do not match the phone-only condition, so their existing window sizing and rendering path are left as-is.

## Test checklist

1. Open the page on a phone in portrait orientation using HTTPS / GitHub Pages.
2. Confirm the live ASCII preview itself is portrait and shows the complete camera composition (not a landscape image squeezed into a narrow display).
3. Save a PNG and inspect its pixel dimensions and composition.
4. Save a TXT and confirm the character grid has substantially more rows than columns.
5. Record a short video and confirm it is 9:16 with the same portrait composition as the live preview.
6. Open the same test page on a desktop browser and confirm the existing 16:9 preview and exports remain unchanged.

## Important verification status

The WebAssembly binary is loaded read-only from `CAM-ASCII-cpp/main`; this test does not rebuild or replace it. The fix is therefore a browser-runtime change to the dimensions supplied to the existing GLFW/WebGL renderer. The mobile PNG/video changes have been committed, but an actual browser run on a phone is still required to confirm the exported image, the selected visual mode in recorded video, and format compatibility. Do not treat this branch as proven until those phone tests pass.

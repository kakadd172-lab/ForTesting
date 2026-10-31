# Phone portrait-render test

This branch is an isolated test in `ForTesting`. It does not modify `CAM-ASCII-cpp/main`.

## Mobile rendering and export changes

- The phone-only GLFW window is created at **720 × 1280** before the generated runtime stores its logical dimensions.
- Mobile resize callbacks now check both logical window dimensions and framebuffer dimensions. If either becomes landscape, the actual GLFW canvas is reset to 720 × 1280.
- The mobile PNG callback exports the visible WebGL canvas directly with `canvas.toBlob()`. It checks both the WebGL drawing-buffer dimensions and the canvas backing dimensions; if either is landscape, it attempts to restore the portrait window and waits for another rendered frame. It refuses to save a landscape image rather than cropping it.
- TXT still comes from the compiled app's character grid.
- Mobile video uses Mediabunny with WebCodecs AVC/H.264 and an MP4 output container. It captures frames directly from the original WebGL canvas, preserving the app's selected visual mode, colors, CRT/HUD overlays and animation. It does not create a WebM file or rename WebM data as MP4. If AVC/H.264 encoding is unavailable, it reports an error instead of downloading a different format.
- Desktop browsers retain their existing WebM browser-recorder path and PNG byte-export path.
- The page uses a versioned JavaScript URL to avoid reusing a cached copy of the old export code.

## Phone test checklist

1. Open this branch's GitHub Pages test page on a phone in portrait orientation. Reload it after deployment.
2. Save a PNG. Its actual image dimensions should be portrait (height greater than width), and its composition should match the visible preview.
3. Save a TXT. Confirm the character grid has substantially more rows than columns.
4. Record a short video in RGB, then another in Cyberpunk. Both downloads should be genuine `.mp4` files and show the same colours/effects visible in the preview.
5. Test on a desktop browser and confirm its existing 16:9 view and exports are unchanged.

## Verification status and limitations

The WebAssembly binary is still loaded read-only from `CAM-ASCII-cpp/main`; it has not been rebuilt or replaced. The changes are isolated to the browser HTML/JavaScript in this `ForTesting` branch. The source has been reviewed, but actual phone-browser testing is still required. MP4 encoding depends on the phone browser providing WebCodecs AVC/H.264 support; if it does not, the page reports that MP4 recording cannot start rather than creating WebM.

# 3D Gaussian Splatting Viewer User Manual (English)

This document is the comprehensive user manual for the WebGL-based real-time **3D Gaussian Splatting Viewer (`splat`)**.  
It allows you to render, view, and interact with 3D Gaussian Splatting models (`.splat` / `.ply`) directly inside any modern web browser with high performance.

---

## Table of Contents

1. [Overview & Key Features](#1-overview--key-features)
2. [Quick Start (Hosting & Local Setup)](#2-quick-start-hosting--local-setup)
3. [Controls & Navigation](#3-controls--navigation)
   - [Keyboard Navigation](#keyboard-navigation)
   - [Mouse Controls](#mouse-controls)
   - [Trackpad Gestures](#trackpad-gestures)
   - [Touch Controls (Mobile / Tablet)](#touch-controls-mobile--tablet)
   - [Gamepad / Controller](#gamepad--controller)
4. [Camera & View Management](#4-camera--view-management)
   - [Switching Preset Cameras](#switching-preset-cameras)
   - [Copying & Sharing Camera Views (URL Hash & Clipboard)](#copying--sharing-camera-views-url-hash--clipboard)
   - [Loading Custom Cameras (cameras.json)](#loading-custom-cameras-camerasjson)
5. [Loading 3D Models](#5-loading-3d-models)
   - [Using URL Parameters](#using-url-parameters)
   - [Drag & Drop Direct Loading](#drag--drop-direct-loading)
6. [Data Conversion Guide (PLY to SPLAT)](#6-data-conversion-guide-ply-to-splat)
   - [Method A: In-Browser Automatic Conversion (Drag & Drop)](#method-a-in-browser-automatic-conversion-drag--drop)
   - [Method B: Python Command-Line Script (convert.py)](#method-b-python-command-line-script-convertpy)
7. [Project Structure](#7-project-structure)
8. [Troubleshooting & FAQ](#8-troubleshooting--faq)

---

## 1. Overview & Key Features

- **Blazing Fast WebGL 1.0 Rendering**: Pure WebGL 1.0 and custom GLSL shaders without external rendering heavyweight dependencies (like Three.js or WebGPU).
- **Progressive Streaming & Rendering**: Starts rendering immediately while downloading the model, enabling instant interaction.
- **Asynchronous WebWorker Sorting**: Sorts splat depth in background CPU threads without stuttering the 60 FPS rendering pipeline.
- **Multi-Device Support**: Full control support for keyboard, mouse, trackpads, mobile touchscreens, and USB/Bluetooth game controllers.
- **Camera Sharing & Matrix Export**: Easily copy the exact 4x4 View Matrix to your clipboard or encode it into the URL hash to share specific viewpoints.

---

## 2. Quick Start (Hosting & Local Setup)

### Online Access
Access hosted instances directly via web browser:
- Deployed instance: `https://splat-three.vercel.app/`
- Original demo: `https://antimatter15.com/splat/`

### Running Locally
Due to browser CORS and local file security restrictions (`file:///` protocol blocks streaming and workers), you need a local HTTP server.

#### Option A: Python 3 (Recommended)
```bash
cd splat
python3 -m http.server 8000
```
Open `http://localhost:8000` in your web browser.

#### Option B: Node.js / npx
```bash
cd splat
npx serve .
```

### macOS Desktop App (.dmg) Build
You can build a native macOS standalone desktop app (`.dmg` / `.app`) using Tauri.

```bash
# Install dependencies (first time only)
npm install

# Build macOS DMG bundle
npm run build
```
The output installers will be generated at:
- `src-tauri/target/release/bundle/dmg/SplatViewer_1.0.0_aarch64.dmg`
- `src-tauri/target/release/bundle/macos/SplatViewer.app`

> For installing and using the desktop app, see the [Desktop App Manual](../DESKTOP_MANUAL_EN.md).

---

## 3. Controls & Navigation

### Keyboard Navigation

| Key | Action |
| :--- | :--- |
| **↑ / ↓** (Up / Down Arrow) | Move Forward / Backward |
| **← / →** (Left / Right Arrow) | Strafe Left / Right |
| **Space** | Jump / Ascend |
| **W / S** | Tilt Camera Up / Down (Pitch) |
| **A / D** | Turn Camera Left / Right (Yaw / Pan) |
| **Q / E** | Roll Camera Counterclockwise / Clockwise |
| **I / K** | Orbit Camera Up / Down around target |
| **J / L** | Orbit Camera Left / Right around target |
| **P** | Resume Default Carousel Animation |
| **C** | Display & Copy Current Camera View Matrix |
| **V** | Save Current View Matrix to the URL Hash (`#...`) |
| **R** | Hide / Dismiss the on-screen Camera Info Overlay |
| **0 – 9** | Switch to Pre-loaded Preset Camera Views (0 to 9) |
| **+ (Plus) / - (Minus)** | Cycle to Next / Previous Loaded Camera View |

### Mouse Controls

| Action | Result |
| :--- | :--- |
| **Left Click & Drag** | Orbit around the scene |
| **Right Click (or Ctrl / Cmd + Click) & Drag** | Move Forward / Backward (Vertical) or Strafe Left / Right (Horizontal) |

### Trackpad Gestures

| Gesture | Result |
| :--- | :--- |
| **Two-finger Scroll** | Orbit Up/Down/Left/Right |
| **Pinch-in / Pinch-out** | Move Forward / Backward (Zoom) |
| **Ctrl + Scroll** | Move Forward / Backward |
| **Shift + Scroll** | Move Up/Down or Strafe Left/Right |

### Touch Controls (Mobile / Tablet)

| Gesture | Result |
| :--- | :--- |
| **One-finger Drag** | Orbit camera |
| **Two-finger Pinch** | Move Forward / Backward (Zoom) |
| **Two-finger Twist** | Roll Camera Clockwise / Counterclockwise |
| **Two-finger Pan** | Pan and move Up/Down/Left/Right |

### Gamepad / Controller
Plug in any compatible USB or Bluetooth game controller (PlayStation, Xbox, generic gamepad), and the analog sticks will automatically allow free-flight navigation and look-around.

---

## 4. Camera & View Management

### Switching Preset Cameras
- Press **0 – 9** on your keyboard to instantly teleport to the predefined camera positions.
- Use **`+`** and **`-`** to step through available cameras sequentially.
- The active camera ID is displayed at the top-right corner (`cam 0`, `cam 1`, etc.).

### Copying & Sharing Camera Views (URL Hash & Clipboard)

1. **Press `C` (Copy & Show Camera Info)**:
   - Copies the 4x4 View Matrix to your system clipboard.
   - Shows a notification box at top-left containing the matrix.
   - Press **`R`** to dismiss the box immediately (or let it fade out after 5 seconds).

2. **Press `V` (Save to Hash)**:
   - Updates the browser URL hash to `#[-0.64,0.76,...]`.
   - Bookmark or copy this URL to return to the exact same angle.

3. **Press `P` (Resume Animation)**:
   - Resumes the automated smooth orbit rotation.

### Loading Custom Cameras (cameras.json)
Drag and drop a `cameras.json` file (exported from COLMAP or 3DGS training pipelines) directly onto the canvas. The cameras will immediately populate the `0–9` and `+/-` hotkeys.

## 5. Loading 3D Models

### Using the "Open Splat File" Button
Click the **"Open Splat File"** button located at the top-left corner to open a native file picker dialog.
- Select any local `.splat`, `.ply`, or `cameras.json` file on your computer.
- The active file name is displayed in a badge next to the button.

### Drag & Drop Direct Loading
Drag any local `.splat`, `.ply`, or `cameras.json` file directly onto the browser canvas to load and render it immediately.

### Using the "Load URL" Box
Type the file name (e.g. `church.splat`) or URL (e.g. `https://example.com/models/my_scene.splat`) of the model file to open into the box at the top-center of the screen, then press **Enter** or click **Load URL**. This does the same as the `?url=` parameter described below. While the cursor is in the box, keys do not move the camera.

### Using the "Set View" Box
Paste a view matrix copied with the **C** key (e.g. `[-0.64,0.76,...]`) into the box at the top-right (below the camera number), then press **Enter** or click **Set View** to switch to that angle. The file is not reloaded. This does the same as `#[matrix]` in the URL. If the input is not 16 numbers, the box turns red.

### Using URL Parameters
Pass the model name or external URL using the `?url=` query parameter.
The examples below assume the app URL is `https://splat-three.vercel.app/`.

```text
https://splat-three.vercel.app/?url=<file_or_url>#[initial_matrix]
```

#### Example 1: Loading from the default Hugging Face dataset
If a relative filename is provided, the viewer automatically resolves it from `https://huggingface.co/datasets/stpete2/splat/resolve/main/`:
```text
https://splat-three.vercel.app/?url=fountain.splat
https://splat-three.vercel.app/?url=church.splat
https://splat-three.vercel.app/?url=town_drone.splat
```

#### Example 2: Loading from a custom CORS-enabled server
Provide the HTTP/HTTPS URL of the model file:
```text
https://splat-three.vercel.app/?url=https://example.com/models/my_scene.splat
```

---

## 6. Data Conversion Guide (PLY to SPLAT)

Standard 3D Gaussian Splatting output files (`point_cloud.ply`) can be converted into the compact `.splat` format.

### Method A: In-Browser Automatic Conversion (Drag & Drop)
1. Open the viewer in your browser.
2. Drag and drop your `.ply` file onto the window.
3. The internal WebWorker will convert it to `.splat`, display it, and trigger a download of the converted `.splat` file (named after the original file, e.g. `point_cloud.splat`).

### Method B: Python Command-Line Script (convert.py)
A lightweight Python script is included for offline or batch conversion.

#### Prerequisites
```bash
pip install plyfile numpy
```

#### Running the Conversion
```bash
# Convert a single file
python convert.py input_point_cloud.ply -o output.splat

# Convert multiple files in batch
python convert.py scene1.ply scene2.ply scene3.ply
```

---

## 7. Project Structure

```text
splat/
├── index.html          # Main HTML entry point (UI, spinner, canvas)
├── main.js             # Core WebGL renderer, WebWorker, and input controllers
├── convert.py          # Python converter script (PLY -> SPLAT)
├── my_splat_data.md    # Sample dataset links and URLs
├── my_2026_02_12.md    # Development log / notes
├── README.md           # Original technical documentation & notes
├── USER_MANUAL_JA.md   # User Manual (Japanese)
└── USER_MANUAL_EN.md   # User Manual (English)
```

---

## 8. Troubleshooting & FAQ

### Q1: The canvas is completely black or shows an error message.
- **CORS Restriction**: If loading from an external domain, ensure the hosting server returns `Access-Control-Allow-Origin: *`.
- **Local File Restriction**: Do not open `index.html` via double-click (`file:///`). Run a local HTTP server (`python3 -m http.server`).
- **File Not Found (404)**: Verify that the specified `.splat` filename exists on the server.

### Q2: Performance is low or frames drop.
- For very large scenes (>1 million splats), initial sorting may take a brief moment.
- On high-resolution (Retina / 4K) displays, the viewer dynamically downsamples the internal canvas buffer for performance.

### Q3: How to reset camera orientation?
- Press number **`0`** to switch back to the primary default camera.
- Press **`P`** to resume automated animation orbit.
- Refresh the page to reset all parameters.

---

*Original WebGL Splat Viewer by Kevin Kwok ([antimatter15](https://github.com/antimatter15/splat))*  
*Customized & Maintained by stpete ([tztechno](https://github.com/tztechno/splat))*

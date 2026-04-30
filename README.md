# Parallax — AI Depth from a Single Photo to 3D Space

> Semester project · **B-MSAP**, Part B (Vibecoding Web App)

An interactive web app that turns a **single photograph** into a walkable 3D space. The **Depth Anything V2** neural network estimates a depth map directly in the browser, and the original pixels are rendered as a 3D point cloud through a custom WebGL2 engine.

🌐 **Live demo:** [xadazhii.github.io/parallax-3D](https://xadazhii.github.io/parallax-3D/)

---

## ✨ Features

### Input
- 📁 **Drag & drop** any photo (JPG / PNG / WEBP)
- 📋 **Paste from clipboard** (Ctrl/Cmd + V)
- 🖼 **4 sample images** (ocean, mountains, street, forest)
- 🎥 **Live Webcam Mode** — real-time depth estimation from your camera

### Visualization
- 🎨 **3 color modes** — original RGB · depth gradient · surface normals
- 👁 **Anaglyph 3D** (red/cyan glasses!)
- ⟳ **Auto-rotation** or **Wiggle** effect (Facebook 3D Photo style)
- 🎚 Sliders for **depth strength**, **point size**, and **background cutoff**
- 🖼 Mini **depth map preview** in the corner

### Export
- ↓ **PNG screenshot**
- ↓ **`.PLY` file** (opens in MeshLab, Blender, CloudCompare, etc.)

### Controls
- 🖱 **Left click** — orbital rotation
- 🖱 **Right click** — pan
- 🎡 **Scroll** — zoom
- 📱 **Touch + pinch zoom** on mobile

---

## 🧠 How it works

```
┌──────────────────────────────────────────────────────┐
│  YOUR BROWSER (Chrome / Safari / Firefox)            │
│                                                       │
│  Photo  ──→  Depth Anything V2 (ONNX, ~50 MB)        │
│                  │                                    │
│                  ↓ WebGPU / WASM inference            │
│                                                       │
│              Depth map (Float32Array)                 │
│                  │                                    │
│                  ↓ + original RGB pixels              │
│                                                       │
│              3D point cloud (~220k points)            │
│                  │                                    │
│                  ↓ Custom WebGL2 renderer             │
│                                                       │
│              Interactive 3D scene                     │
└──────────────────────────────────────────────────────┘
```

**No backend, no server.** Your photo never leaves the browser.

---

## 🛠 Tech stack

| Layer | Technology |
|---|---|
| **AI / ML** | [Transformers.js](https://github.com/huggingface/transformers.js) v3 + [Depth Anything V2](https://depth-anything-v2.github.io/) |
| **GPU acceleration** | WebGPU (with WebAssembly SIMD fallback) |
| **3D rendering** | Custom WebGL2 point-cloud engine (custom shaders) |
| **Idle background** | Canvas 2D parallax starfield |
| **Other** | Vanilla JS, no build step, single HTML file |

### Custom implementations
- **Bilateral depth smoothing** — 3×3 edge-aware filter to remove artifacts
- **Real-time background cutoff** in the vertex shader
- **Anaglyph rendering** with color masking and IPD eye-offset
- **Surface-normal estimation** from depth-map gradient
- **Adaptive-stride point cloud builder** for performance scaling

---

## 🚀 Running locally

ES modules require an HTTP server (they don't work over `file://`):

```bash
# Clone
git clone https://github.com/xadazhii/parallax-3D.git
cd parallax-3D

# Serve via Python
python3 -m http.server 8000
# or Node:
npx serve .

# Open in browser:
# http://localhost:8000
```

**First run:** the Depth Anything V2 model (~50 MB) is downloaded once from the HuggingFace CDN. Subsequent runs load instantly from the browser cache.

---

## 📁 Repository structure

```
parallax-3D/
├── index.html       # The whole app (HTML + CSS + JS inline)
├── thumbnail.png    # Portal preview (1000×1000)
└── README.md        # This file
```

---

## ⚙️ Compatibility

| Browser | WebGPU | WASM fallback | Webcam |
|---|---|---|---|
| Chrome / Edge 113+ | ✅ | ✅ | ✅ |
| Safari 17+ | ⚠️ | ✅ | ✅ |
| Firefox | ❌ | ✅ | ✅ |

WebGPU makes inference ~5–10× faster than WASM, but the app works fine without it.

---

## 📚 Assignment context

**Course:** B-MSAP (Multimedia Systems and Applications in Practice)
**Part:** B — Vibecoding Web App
**Author:** Kristína Adazhii
**Medium:** Image → 3D
**Topic:** AI 3D-space reconstruction from a single photograph using monocular depth estimation

### Assignment requirements satisfied
- ✅ 100 % client-side, no backend
- ✅ « Späť na portál » back button in the corner
- ✅ `thumbnail.png` exactly 1000 × 1000 px
- ✅ WOW effect — real in-browser AI inference + 3D anaglyph + live webcam
- ✅ Processes a student medium (Image)
- ✅ Uses only allowed standards (WebGL, WebAssembly, Canvas)

---

## 📄 License

Creative Commons **CC BY 4.0** — free for educational use.

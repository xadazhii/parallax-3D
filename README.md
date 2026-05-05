# Parallax — Multimedia → 3D Space

> Semester project · **B-MSAP**, Part B (Vibecoding Web App)

An interactive web app that turns **photos, audio, video, text, and PLY files** into walkable 3D point-cloud spaces. A single WebGL2 engine renders five different media pipelines: AI monocular depth (Depth Anything V2 via Transformers.js), custom radix-2 FFT spectrograms, video frame capture, text-to-shape, and direct PLY parsing.

🌐 **Live demo:** [xadazhii.github.io/parallax-3D](https://xadazhii.github.io/parallax-3D/)

---

## ✨ Features

### 5 input modes (one renderer)

| Mode | Pipeline | Output |
|---|---|---|
| 📷 **Photo** | Depth Anything V2 (ONNX, ~50 MB) → bilateral smoothing → point cloud | RGB 3D scene |
| 🎵 **Audio** | Decode → custom radix-2 FFT (window=512, hop≈220) → log-magnitude spectrogram | 3D height-map: X=time, Y=frequency, Z=amplitude |
| 🎬 **Video** | `<video>` preview + scrub slider → grab frame to canvas → Photo pipeline | RGB 3D scene per frame |
| 📝 **Text** | Render to canvas → pixel mask → points + shader sine/cosine wave | Animated 3D text |
| 🧊 **.PLY** | ASCII parser → normalize bounds → direct upload to GPU | 3D point cloud (compatible with Part A Gaussian Splatting exports) |

### Live capture
- 🎥 **Live Webcam Mode** — real-time depth estimation from camera
- 🎤 **Live Microphone Mode** — realtime FFT from mic, streaming spectrogram

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
┌──────────────────────────────────────────────────────────────┐
│  YOUR BROWSER (Chrome / Safari / Firefox)                    │
│                                                               │
│   📷 Photo ──→  Depth Anything V2 (ONNX, WebGPU/WASM)        │
│   🎵 Audio ──→  Custom FFT analyzer (Web Audio API)          │
│   🎬 Video ──→  Frame capture → Depth Anything V2            │
│   📝 Text  ──→  Canvas raster → pixel mask                   │
│   🧊 .PLY  ──→  ASCII parser                                 │
│                          │                                    │
│                          ↓                                    │
│                ┌─────────────────────┐                        │
│                │ Unified cloud format│                        │
│                │ {pos, col, depth, n}│                        │
│                └─────────┬───────────┘                        │
│                          ↓                                    │
│                Custom WebGL2 point-cloud renderer             │
│                  (orbital camera, anaglyph, wave shader)      │
└──────────────────────────────────────────────────────────────┘
```

**No backend, no server.** Your photo never leaves the browser.

---

## 🛠 Tech stack

| Layer | Technology |
|---|---|
| **AI / ML** | [Transformers.js](https://github.com/huggingface/transformers.js) v3 + [Depth Anything V2](https://depth-anything-v2.github.io/) |
| **Audio** | Web Audio API (`AudioContext`, `AnalyserNode`) + custom radix-2 FFT |
| **Video** | `<video>` element + canvas frame grab |
| **Text** | Canvas 2D rasterization → pixel mask |
| **3D files** | ASCII PLY parser |
| **GPU acceleration** | WebGPU (with WebAssembly SIMD fallback) |
| **3D rendering** | Custom WebGL2 point-cloud engine (custom shaders, anaglyph, wave) |
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
**Media covered:** Image · Audio · Video · Text · 3D — all five categories from the assignment brief
**Topic:** Multimedia → 3D point-cloud reconstruction (AI depth estimation, FFT spectrogram, video frame capture, text rasterization, PLY import)

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

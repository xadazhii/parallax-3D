# Parallax — AI Hĺbka z Fotografie do 3D priestoru

> Semestrálna práca · **B-MSAP**, časť B (Vibecoding Web App)

Interaktívna webová aplikácia, ktorá z **jedinej fotografie** vytvorí prechádzateľný 3D priestor. Neurónová sieť **Depth Anything V2** odhadne hĺbkovú mapu obrázka priamo v prehliadači, zo zdrojových pixelov sa vyrenderuje 3D mračno bodov v custom WebGL2 enginu.

🌐 **Live demo:** [xadazhii.github.io/parallax-3D](https://xadazhii.github.io/parallax-3D/)

---

## ✨ Funkcie

### Vstup
- 📁 **Drag & drop** ľubovoľnej fotografie (JPG/PNG/WEBP)
- 📋 **Paste z clipboardu** (Ctrl/Cmd + V)
- 🖼 **4 ukážkové fotky** (oceán, hory, ulica, les)
- 🎥 **Webcam Live Mode** — odhadovanie hĺbky v reálnom čase z kamery

### Vizualizácia
- 🎨 **3 farebné režimy** — pôvodná RGB · gradient hĺbky · normálové vektory
- 👁 **Anaglyph 3D** (červeno-modré okuliare!)
- ⟳ **Auto-rotácia** alebo **Wiggle** efekt (Facebook 3D Photo)
- 🎚 Slidre pre **silu hĺbky**, **veľkosť bodov**, **odrezanie pozadia**
- 🖼 Mini-náhľad **hĺbkovej mapy** v rohu obrazovky

### Export
- ↓ **PNG screenshot**
- ↓ **`.PLY` súbor** (otvorí sa v MeshLab, Blender, CloudCompare...)

### Ovládanie
- 🖱 **Ľavé tlačidlo** — orbitálna rotácia
- 🖱 **Pravé tlačidlo** — posun (pan)
- 🎡 **Scroll** — zoom
- 📱 **Touch + pinch zoom** na mobile

---

## 🧠 Ako to funguje

```
┌──────────────────────────────────────────────────────┐
│  TVOJ PREHLIADAČ (Chrome / Safari / Firefox)         │
│                                                       │
│  Fotografia ──→ Depth Anything V2 (ONNX, ~50 MB)     │
│                  │                                    │
│                  ↓ WebGPU / WASM inference            │
│                                                       │
│              Hĺbková mapa (Float32Array)              │
│                  │                                    │
│                  ↓ + originálne RGB pixely            │
│                                                       │
│              3D Point Cloud (~220k bodov)             │
│                  │                                    │
│                  ↓ Custom WebGL2 renderer             │
│                                                       │
│              Interaktívna 3D scéna                    │
└──────────────────────────────────────────────────────┘
```

**Žiadny backend, žiadny server.** Fotografia neopustí tvoj prehliadač.

---

## 🛠 Technologický stack

| Vrstva | Technológia |
|---|---|
| **AI / ML** | [Transformers.js](https://github.com/huggingface/transformers.js) v3 + [Depth Anything V2](https://depth-anything-v2.github.io/) |
| **GPU akcelerácia** | WebGPU (s fallbackom na WebAssembly SIMD) |
| **3D rendering** | Custom WebGL2 point-cloud engine (vlastné shadery) |
| **Idle pozadie** | Canvas 2D parallax starfield |
| **Ostatné** | Vanilla JS, žiadny build step, jeden HTML súbor |

### Vlastné implementácie
- **Bilateral depth smoothing** — 3×3 edge-aware filter na elimináciu artefaktov
- **Real-time background cutoff** v vertex shaderi
- **Anaglyph rendering** s color masking a IPD eye-offset
- **Normal-vector estimation** z gradientu hĺbkovej mapy
- **Counting-style point cloud builder** s adaptívnym strideom

---

## 🚀 Lokálne spustenie

ES moduly vyžadujú HTTP server (nefungujú cez `file://`):

```bash
# Klonovanie
git clone https://github.com/xadazhii/parallax-3D.git
cd parallax-3D

# Spustenie cez Python
python3 -m http.server 8000
# alebo Node:
npx serve .

# Otvor v prehliadači:
# http://localhost:8000
```

**Prvý spustenie:** stiahne sa Depth Anything V2 model (~50 MB) z HuggingFace CDN. Ďalšie spustenia bežia okamžite zo cache prehliadača.

---

## 📁 Štruktúra repozitára

```
parallax-3D/
├── index.html       # Celá aplikácia (HTML + CSS + JS inline)
├── thumbnail.png    # Náhľad pre portál (1000×1000)
└── README.md        # Tento súbor
```

---

## ⚙️ Kompatibilita

| Prehliadač | WebGPU | WASM fallback | Webcam |
|---|---|---|---|
| Chrome / Edge 113+ | ✅ | ✅ | ✅ |
| Safari 17+ | ⚠️ | ✅ | ✅ |
| Firefox | ❌ | ✅ | ✅ |

WebGPU dáva inferenciu ~5–10× rýchlejšiu ako WASM, ale aplikácia funguje aj bez nej.

---

## 📚 Kontext zadania

**Predmet:** B-MSAP (Multimediálne systémy a aplikácie v praxi)
**Časť:** B — Vibecoding Web App
**Autor:** Kristína Adazhii
**Médium:** Image → 3D
**Téma:** AI rekonštrukcia 3D priestoru z jednej fotografie pomocou monoculárneho odhadu hĺbky

### Splnené požiadavky zadania
- ✅ 100 % client-side, žiadny backend
- ✅ Tlačidlo *« Späť na portál »* v rohu
- ✅ `thumbnail.png` presne 1000 × 1000 px
- ✅ WOW efekt — reálna AI inferencia v prehliadači + 3D anaglyph + live webcam
- ✅ Spracováva študentské médium (Image)
- ✅ Použité štandardy z umožnených technológií (WebGL, WebAssembly, Canvas)

---

## 📄 Licencia

Creative Commons **CC BY 4.0** — voľne dostupné pre vzdelávacie účely.

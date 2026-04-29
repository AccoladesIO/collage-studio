# Collage Studio

A lightweight, browser-based photo collage builder. Upload images, pick a layout, choose a background, and download your finished collage as a PNG — no server, no sign-up, no dependencies beyond a single CDN script. Installable as a PWA for offline use.

![HTML](https://img.shields.io/badge/HTML-5-e34f26?logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS-3-1572b6?logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-f7df1e?logo=javascript&logoColor=black)
![PWA](https://img.shields.io/badge/PWA-Installable-5a0fc8?logo=pwa&logoColor=white)

---

## Features

**10 grid layouts** covering a range of collage styles:

| Layout | Cells | Description |
|--------|-------|-------------|
| Diamond | 7 | Rotated squares in a 3–4 zigzag pattern (16:9) |
| Classic Grid | 9 | Standard 3×3 equal cells |
| Hero Left | 9 | Large feature image on the left, smaller cells right and bottom |
| Focus Center | 11 | 4×3 grid with a circular center cell |
| Magazine | 12 | Editorial-style grid with a large circle element |
| Mosaic | 11 | Irregular PicCollage-style layout with spanning cells |
| Asymmetric | 8 | Mixed-size cells with a large center feature |
| Wide Strip | 7 | Panoramic center row flanked by standard rows |
| Duo | 6 | Two-row feature layout |
| Quad | 4 | Simple 2×2 grid |

**No-stretch image handling** — images are always center-cropped (`object-fit: cover` with `object-position: center`) so they fill their cell without any distortion. Edges may be trimmed but the subject stays centered. Diamond cells use `background-image` with `background-size: cover` for the same effect through the rotation transform.

**Background palette** — 12 presets including solid colors (white through black) and gradients (blush, sky, sage, peach, lavender, midnight, wine).

**Grid width control** — a slider from 50 % to 100 % that sizes the collage within the background, so the remaining margin shows your chosen color or gradient in the final export.

**Watermark** — toggle on, type your text, and pick a position (bottom-right, bottom-left, top-right, top-left, or center). The watermark renders as a semi-transparent overlay and is included in the exported PNG.

**Perfect circles** — circle cells use `aspect-ratio: 1` constrained by `max-width` and `max-height`, so they stay perfectly round regardless of the grid cell's proportions.

**PWA / Installable** — includes a web app manifest, service worker for offline caching, and an in-app install prompt. Works on Chrome, Edge, and other Chromium browsers on desktop and mobile.

---

## Getting Started

1. **Download or clone** the repository.
2. Serve the files from any HTTP server (required for PWA / service worker):
   ```bash
   # Python
   python3 -m http.server 8000

   # Node
   npx serve .
   ```
3. Open `http://localhost:8000/index.html` in your browser.
4. To install as an app, click the install banner that appears at the bottom, or use your browser's "Install" option in the address bar.

> **Note:** Opening `index.html` directly via `file://` works for the collage itself, but the PWA install and offline features require an HTTP server.

---

## Usage

1. **Pick a layout** from the horizontal scroll bar at the top. Each thumbnail shows the grid structure and a badge with the number of images required.
2. **Choose a background** by clicking a color swatch.
3. **Adjust grid width** with the slider. At 80 %, for example, 20 % of the canvas is filled by your background color.
4. **Upload images** in one of two ways:
   - Click an individual empty cell to place a specific image there.
   - Click the **Upload Images** button to bulk-fill the next available empty cells in order.
5. **Remove an image** by hovering over a filled cell and clicking the ✕ button.
6. **Add a watermark** (optional) — toggle the watermark switch on, type your text, and choose a corner or center position.
7. **Download** your collage as a 2× resolution PNG by clicking **Download Collage** (enabled once every cell is filled).

---

## Project Structure

```
collage-studio/
├── index.html      ← entire application (HTML + CSS + JS)
├── manifest.json     ← PWA web app manifest
├── sw.js             ← service worker for offline caching
└── README.md
```

### External dependency

The only external resource is [html2canvas](https://html2canvas.hertzen.com/) loaded from cdnjs, used to rasterize the collage DOM node into a downloadable PNG canvas.

```
https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js
```

---

## How It Works

### Image rendering — no stretch

Every image cell uses absolute positioning with `object-fit: cover` and `object-position: center center`. This ensures the image scales to fill the cell completely, cropping any overflow from the edges equally, and never distorts the aspect ratio.

For diamond cells (rotated 45°), images are rendered as a `div` with `background-image`, `background-size: cover`, and `background-position: center`. The div is counter-rotated and scaled to 142 % so it fully covers the visible diamond area through the rotation — again with no stretching.

### Layout engine

Each layout is a plain JavaScript object with a `type` property:

- **`grid`** layouts define CSS `grid-template-columns`, `grid-template-rows`, and an array of `cells` with `col`, `row`, and an optional `circle` flag.
- **`diamond`** layouts define an array of diamonds with `x`, `y`, and `size` percentages. Cells are absolutely positioned and rotated 45° via CSS `transform`.

### Watermark

The watermark is a `position: absolute` overlay inside `#download-wrapper`, positioned via CSS classes (`pos-br`, `pos-tl`, etc.). Since it lives inside the wrapper, `html2canvas` captures it in the export automatically.

### Export pipeline

1. The wrapper's border and border-radius are temporarily removed.
2. `html2canvas` captures `#download-wrapper` at 2× scale with `backgroundColor: null` to preserve gradients.
3. The canvas is converted to a `data:image/png` URL and triggered as a download.

### PWA

- `manifest.json` declares the app name, icons, theme color, and standalone display mode.
- `sw.js` caches the HTML, manifest, html2canvas script, and fonts on install, then serves from cache on fetch (cache-first strategy).
- The app listens for the `beforeinstallprompt` event and shows a floating install banner.

---

## Customization

### Adding a new grid layout

Add an entry to the `LAYOUTS` array in the `<script>` section:

```js
{
  name: 'My Layout',
  type: 'grid',
  cols: 'repeat(3, 1fr)',
  rows: '2fr 1fr 2fr',
  cells: [
    { col: '1/2', row: '1/2' },
    { col: '2/4', row: '1/2', circle: true },
    // ...
  ]
}
```

### Adding a new background color

Add an entry to the `COLORS` array:

```js
{ name: 'Ocean', css: 'linear-gradient(135deg, #0077b6 0%, #00b4d8 100%)' }
```

Solid colors work too:

```js
{ name: 'Coral', css: '#ff6b6b' }
```

---

## Browser Support

Tested on the latest versions of Chrome, Firefox, Safari, and Edge. Requires support for CSS Grid, `aspect-ratio`, and ES6+. PWA install is supported on Chromium-based browsers.

---

## Credits

Built with ❤️ by Accolades

---

## License

MIT — use it however you like.
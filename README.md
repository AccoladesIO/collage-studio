# Collage Studio

A lightweight, browser-based photo collage builder. Upload images, pick a layout, choose a background, and download your finished collage as a PNG — no server, no sign-up, no dependencies beyond a single CDN script.

![HTML](https://img.shields.io/badge/HTML-5-e34f26?logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS-3-1572b6?logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-f7df1e?logo=javascript&logoColor=black)

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

**Background palette** — 12 presets including solid colors (white through black) and gradients (blush, sky, sage, peach, lavender, midnight, wine).

**Grid width control** — a slider from 50 % to 100 % that sizes the collage within the background, so the remaining margin shows your chosen color or gradient in the final export.

**Perfect circles** — circle cells use `aspect-ratio: 1` constrained by `max-width` and `max-height`, so they stay perfectly round regardless of the grid cell's proportions.

**Single-file architecture** — everything (HTML, CSS, JS) lives in one `collage.html` file. Drop it anywhere and open it in a browser.

---

## Getting Started

1. **Download or clone** the repository.
2. Open `collage.html` in any modern browser (Chrome, Firefox, Safari, Edge).
3. That's it — no build step, no `npm install`.

---

## Usage

1. **Pick a layout** from the horizontal scroll bar at the top. Each thumbnail shows the grid structure and a badge with the number of images required.
2. **Choose a background** by clicking a color swatch. Solid colors and gradients are available.
3. **Adjust grid width** with the slider. At 80 %, for example, 20 % of the canvas is filled by your background color.
4. **Upload images** in one of two ways:
   - Click an individual empty cell to place a specific image there.
   - Click the **Upload Images** button to bulk-fill the next available empty cells in order.
5. **Remove an image** by hovering over a filled cell and clicking the ✕ button.
6. **Download** your collage as a 2× resolution PNG by clicking **Download Collage** (enabled once every cell is filled).

---

## Project Structure

```
collage-studio/
├── collage.html      ← entire application (HTML + CSS + JS)
└── README.md
```

### External dependency

The only external resource is [html2canvas](https://html2canvas.hertzen.com/) loaded from cdnjs, used to rasterize the collage DOM node into a downloadable PNG canvas.

```
https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js
```

---

## How It Works

### Layout engine

Each layout is a plain JavaScript object with a `type` property:

- **`grid`** layouts define `cols` (CSS `grid-template-columns`), `rows` (`grid-template-rows`), and an array of `cells`, each with `col`, `row`, and an optional `circle` flag.
- **`diamond`** layouts define an array of `diamonds` with `x`, `y`, and `size` percentages. Cells are absolutely positioned and rotated 45° via CSS `transform`, with inner images counter-rotated and scaled to fill the diamond shape.

### Circle rendering

Circle cells wrap their content in a `.circle-mask` div:

```css
.circle-mask {
  aspect-ratio: 1 / 1;
  max-width: 100%;
  max-height: 100%;
  border-radius: 50%;
  overflow: hidden;
}
```

This guarantees a perfect circle inscribed within any rectangular grid cell.

### Export pipeline

1. The collage container's border and border-radius are temporarily removed.
2. `html2canvas` captures `#download-wrapper` (which includes the background) at 2× scale with `backgroundColor: null` to preserve gradients.
3. The canvas is converted to a `data:image/png` URL and triggered as a download.

---

## Customization

### Adding a new grid layout

Add an entry to the `LAYOUTS` array in the `<script>` section:

```js
{
  name: 'My Layout',
  type: 'grid',
  cols: 'repeat(3, 1fr)',     // CSS grid-template-columns
  rows: '2fr 1fr 2fr',        // CSS grid-template-rows
  cells: [
    { col: '1/2', row: '1/2' },
    { col: '2/4', row: '1/2', circle: true },  // circle cell
    // ... more cells
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

Tested on the latest versions of Chrome, Firefox, Safari, and Edge. Requires support for CSS Grid, `aspect-ratio`, and ES6+.

---

## License

MIT — use it however you like.
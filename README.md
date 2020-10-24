# BrogueTiles

Tools to generate font/graphic tiles for Brogue CE.

## Fonts

Make sure you have installed the following fonts:

- Noto Sans
- Monaco
- Consolas
- Cambria
- Segoe UI Symbol

Then open `canvas_fonts/generate font assets.htm` in Chrome on Linux.

Click on one of the suggested size lists. The page will be busy for about a minute.

Save (right-click > Save) the generated images as `font-1.png` through `font-15.png`

### How it works

Glyphs are selected from the best font, and their sizes and positions sometimes adjusted for best results.

Then each glyph is drawn onto the canvas at several possible non-integer coordinates, looking for the ones that give the sharpest output.

## Graphics

To avoid "cross-origin" errors, you will need to open the html page through an http server,
for example with one of:

- `npx serve`
- `python -m http.server`

Open `vector_graphics/generate tile assets.htm` in Chrome.

Click on one of the suggested size lists. The page will be busy for several minutes; be patient.

The "blurry, fast" options disable edge alignment; it will run much faster but the output will not be as sharp, especially on small sizes.

Save (right-click > Save) the generated images as `tiles-1.png` through `tiles-15.png`

### How it works

Brogue CE's graphic tiles, originally by Oryx, have been re-drawn in Inkscape, adding some fine details and taking care to align edges as much as possible with a 256x232 grid (which is the size of the original `tiles-10.png`). Then the SVG was exported as PNG at size 2048x1856.

The JS code processes each tile separately:

- Decide if it must stretch the tile to fill the space (walls, doors) or preserve aspect ratio (monsters, objects).
- Try several possible alignments:
    - Middle row and middle column can be shifted by as much as 1 pixel, in 0.1 pixel increment.
    - Second and penultimate pixel rows and columns can be shifted by as much as 0.5 pixel.
- Downscaling is performed in linear color space, using the box sampling method.
- Tiles less 20 pixel wide are further sharpened with a simple 3x3 kernel.
- Wall tops are filled with an integer number of sine waves approximately 4 pixels apart horizontally and 4 pixels vertically.
- The floor tile is generated with blue noise produced by doing Floyd-Steinberg error diffusion on a noisy background. A post-processing step adds pixels to fill any gaps that remain.

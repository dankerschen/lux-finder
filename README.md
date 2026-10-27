# Luxembourg quadrant finder

Find Luxembourg towns and communes on a quadrant map.

Type the name of a town or commune and the app tells you which cell of the map it is in (for example **D9**), highlights that cell and drops a pin on the map. If the place is close to a grid line, it says so and shows the neighbouring cell too.

It is a single HTML file with no dependencies. The map is embedded in the file, so it works offline and on a phone or tablet.

## Using it

1. Download and open `index.html` in any browser.
   OR: use the online version [https://dankerschen.github.io/lux-finder/](https://dankerschen.github.io/lux-finder/)
3. Type a town or commune, then press Enter or tap **Find**.
4. Read the cell name, and look at the pin and the highlighted cell on the map.

Things worth knowing:

- **Spelling is forgiving.** Accents are ignored (`Ettelbruck` finds Ettelbréck), small typos are tolerated (`Diekirsh`), and many places also answer to their Luxembourgish, German or short names (`Lëtzebuerg`, `Luxemburg`, `Esch`).
- **Ambiguous names** show the best match first and list the others under "Also matches".
- **Border cases** are flagged in amber. The pin is placed in the cell it falls in, and the cell(s) next to the line are outlined with dashes.
- **Recent** keeps the last few searches. **Clear pin** resets everything.

## The map

The map is a grey outline of Luxembourg on a grid of 7 columns (A to G) and 12 rows (1 to 12). It contains no other labels, so the highlighted cell and the pin are what you read off it.

## How it works

The app turns a latitude and longitude into a position on the map image, then into a grid cell.

1. **Place to pixel.** The map is treated as a simple projection with the east-west scale adjusted for the latitude of Luxembourg. The scale and offset were fitted to the country's extreme points (north, south, east and west) against the edges of the grey outline in the image. As a check, the fitted vertical-to-horizontal scale ratio matches what the geometry predicts to within about 0.3 %.
2. **Pixel to cell.** The grid lines were measured from the image. Each cell is about 54.6 px wide and 44.4 px tall on the 462 x 600 pixel map.
3. **Border cases.** A place counts as a border case when it is within 7 px of a vertical line or 6 px of a horizontal line. That is roughly 1 km on the ground.
4. **Search.** Names and aliases are lower-cased, stripped of accents and compared by exact match, prefix, word prefix, substring and, for longer queries, a small edit distance.

## Accuracy

Pins are placed from each place's latitude and longitude, not by hand. The coordinates were entered from general knowledge rather than copied from an official gazetteer, so a pin can be off by a few kilometres. Treat anything flagged as a border case as "one of these two cells", and spot-check a few places against a trusted source before relying on the result.

## Customising

Everything lives in `luxembourg-quadrants.html`.

**Add or fix a place.** Find the `TOWNS` list in the second `<script>` and add or edit an entry:

```js
{"n": "Echternach", "la": 49.8114, "lo": 6.4189, "a": ["Iechternach"], "t": ""}
```

| Field | Meaning |
| --- | --- |
| `n` | Name shown in the result |
| `la`, `lo` | Latitude and longitude in decimal degrees |
| `a` | Other names the search should accept |
| `t` | Optional note shown under the result, such as "in the commune of ..." |

**Change how strict the border warning is.** In the first `<script>` (`id="core"`), edit `TOLX` and `TOLY`. Smaller numbers flag fewer places.

**Use a different map.** Replace the `src` of the `<img>` with your own image (a base64 `data:` URL keeps the file self-contained), then update `CAL` (how latitude and longitude map to pixels) and `GRID` (where the grid lines are) in the same script.

## Publishing on GitHub Pages

Rename `luxembourg-quadrants.html` to `index.html`, push it to a repository and enable Pages for the main branch. No build step is needed.

## How this was made

The app was built in a conversation with Claude, an AI assistant made by Anthropic. The starting point was a screenshot of a Luxembourg map with a grid drawn on it, plus the request to find the quadrant for a town and put a pin on the map.

1. **Measuring the map.** The positions of the grid lines and the extreme points of the grey outline (northernmost, southernmost, easternmost, westernmost) were read from the image's pixels.
2. **Calibrating.** Those four extreme points were matched to the real extreme points of Luxembourg to fit latitude and longitude to pixel positions. The fitted scale agrees with the expected east-west shrinkage at this latitude to within about 0.3 %.
3. **Writing the place list.** The 122 places, with coordinates and alternative names, were written from the assistant's general knowledge. A script checked that every one lands on or right next to the grey outline. They were not compared with an official gazetteer.
4. **Building and testing.** The search and quadrant logic was run on sample names and compared with the earlier hand-made answers. The page was then loaded in a headless browser at phone, tablet and desktop widths to check the layout and to confirm there were no script errors.

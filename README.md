# Pin Picker

A single-page tool for getting pins out of a Pinterest board and back in front of me, one at a time.

**Live:** https://datadudecm.github.io/pinpicker/pinterest-pin-picker.html

It does two jobs:

1. **Scrape.** A bookmarklet collects every pin on a board into a JSON file.
2. **Pick.** The page loads that JSON and shows a random pin (image, description and a link back to Pinterest), which I use as a sketch subject.

No server, no Pinterest API, no login beyond being signed in to Pinterest in the browser.

## Using it

### 1. Scrape a board
1. Open the live page and drag the **📌 Pin Scraper** button to the browser's bookmarks bar.
2. Go to the Pinterest board.
3. Click **Pin Scraper**. An alert confirms it's running.
4. Scroll slowly to the bottom of the board, so every pin loads. Pinterest only renders pins near the screen, and the scraper only sees what's rendered.
5. Click **Pin Scraper** again. It downloads `pins.json` and reports how many pins it collected.

Rename the file after the board (e.g. `portrait-art-pins.json`) so exports don't overwrite each other.

### 2. Pick a pin
Drop a JSON file on the page (or browse for it) and press **Random Pin**.

When the page is served from a web server (GitHub Pages included), it first tries to load `pins.json` from the same folder automatically. Other file names have to be dropped in by hand.

## JSON format
An array of pins, in the order the scraper saw them (top of the board first):

```json
[
  {
    "id": "176203404168834826",
    "url": "https://www.pinterest.com/pin/176203404168834826/",
    "image": "https://i.pinimg.com/736x/78/d6/54/78d654736bc7f974587c772736851eca.jpg",
    "description": "This may contain: a drawing of a man with a beard and mustache"
  }
]
```

- `image` is rewritten to the 736 px size; the picker falls back to 474 px if that fails.
- `description` is the image's alt text, usually Pinterest's auto-generated "This may contain: …", not a caption I wrote.

## Files

| File | What it is |
|---|---|
| `pinterest-pin-picker.html` | The whole tool: page, bookmarklet and picker, no dependencies beyond Google Fonts |
| `pins.json` | Export of my sketch-subjects board (auto-loaded by the live page) |
| `portrait-art-pins.json` | Export of my Portrait Art board (259 pins, Nov 2026) |

## Notes and quirks

- **Weighted toward the end of the file.** The picker uses `Math.pow(Math.random(), 0.3)`, which favors pins near the end of the array, the bottom of the board. Set `skew` to `1` in `pickRandom()` for a uniform pick.
- **Board name is hard-coded** in the page title, subtitle and step 1 instructions (sketch-subjects). The scraper itself works on any board.
- **It depends on Pinterest's page structure** (links containing `/pin/` with an `img` from `pinimg.com`). If Pinterest changes its markup, the scraper may collect nothing; the alert will say "No pins found".
- Image links point at Pinterest's CDN, so a pin removed from Pinterest will stop loading.

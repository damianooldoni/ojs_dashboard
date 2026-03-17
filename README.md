# OJS Dashboard

Interactive dashboard using Observable JavaScript (OJS) in Quarto for visualizing species data across polygons.

## Features

- **Species Selection with Autocomplete**: Text input with autocomplete to filter data by species - perfect for long species lists
- **Interactive Map**: Leaflet map displaying polygons with species data
- **Plot Visualization**: Click on any polygon to view detailed plots in a popup
- **Dark Theme**: Modern dark theme for better visual aesthetics

## Data Structure

The dashboard works with two data files:

1. **species_polygons.csv**: Tabular data with columns:
   - `polygons_id`: Polygon identifiers
   - `species`: Species names
   
   **Note**: The combination of `polygons_id` and `species` is unique. Multiple species can exist in the same polygon, and each unique species/polygon combination has a corresponding PNG plot in `docs/assets/plots/`.

2. **polygons.geojson**: Geospatial data with polygon geometries and `polygon_id` properties

### Example Data

The sample dataset includes 5 polygons with varying species compositions:
- **poly_1**: Oak, Pine (2 species)
- **poly_2**: Pine, Birch (2 species)
- **poly_3**: Oak, Pine, Birch (3 species)
- **poly_4**: Birch, Oak (2 species)
- **poly_5**: Pine (1 species)

## Usage

### Prerequisites

Install [Quarto](https://quarto.org/docs/get-started/) on your system.

### Running the Dashboard

1. Clone the repository
2. Navigate to the project directory
3. Run:
   ```bash
   quarto preview index.qmd
   ```
4. Open your browser to view the dashboard

### Building the Site

To build the static site:
```bash
quarto render
```

The output will be in the `docs/` directory.

## How It Works

1. Type a species name in the autocomplete text input (left panel) - suggestions will appear as you type
2. The map displays all polygons, highlighting those containing the selected species in blue
3. Hover over any polygon to see all species it contains and the count
4. Click on any highlighted (blue) polygon to view the plot for the selected species in a popup
5. Gray polygons do not contain the selected species and are not clickable

**Important**: When you click on a polygon, the popup shows the plot data specifically for the selected species in that polygon. Since polygons can contain multiple species, each species/polygon combination has its own unique plot.

## Plot Images (PNG)

Popup plots are rendered from pre-generated PNG images stored under `docs/assets/plots/`.
When a user clicks a highlighted polygon, the dashboard constructs the image path from the
selected species name and the polygon identifier, then shows the PNG inside the popup.
If the PNG cannot be loaded the popup shows "No plot available for this selection.".

### Where to place PNGs

```
docs/
└── assets/
    └── plots/
        ├── oak__poly_1.png
        ├── pine__poly_1.png
        └── ...
```

Because `docs/` is the Quarto output directory published to GitHub Pages, any file placed
inside it is served at the site root.  At build time `quarto render` copies additional
resource files into `docs/` as needed, but the `assets/plots/` sub-folder is meant to be
**committed directly** so that GitHub Pages serves the PNGs alongside the built HTML.

### Filename convention

| Component | Rule | Example |
|-----------|------|---------|
| species slug | NFD-normalise → strip diacritics → lowercase; runs of non-alphanumeric chars → `_`; trim leading/trailing `_` | `"Oak"` → `oak`, `"Pinus sylvestris"` → `pinus_sylvestris`, `"Séneçon"` → `senecon` |
| separator | double underscore `__` | — |
| polygon ID | keep only `[a-zA-Z0-9_-]`, replace anything else with `_` | `poly_1` → `poly_1` |
| extension | `.png` | — |

Full pattern: **`{species_slug}__{polygon_id}.png`**

Examples:
- `oak__poly_1.png` — Oak in polygon poly\_1
- `pinus_sylvestris__poly_3.png` — *Pinus sylvestris* in polygon poly\_3
- `birch__poly_2.png` — Birch in polygon poly\_2

### How popups resolve an image

1. The JavaScript helper `slugify(str)` converts `species_name` to the slug form (NFD normalisation → diacritic stripping → lowercase → replace non-alphanumeric runs with `_`).
2. The polygon ID is sanitised with `sanitizeId(id)` (only `[a-zA-Z0-9_-]` kept).
3. The path `assets/plots/<slug>__<sanitized_id>.png` is set as the `<img src>`.
4. If the browser can load the image it is displayed (max-width: 300 px).
5. If loading fails (`onerror`) the popup shows "No plot available for this selection.".

## Technologies Used

- [Quarto](https://quarto.org/): Document publishing system
- [Observable JavaScript (OJS)](https://quarto.org/docs/interactive/ojs/): Reactive JavaScript runtime
- [Leaflet](https://leafletjs.com/): Interactive maps
- [D3.js](https://d3js.org/): Data manipulation

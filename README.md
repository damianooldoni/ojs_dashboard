# OJS Dashboard

Interactive dashboard using Observable JavaScript (OJS) in Quarto for visualizing species data across polygons.

## Features

- **Species Selection**: Dropdown menu to filter data by species
- **Interactive Map**: Leaflet map displaying polygons with species data
- **Plot Visualization**: Click on any polygon to view detailed plots in a popup

## Data Structure

The dashboard works with two data files:

1. **species_plots.csv**: Tabular data with columns:
   - `polygons_id`: Polygon identifiers
   - `species`: Species names
   - `plot`: Plot data (JSON format)

2. **polygons.geojson**: Geospatial data with polygon geometries and `polygon_id` properties

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

1. Select a species from the dropdown menu (left panel)
2. The map displays all polygons, highlighting those containing the selected species
3. Click on any highlighted (blue) polygon to view its plot in a popup
4. Gray polygons contain other species and are not clickable

## Technologies Used

- [Quarto](https://quarto.org/): Document publishing system
- [Observable JavaScript (OJS)](https://quarto.org/docs/interactive/ojs/): Reactive JavaScript runtime
- [Leaflet](https://leafletjs.com/): Interactive maps
- [Observable Plot](https://observablehq.com/plot/): Data visualization
- [D3.js](https://d3js.org/): Data manipulation

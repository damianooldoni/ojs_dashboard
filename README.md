# OJS Dashboard

Interactive dashboard using Observable JavaScript (OJS) in Quarto for visualizing species data across polygons.

## Features

- **Species Selection**: Dropdown menu to filter data by species
- **Interactive Map**: Leaflet map displaying polygons with species data
- **Plot Visualization**: Click on any polygon to view detailed plots in a popup
- **Dark Theme**: Modern dark theme for better visual aesthetics

## Data Structure

The dashboard works with two data files:

1. **species_plots.csv**: Tabular data with columns:
   - `polygons_id`: Polygon identifiers
   - `species`: Species names
   - `plot`: Plot data (JSON format)
   
   **Note**: The combination of `polygons_id` and `species` is unique. Multiple species can exist in the same polygon, and each unique species/polygon combination has its own associated plot.

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

1. Select a species from the dropdown menu (left panel)
2. The map displays all polygons, highlighting those containing the selected species in blue
3. Hover over any polygon to see all species it contains and the count
4. Click on any highlighted (blue) polygon to view the plot for the selected species in a popup
5. Gray polygons do not contain the selected species and are not clickable

**Important**: When you click on a polygon, the popup shows the plot data specifically for the selected species in that polygon. Since polygons can contain multiple species, each species/polygon combination has its own unique plot.

## Technologies Used

- [Quarto](https://quarto.org/): Document publishing system
- [Observable JavaScript (OJS)](https://quarto.org/docs/interactive/ojs/): Reactive JavaScript runtime
- [Leaflet](https://leafletjs.com/): Interactive maps
- [Observable Plot](https://observablehq.com/plot/): Data visualization
- [D3.js](https://d3js.org/): Data manipulation

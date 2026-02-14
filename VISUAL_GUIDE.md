# OJS Dashboard - Visual Guide

## Dashboard Layout

The OJS Dashboard provides an interactive interface with the following components:

### Layout Structure

```
┌─────────────────────────────────────────────────────────────────────┐
│                          OJS Dashboard                               │
├─────────────────┬───────────────────────────────────────────────────┤
│                 │                                                     │
│ Select Species  │          Map - Click on a polygon to view plot     │
│                 │                                                     │
│ ┌─────────────┐ │   ┌─────────────────────────────────────────┐     │
│ │ Dropdown    │ │   │                                         │     │
│ │ ▼ Oak       │ │   │         Interactive Leaflet Map        │     │
│ └─────────────┘ │   │                                         │     │
│                 │   │   • Blue polygons = Selected species    │     │
│ Filtered        │   │   • Gray polygons = Other species       │     │
│ Polygons:       │   │                                         │     │
│                 │   │   Click blue polygon → Plot popup      │     │
│ Showing 2       │   │                                         │     │
│ polygon(s) for  │   │                                         │     │
│ Oak             │   │                                         │     │
│                 │   │                                         │     │
│                 │   └─────────────────────────────────────────┘     │
│                 │                                                     │
└─────────────────┴───────────────────────────────────────────────────┘
```

## Features

### 1. Species Selector (Left Panel)
- Dropdown menu with all unique species from the dataset
- Dynamically filters the map to show only polygons containing the selected species
- Shows count of matching polygons

### 2. Interactive Map (Right Panel)
- Displays all polygons from the GeoJSON data
- **Blue polygons**: Contain the selected species (clickable)
- **Gray polygons**: Contain other species (non-interactive)
- Hover effects on clickable polygons
- Tooltips showing polygon ID and species

### 3. Plot Popups
When clicking on a blue polygon:
- Popup appears at the polygon center
- Shows polygon ID and species name
- Displays an Observable Plot bar chart
- Chart visualizes temporal data from the plot column

## Workflow

1. **Initial Load**: Dashboard loads with first species selected by default
2. **Change Species**: Select different species from dropdown
3. **Map Updates**: Polygons automatically re-color based on selection
4. **View Details**: Click any blue polygon to see its plot
5. **Close Popup**: Click map background or 'X' to close popup

## Data Flow

```
species_plots.csv (tabular data)
    ↓
[Species Dropdown] → filtered_polygon_ids
    ↓
polygons.geojson (spatial data)
    ↓
[Leaflet Map] → styled polygons
    ↓
[Click Event] → Popup with Observable Plot
```

## Technologies

- **Quarto**: Document framework
- **OJS**: Reactive JavaScript runtime
- **Leaflet**: Interactive mapping library
- **Observable Plot**: Data visualization
- **D3.js**: Data loading and manipulation

## Example Interactions

### Scenario 1: View Oak Species
1. Select "Oak" from dropdown
2. Map shows 2 blue polygons (poly_1, poly_3)
3. Click poly_1 → See bar chart with Oak data for poly_1
4. Click poly_3 → See bar chart with Oak data for poly_3

### Scenario 2: Switch to Pine Species
1. Select "Pine" from dropdown
2. Map updates to show 2 different blue polygons (poly_2, poly_5)
3. Previous Oak polygons turn gray
4. Click poly_2 → See bar chart with Pine data for poly_2

### Scenario 3: View Birch Species
1. Select "Birch" from dropdown
2. Map shows 1 blue polygon (poly_4)
3. All other polygons are gray
4. Click poly_4 → See bar chart with Birch data for poly_4

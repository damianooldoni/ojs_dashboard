# Reactive Polygon Filtering Fix

## Problem

The OJS dashboard had a critical reactivity issue:
- Only `poly_4` (species: Birch) remained blue and clickable
- Other polygons showed tooltips but clicking them did nothing
- Changing species in the dropdown had no effect on which polygons were clickable
- The polygon colors didn't update when selecting different species

## Root Cause

The original code structure had everything in a single OJS cell:

```javascript
```{ojs}
{
  const map = L.map(map_container).setView([51.15, 4.15], 11);
  const polygonLayer = L.layerGroup().addTo(map);
  
  function updatePolygons() {
    // ... update logic
  }
  
  filtered_polygon_ids; // Add dependency
  updatePolygons();
  
  return map_container;
}
```

**The Problem:** While this cell referenced `filtered_polygon_ids` at the end, OJS evaluates the entire cell as a single unit. The cell ran once on initial load and never re-executed when `filtered_polygon_ids` changed. The reference to `filtered_polygon_ids` created a dependency, but didn't actually trigger the update logic to run.

Since "Birch" is alphabetically first, it was selected by default, making only `poly_4` (Birch) appear blue and clickable initially.

## Solution

Separated the code into multiple OJS cells with clear responsibilities:

### 1. Map Initialization (runs once)
```javascript
```{ojs}
map = {
  const m = L.map(map_container).setView([51.15, 4.15], 11);
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors'
  }).addTo(m);
  return m;
}
```

### 2. Layer Group (runs once, depends on map)
```javascript
```{ojs}
polygonLayer = L.layerGroup().addTo(map)
```

### 3. Helper Functions (defined once)
```javascript
```{ojs}
function getPlotData(polygon_id) { ... }
```

```javascript
```{ojs}
function createPopupContent(polygon_id, species_name) { ... }
```

### 4. Reactive Polygon Update (runs whenever filtered_polygon_ids changes)
```javascript
```{ojs}
{
  // Clear existing polygons
  polygonLayer.clearLayers();
  
  // Add all polygons with appropriate styling
  polygons_geojson.features.forEach(feature => {
    const polygon_id = feature.properties.polygon_id;
    const is_filtered = filtered_polygon_ids.includes(polygon_id);
    // ... styling and event handlers
  });
  
  // Fit map bounds
  if (filtered_polygon_ids.length > 0) {
    const bounds = polygonLayer.getBounds();
    if (bounds.isValid()) {
      map.fitBounds(bounds, { padding: [50, 50] });
    }
  }
}
```

## How It Works Now

The reactive data flow:

1. User selects species from dropdown → `selected_species` changes
2. `filtered_data = species_data.filter(d => d.species === selected_species)` updates
3. `filtered_polygon_ids = filtered_data.map(d => d.polygons_id)` updates
4. **The reactive polygon cell detects the change** and re-executes:
   - Clears all existing polygons from the map
   - Iterates through all polygons in the GeoJSON
   - Applies blue styling to filtered polygons
   - Applies gray styling to non-filtered polygons
   - Attaches click handlers ONLY to blue (filtered) polygons
   - Fits the map bounds to show the filtered polygons

## Expected Behavior After Fix

✅ Selecting "Birch" → poly_4 turns blue and is clickable
✅ Selecting "Oak" → poly_1 and poly_3 turn blue and are clickable
✅ Selecting "Pine" → poly_2 and poly_5 turn blue and are clickable
✅ Clicking any blue polygon opens a popup with its plot
✅ Hover tooltips work on all polygons
✅ Gray polygons are not clickable

## Key Insights

**OJS Reactivity Rules:**
1. Each `{ojs}` cell is a separate reactive unit
2. When a cell references a variable (like `filtered_polygon_ids`), it becomes a dependency
3. When a dependency changes, the cell re-executes
4. Variables assigned with `=` (like `map =` or `polygonLayer =`) are available to other cells
5. Function definitions are global and available to all cells

**Why the Original Code Failed:**
- The entire block was one cell that ran once
- Adding `filtered_polygon_ids;` at the end created a dependency but didn't help
- The cell couldn't re-run properly because it tried to re-initialize the map
- Variables like `map` and `polygonLayer` were scoped locally with `const`

**Why the New Code Works:**
- Map initialization is separate and runs once
- The reactive cell ONLY contains the update logic
- Dependencies (`filtered_polygon_ids`, `polygons_geojson`, `species_data`) are clear
- When any dependency changes, just the reactive cell re-executes
- This efficiently updates the polygon styling and event handlers

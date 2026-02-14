# Before vs After: OJS Cell Structure

## BEFORE (Broken - Single Cell)

```
```{ojs}
{
  const map = L.map(...).setView(...);           // Initialize once
  const polygonLayer = L.layerGroup().addTo(map); // Initialize once
  
  function getPlotData(polygon_id) { ... }       // Define once
  function createPopupContent(...) { ... }       // Define once
  
  function updatePolygons() {                     // Define once
    polygonLayer.clearLayers();
    polygons_geojson.features.forEach(...);
    // Add polygons with styling and event handlers
  }
  
  filtered_polygon_ids; // Reference dependency
  updatePolygons();     // Call once
  
  return map_container;
}
```

**Problem**: 
- ❌ Entire cell runs once on load
- ❌ `updatePolygons()` is called once
- ❌ When `filtered_polygon_ids` changes, nothing happens
- ❌ Only initial species (Birch) has working click handlers
- ❌ Polygon colors don't update when species changes

---

## AFTER (Fixed - Multiple Cells)

### Cell 1: Map Initialization
```
```{ojs}
map = {
  const m = L.map(map_container).setView([51.15, 4.15], 11);
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors'
  }).addTo(m);
  return m;
}
```
✅ Runs once, `map` is available globally

### Cell 2: Layer Group
```
```{ojs}
polygonLayer = L.layerGroup().addTo(map)
```
✅ Runs once (depends on `map`), `polygonLayer` is available globally

### Cell 3: Helper Function 1
```
```{ojs}
function getPlotData(polygon_id) {
  const record = species_data.find(d => d.polygons_id === polygon_id);
  if (record) {
    return JSON.parse(record.plot);
  }
  return null;
}
```
✅ Defined once, available globally

### Cell 4: Helper Function 2
```
```{ojs}
function createPopupContent(polygon_id, species_name) {
  const plotData = getPlotData(polygon_id);
  if (!plotData) return `<b>No data available</b>`;
  // ... create popup with plot
  return popupDiv;
}
```
✅ Defined once, available globally

### Cell 5: REACTIVE Polygon Update
```
```{ojs}
{
  // Clear existing polygons
  polygonLayer.clearLayers();
  
  // Add all polygons with appropriate styling
  polygons_geojson.features.forEach(feature => {
    const polygon_id = feature.properties.polygon_id;
    const is_filtered = filtered_polygon_ids.includes(polygon_id);
    
    // Style based on whether it's in filtered set
    const style = {
      fillColor: is_filtered ? '#3388ff' : '#cccccc',
      weight: 2,
      opacity: 1,
      color: is_filtered ? '#0066cc' : '#999999',
      fillOpacity: is_filtered ? 0.6 : 0.3
    };
    
    const layer = L.geoJSON(feature, {
      style: style,
      onEachFeature: (feature, layer) => {
        layer.bindTooltip(`${polygon_id} (${species_name})`, {
          permanent: false,
          direction: 'center'
        });
        
        // Add click handler with popup (only for filtered)
        if (is_filtered) {
          layer.on('click', () => {
            const popupContent = createPopupContent(polygon_id, species_name);
            L.popup()
              .setLatLng(layer.getBounds().getCenter())
              .setContent(popupContent)
              .openOn(map);
          });
          
          // Change cursor on hover
          layer.on('mouseover', () => {
            layer.setStyle({ fillOpacity: 0.8 });
          });
          layer.on('mouseout', () => {
            layer.setStyle({ fillOpacity: 0.6 });
          });
        }
      }
    });
    
    layer.addTo(polygonLayer);
  });
  
  // Fit map to filtered polygons
  if (filtered_polygon_ids.length > 0) {
    const bounds = polygonLayer.getBounds();
    if (bounds.isValid()) {
      map.fitBounds(bounds, { padding: [50, 50] });
    }
  }
}
```
✅ This cell references `filtered_polygon_ids`, `polygons_geojson`, and `species_data`
✅ When any of these dependencies change, the cell re-executes
✅ Polygons are cleared and re-added with fresh styling and event handlers
✅ All filtered (blue) polygons get working click handlers

---

## Data Flow Comparison

### BEFORE (Broken)
```
User selects species
    ↓
selected_species changes
    ↓
filtered_data updates
    ↓
filtered_polygon_ids updates
    ↓
❌ NOTHING HAPPENS (cell doesn't re-run)
    ↓
Map still shows Birch polygons in blue
Click handlers only work on poly_4
```

### AFTER (Fixed)
```
User selects species
    ↓
selected_species changes
    ↓
filtered_data updates
    ↓
filtered_polygon_ids updates
    ↓
✅ Reactive cell detects change
    ↓
✅ polygonLayer.clearLayers() removes old polygons
    ↓
✅ forEach loop adds all polygons back
    ↓
✅ Filtered polygons get blue styling
✅ Filtered polygons get click handlers
✅ Non-filtered polygons get gray styling
✅ Non-filtered polygons get NO click handlers
    ↓
✅ Map shows correct polygons in blue
✅ Click handlers work on all blue polygons
✅ Each polygon shows its own data
```

---

## Key Differences

| Aspect | Before | After |
|--------|--------|-------|
| Cell structure | Single monolithic cell | Multiple focused cells |
| Map initialization | Inside reactive block | Separate cell (runs once) |
| Variable scope | Local (`const map`) | Global (`map =`) |
| Reactivity | Broken (reference but no re-run) | Working (cell re-executes) |
| Click handlers | Only on initial species | On all filtered polygons |
| Color updates | Never update | Update on every species change |
| Code organization | Hard to debug | Clear and modular |

---

## Summary

**The Fix**: By separating concerns into multiple OJS cells and ensuring the polygon update logic is in its own reactive cell, we enable proper OJS reactivity. When `filtered_polygon_ids` changes, only the necessary code (polygon updates) re-executes, while the map initialization remains stable.

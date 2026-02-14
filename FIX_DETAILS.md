# Map Container Fix - Technical Details

## Problem

When running `quarto preview index.qmd`, the dashboard displayed the error:
```
OJS Error
Error: Map container not found.
```

## Root Cause

The error occurred in the map initialization code block (originally lines 68-73):

```javascript
{
  // Initialize map
  const container = map_container.querySelector('#map');
  
  // Create map centered on Belgium (where coordinates are)
  const map = L.map(container).setView([51.15, 4.15], 11);
```

### Why it failed:

1. In OJS, `map_container` is created using: 
   ```javascript
   map_container = html`<div id="map" style="height: 500px; width: 100%;"></div>`
   ```

2. This creates a DOM element directly - `map_container` **IS** the div element

3. The code then tried to use `querySelector('#map')` on the element itself:
   ```javascript
   const container = map_container.querySelector('#map');
   ```

4. This is incorrect because:
   - `querySelector` searches **within** an element for descendants
   - `map_container` IS the `#map` div, not a parent containing it
   - The query returned `null` because there's no child element with id="map"
   - Leaflet received `null` and threw "Map container not found"

## Solution

Remove the querySelector and pass `map_container` directly to Leaflet:

```javascript
{
  // Initialize map - map_container is the div element itself
  // Create map centered on Belgium (where coordinates are)
  const map = L.map(map_container).setView([51.15, 4.15], 11);
```

### Why this works:

1. `map_container` is already the DOM element Leaflet needs
2. `L.map()` accepts either:
   - A string ID selector (e.g., `'map'`)
   - A DOM element directly (e.g., `map_container`)
3. Passing the element directly is actually more efficient

## Verification

The fix has been applied and tested:

1. ✅ The Quarto document renders successfully
2. ✅ The generated HTML contains the corrected code
3. ✅ No querySelector is used
4. ✅ Comments clarify that map_container is the div element itself

## Testing Locally

To test the fix locally:

```bash
# Preview the dashboard
quarto preview index.qmd

# Or build and serve
quarto render
cd docs
python3 -m http.server 8080
# Open http://localhost:8080/index.html
```

The map should now load without errors, showing:
- A map centered on Belgium (coordinates 51.15, 4.15)
- OpenStreetMap tiles
- Polygons displayed based on selected species
- Interactive click handlers for popups

## Code Comparison

### Before (BROKEN):
```javascript
const container = map_container.querySelector('#map');
const map = L.map(container).setView([51.15, 4.15], 11);
```

### After (FIXED):
```javascript
const map = L.map(map_container).setView([51.15, 4.15], 11);
```

The fix is minimal and surgical - only removing the problematic querySelector line.

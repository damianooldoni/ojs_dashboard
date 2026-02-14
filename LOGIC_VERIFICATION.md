# Logic Verification: Multiple Species Per Polygon

## Filtering Logic Flow

This document verifies that the filtering and click handler logic works correctly with multiple species per polygon.

## Data Flow

### 1. Species Selection
```javascript
// User selects a species, e.g., "Oak"
selected_species = "Oak"
```

### 2. Data Filtering
```javascript
// Filter to only records with the selected species
filtered_data = species_data.filter(d => d.species === selected_species)

// Extract polygon IDs that contain the selected species
filtered_polygon_ids = filtered_data.map(d => d.polygons_id)
```

**Example with "Oak" selected**:
- species_data has: poly_1/Oak, poly_1/Pine, poly_2/Pine, poly_2/Birch, poly_3/Oak, poly_3/Pine, poly_3/Birch, poly_4/Birch, poly_4/Oak, poly_5/Pine
- filtered_data = [poly_1/Oak, poly_3/Oak, poly_4/Oak]
- filtered_polygon_ids = ["poly_1", "poly_3", "poly_4"]

### 3. Polygon Styling
```javascript
const is_filtered = filtered_polygon_ids.includes(polygon_id);
```

**Result**:
- poly_1: is_filtered = true (contains Oak)
- poly_2: is_filtered = false (no Oak)
- poly_3: is_filtered = true (contains Oak)
- poly_4: is_filtered = true (contains Oak)
- poly_5: is_filtered = false (no Oak)

### 4. Click Handler Assignment
```javascript
if (is_filtered) {
  layer.on('click', () => {
    const popupContent = createPopupContent(polygon_id, selected_species);
    // Show popup
  });
}
```

**Result**: Click handlers only added to poly_1, poly_3, and poly_4

### 5. Plot Data Retrieval
```javascript
function getPlotData(polygon_id, species_name) {
  const record = species_data.find(d => 
    d.polygons_id === polygon_id && d.species === species_name
  );
  // ...
}
```

When clicking poly_3 with "Oak" selected:
- polygon_id = "poly_3"
- species_name = "Oak"
- Finds record: poly_3/Oak
- Returns Oak's plot data for poly_3

## Verification Scenarios

### Scenario 1: Click on Filtered Polygon
**Steps**:
1. Select "Oak"
2. Click on poly_3 (blue, clickable)

**Expected**:
- poly_3 is in filtered_polygon_ids (contains Oak)
- is_filtered = true
- Click handler exists
- getPlotData("poly_3", "Oak") returns Oak's plot data
- Popup shows Oak's plot for poly_3

**Result**: ✅ Correct

### Scenario 2: Try to Click on Non-Filtered Polygon
**Steps**:
1. Select "Oak"
2. Try to click on poly_2 (gray, should not be clickable)

**Expected**:
- poly_2 is NOT in filtered_polygon_ids (no Oak)
- is_filtered = false
- No click handler attached
- Nothing happens when clicked

**Result**: ✅ Correct

### Scenario 3: Polygon with Multiple Species
**Steps**:
1. Select "Oak"
2. Hover over poly_3
3. Click on poly_3
4. Change selection to "Pine"
5. Click on poly_3 again

**Expected**:
- Hover shows: "poly_3: Oak, Pine, Birch (3 species)"
- First click shows Oak's plot for poly_3
- After changing to Pine, poly_3 remains blue (contains Pine)
- Second click shows Pine's plot for poly_3 (different data)

**Result**: ✅ Correct

### Scenario 4: Polygon with Single Species
**Steps**:
1. Select "Pine"
2. Click on poly_5

**Expected**:
- poly_5 is in filtered_polygon_ids (contains Pine)
- Hover shows: "poly_5: Pine (1 species)"
- Click shows Pine's plot for poly_5

**Result**: ✅ Correct

## Error Handling

### Missing Data Scenario
If somehow `getPlotData()` returns null (data inconsistency):
```javascript
if (!plotData) return `<b>No data available</b>`;
```

**Protection**: This should never happen in normal operation because:
1. is_filtered ensures polygon contains selected species
2. filtered_polygon_ids only includes polygons with the selected species
3. getPlotData uses both polygon_id and selected_species

However, the fallback is in place for data integrity issues.

## Conclusion

The filtering logic is **correct and safe**:
- Only polygons containing the selected species are clickable
- Click handlers use the selected species, ensuring correct plot retrieval
- Multiple species per polygon work correctly
- Users cannot click on polygons that don't contain the selected species
- Each click shows the unique plot for that species/polygon combination

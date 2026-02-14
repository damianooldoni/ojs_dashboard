# Testing the Reactivity Fix

## Manual Testing Steps

To verify the fix works correctly, follow these steps:

### 1. Start the Dashboard

```bash
cd /home/runner/work/ojs_dashboard/ojs_dashboard
quarto preview index.qmd
```

### 2. Test Birch Species (Default)

When the dashboard loads:
- **Expected:** Species dropdown should show "Birch" selected
- **Expected:** poly_4 should be blue
- **Expected:** All other polygons (poly_1, poly_2, poly_3, poly_5) should be gray
- **Action:** Click on poly_4 (blue polygon)
- **Expected:** Popup appears showing "Birch - poly_4" with a bar chart
- **Action:** Hover over gray polygons
- **Expected:** Tooltips appear showing polygon ID and species name

### 3. Test Oak Species

- **Action:** Select "Oak" from dropdown
- **Expected:** poly_1 and poly_3 turn blue
- **Expected:** poly_4 (Birch) turns gray
- **Expected:** poly_2 and poly_5 (Pine) remain gray
- **Action:** Click on poly_1 (blue polygon)
- **Expected:** Popup shows "Oak - poly_1" with bar chart data [12, 19, 3, 5, 2, 3]
- **Action:** Click on poly_3 (blue polygon)
- **Expected:** Popup shows "Oak - poly_3" with bar chart data [15, 21, 7, 9, 4, 6]
- **Action:** Try clicking on gray polygon (e.g., poly_4)
- **Expected:** Nothing happens (no popup)

### 4. Test Pine Species

- **Action:** Select "Pine" from dropdown
- **Expected:** poly_2 and poly_5 turn blue
- **Expected:** All Oak and Birch polygons turn gray
- **Action:** Click on poly_2 (blue polygon)
- **Expected:** Popup shows "Pine - poly_2" with bar chart data [7, 11, 5, 8, 3, 7]
- **Action:** Click on poly_5 (blue polygon)
- **Expected:** Popup shows "Pine - poly_5" with bar chart data [6, 10, 4, 6, 2, 5]

### 5. Test Rapid Species Changes

- **Action:** Quickly switch between species: Oak → Pine → Birch → Oak
- **Expected:** Map updates smoothly each time
- **Expected:** Correct polygons turn blue for each species
- **Expected:** Click handlers work on the newly blue polygons
- **Expected:** No console errors

## Automated Verification Checklist

✅ Dashboard renders without errors (`quarto render index.qmd`)
✅ Generated HTML contains separate OJS cells
✅ Map initialization cell is separate from polygon update cell
✅ Reactive cell references `filtered_polygon_ids`
✅ Helper functions are defined in separate cells
✅ No syntax errors in JavaScript code

## Data Reference

For verification, here's the species-polygon mapping:

| Polygon ID | Species | Data Points |
|------------|---------|-------------|
| poly_1     | Oak     | [12, 19, 3, 5, 2, 3] |
| poly_2     | Pine    | [7, 11, 5, 8, 3, 7] |
| poly_3     | Oak     | [15, 21, 7, 9, 4, 6] |
| poly_4     | Birch   | [9, 14, 6, 7, 5, 8] |
| poly_5     | Pine    | [6, 10, 4, 6, 2, 5] |

## Expected Species Counts

- Birch: 1 polygon (poly_4)
- Oak: 2 polygons (poly_1, poly_3)
- Pine: 2 polygons (poly_2, poly_5)

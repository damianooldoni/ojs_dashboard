# Multiple Species Per Polygon Enhancement

## Overview

The dashboard has been enhanced to support a more realistic data model where **multiple species can exist in the same polygon**. Each unique combination of species and polygon has its own associated plot.

## Data Model

### Before
- One-to-one relationship: Each polygon had exactly one species
- 5 records total (one per polygon)

### After
- Many-to-many relationship: Each polygon can have multiple species
- 10 records total (multiple species per polygon)
- Each unique `(polygons_id, species)` combination has one plot

## Dataset Composition

| Polygon ID | Species Count | Species Present |
|------------|---------------|-----------------|
| poly_1     | 2             | Oak, Pine       |
| poly_2     | 2             | Pine, Birch     |
| poly_3     | 3             | Oak, Pine, Birch|
| poly_4     | 2             | Birch, Oak      |
| poly_5     | 1             | Pine            |

## User Experience

### Species Selection
1. User selects a species from the dropdown (e.g., "Oak")
2. Map highlights all polygons containing that species in blue
3. For "Oak", polygons 1, 3, and 4 would be highlighted

### Polygon Interaction

**Hovering**:
- Tooltip shows all species in the polygon with count
- Example: "poly_3: Oak, Pine, Birch (3 species)"

**Clicking**:
- Popup displays the plot for the **selected species** in that polygon
- Each species/polygon combination has unique plot data
- If you select "Oak" and click poly_3, you see Oak's plot for poly_3
- If you switch to "Pine" and click poly_3, you see Pine's plot for poly_3

## Technical Implementation

### Key Changes

1. **Enhanced Data Structure**
   - CSV now contains multiple rows per polygon
   - Each row represents one species/polygon combination

2. **Updated Function: `getPlotData()`**
   ```javascript
   function getPlotData(polygon_id, species_name) {
     const record = species_data.find(d => 
       d.polygons_id === polygon_id && d.species === species_name
     );
     if (record) {
       return JSON.parse(record.plot);
     }
     return null;
   }
   ```
   - Now requires both polygon_id AND species_name
   - Finds the specific record matching both criteria

3. **Updated Tooltip Logic**
   ```javascript
   const polygon_records = species_data.filter(d => d.polygons_id === polygon_id);
   const all_species = polygon_records.map(d => d.species);
   const species_count = all_species.length;
   ```
   - Shows all species present in a polygon
   - Provides species count for quick reference

4. **Updated Click Handler**
   ```javascript
   layer.on('click', () => {
     const popupContent = createPopupContent(polygon_id, selected_species);
     // ...
   });
   ```
   - Uses the currently selected species
   - Ensures correct plot is shown for the selection

## Benefits

1. **Realistic Data Model**: Reflects real-world scenarios where multiple species coexist
2. **Flexible Analysis**: Users can explore different species in the same polygon
3. **Rich Information**: Tooltips provide overview of all species per polygon
4. **Intuitive Interaction**: Clear connection between selection and displayed plot

## Example Workflow

1. **Explore Oak Distribution**
   - Select "Oak" from dropdown
   - See polygons 1, 3, and 4 highlighted
   - Hover over poly_3 to see: "poly_3: Oak, Pine, Birch (3 species)"
   - Click poly_3 to view Oak's specific plot for that polygon

2. **Switch to Pine Analysis**
   - Select "Pine" from dropdown
   - See polygons 1, 2, 3, and 5 highlighted
   - Click poly_3 again
   - Now see Pine's plot for poly_3 (different from Oak's plot)

## Data Uniqueness Constraint

The key constraint is: **Each (polygons_id, species) combination must be unique**

This ensures:
- No duplicate records
- Each combination has exactly one plot
- Clear mapping from selection to visualization
- Consistent user experience

# Dark Theme Implementation

## Overview

The OJS Dashboard has been configured with a dark theme for improved visual aesthetics and reduced eye strain in low-light environments.

## Theme Details

### Quarto Theme
- **Theme Name**: `darkly`
- **Theme Type**: Bootstrap-based dark theme
- **Applied In**: Both `_quarto.yml` and `index.qmd`

### Color Palette

The darkly theme provides:
- **Background**: Dark grey/black tones
- **Text**: Light grey/white for high contrast
- **Navigation**: Dark navbar with light text
- **Accents**: Blue highlights for interactive elements

### Custom CSS Enhancements

Additional styling has been applied in `styles.css` to ensure all components work well with the dark theme:

```css
/* Map border - darker for dark theme */
#map {
  border: 1px solid #444;
  border-radius: 4px;
}

/* Leaflet popup styling for dark theme */
.leaflet-popup-content-wrapper {
  background: #2b3e50;
  color: #ffffff;
}

.leaflet-popup-tip {
  background: #2b3e50;
}
```

### Components with Dark Theme Support

1. **Navigation Bar**: Dark navbar with light text
2. **Main Content**: Dark background with light text
3. **Interactive Map**: 
   - Dark borders
   - Polygons display correctly on map tiles
   - Tooltips have dark backgrounds
4. **Popups**: 
   - Dark background (#2b3e50)
   - White text for readability
   - Observable Plot charts render appropriately
5. **Species Dropdown**: Styled to match dark theme
6. **Filtered Polygons Counter**: Light text on dark background

## Benefits

1. **Reduced Eye Strain**: Especially beneficial in low-light environments
2. **Modern Aesthetics**: Contemporary look and feel
3. **Better Focus**: Dark backgrounds help focus attention on data visualizations
4. **Energy Savings**: On OLED/AMOLED displays, dark themes consume less power

## Alternative Themes

If you prefer a different theme, Quarto supports several Bootstrap themes. To change:

### Other Dark Themes:
- `slate` - A subtle dark theme
- `superhero` - A more vibrant dark theme
- `solar` - Dark theme with unique color palette
- `vapor` - A dark neon theme
- `cyborg` - High-contrast dark theme

### Light Themes:
- `cosmo` - The original theme (clean and modern)
- `flatly` - Flat design
- `lumen` - Light and clean
- `minty` - Fresh green accents

### To Change Theme:

1. Edit `_quarto.yml`:
```yaml
format:
  html:
    theme: your-chosen-theme
    css: styles.css
    toc: false
```

2. Edit `index.qmd` frontmatter:
```yaml
---
title: "OJS Dashboard"
format:
  html:
    page-layout: full
    theme: your-chosen-theme
---
```

3. Re-render: `quarto render`

## Customization

To further customize the dark theme, you can:

1. **Modify styles.css**: Add custom CSS rules
2. **Override theme variables**: Create a custom SCSS file
3. **Adjust specific components**: Target specific classes in CSS

Example custom color for filtered polygons:
```css
/* Custom highlight color for selected polygons */
.leaflet-interactive[style*="fill:#3388ff"] {
  /* Your custom styling */
}
```

## Accessibility

The dark theme maintains good contrast ratios for accessibility:
- Text contrast meets WCAG AA standards
- Interactive elements are clearly visible
- Color is not the only means of conveying information

## Browser Compatibility

The dark theme works across all modern browsers:
- Chrome/Edge (Chromium)
- Firefox
- Safari
- Opera

No special configuration needed for browser dark mode as the theme is explicitly set.

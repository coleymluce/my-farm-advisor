# Jefferson Farm Field Dashboard

## Overview

A field-level dashboard for Jefferson Farm providing interactive maps, weather forecasts, soil data, and PDF export functionality.

## Features

- **Field Selection**: Dropdown to select from 57 fields
- **Weather Forecast**: 3-day weather from Open-Meteo API
- **Alert Cards**: GDD, Precipitation, NDVI, Soil Health with conditional warnings
- **Interactive Map**: Leaflet map with field polygons and satellite basemap
- **Layer Toggles**: Switch between Soil and NDVI layers
- **Field Details Panel**: Soil type, pH, OM, CEC, drainage, NDVI
- **Directions**: Google Maps link from current location
- **PDF Export**: Screenshot-style export using html2canvas

## File Structure

```
notebooks/html/
├── field-dashboard.html    # Main dashboard (15,600+ lines)
└── dashboard_data.json # Embedded field data & GeoJSON
```

## Data Sources

- **Weather**: Open-Meteo API (`https://api.open-meteo.com/v1/forecast`)
- **Satellite Imagery**: Esri World Imagery
- **Field Boundaries**: GeoJSON from `jefferson_farm_soil_overlay.geojson`
- **NDVI**: Per-field statistics from `ndvi_per_field/per_field_ndvi.json`

## Key Implementation Details

### Map Rendering

```javascript
// Initialize map with satellite tiles
map = L.map('map', {
    center: [43.8856, -112.2705],
    zoom: 11
});

L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
    attribution: '© Esri'
}).addTo(map);
```

### Popups

Field popups display compact info: field name, soil type, pH, OM, NDVI, and area.

```javascript
layer.bindPopup(popupContent, {
    autoPan: true,
    autoPanPadding: [40, 40],
    maxWidth: 220,
    minWidth: 160,
    offset: [0, -20]
});
```

### PDF Export

Uses html2canvas to capture the dashboard visually.

```javascript
html2canvas(document.getElementById('dashboard'), {
    scale: 2,
    useCORS: true,
    backgroundColor: '#f5f7f5'
}).then(canvas => { ... });
```

### CSS Structure

- `.dashboard`: Main container (flex column)
- `.map-panel`: Fixed height 520px
- `#map`: Explicit height 480px with `overflow: hidden`
- Leaflet popup styling for consistent appearance

## Dependencies

- Leaflet 1.9.4 (`unpkg.com/leaflet@1.9.4`)
- html2canvas 1.4.1 (`cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1`)
- Chart.js 3.x (bundled in HTML)

## Usage

Open `field-dashboard.html` in a browser. Select a field from the dropdown to:
- View field details in the panel
- See weather for that field's location
- Center the map on the field
- Get Google Maps directions

## Development History

Key commits in order of significance:

1. **a090903** - Fix: simplify popup, improve init with bounds fitting
2. **7a30e4b** - Fix map not rendering: add overflow hidden + invalidateSize on init
3. **7268f00** - Compact popup: smaller size, top-center anchor, removed tooltip
4. **dd9d88b** - Fix popup clipping: add autoPan options, offset, visible overflow
5. **2ae5172** - Fix map rendering: explicit heights, invalidateSize delays
6. **ee0c33b** - Add interactive field dashboard with map, weather, NDVI, PDF export

## Known Issues Resolved

- Map not showing polygons: Fixed by adding `overflow: hidden` to `#map` CSS and calling `map.invalidateSize()` after init
- Popup clipping: Fixed with `autoPan` options and explicit padding
- Popup too large: Reduced to compact 220px max width
- Map centering: Use `fitBounds()` on polygon bounds, not centroids
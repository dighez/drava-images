# DRAVABIKE PROJECT - COMPLETE DOCUMENTATION
**Last Updated:** 2026-03-27  
**Project:** DravaBike StoryMap - Interreg Europe Cycling Route  
**Status:** In Development

---

## PROJECT OVERVIEW

**Objective:** Create interactive StoryMap for DravaBike cycling route along Drava River (Slovenia) showcasing outdoor classroom concept for environmental education.

**Structure:** 3-section narrative
1. **Challenge:** River ecosystem disconnected from communities
2. **Solution:** Interactive cycling route with interpretation points (THIS MAP - custom web app)
3. **Evidence:** Impact metrics and monitoring data

**Route:** 145km in Slovenia (part of 510km international greenway)

---

## GITHUB REPOSITORY

**Account:** cyclingwaterways  
**Email:** cyclingwaterways.github@gmail.com  
**Repository:** https://github.com/cyclingwaterways/drava-storymap  
**GitHub Pages:** https://cyclingwaterways.github.io/drava-storymap/  
**Web App URL:** https://cyclingwaterways.github.io/drava-storymap/dravabike-webapp.html

**Repository Contents:**
- `index.html` - Main web app file
- 13 placeholder images: `LP_01_Miklavski_potok.jpg` through `LP_13_Limbusko_nabrezje.jpg`

---

## ARCGIS ONLINE SETUP

**Account:** Politecnico di Milano (poli.maps.arcgis.com)  
**Folder:** DravaBike  
**Web Map ID:** 9fbc757c6ceb4ea4b39b96ffc47b4ea9  
**Web Map URL:** https://poli.maps.arcgis.com/apps/mapviewer/index.html?webmap=9fbc757c6ceb4ea4b39b96ffc47b4ea9

### HOSTED FEATURE LAYERS (8 total)

All layers in EPSG:3794 (MGI 1901 / Slovene National Grid) - ArcGIS Online handles reprojection automatically.

#### 1. Drava - Interpretation Locations
- **Source:** DRAVA_INTERPRETATION-LOCATIONS2.gpkg (13 features)
- **Fields:** Point (name), Type (1=Center, 2=Polygon, 3=Point), photo_url, description
- **Tags:** interpretation, education, drava
- **Summary:** Educational centers and learning points along cycling route promoting environmental awareness
- **Symbology:** Unique values by Type field
  - Type 1 (Centers): Dark green/brown X symbol
  - Type 2 (Polygons): Gold/yellow square
  - Type 3 (Points): Yellow circle
- **Pop-ups:** Configured with photo from photo_url field

#### 2. Drava - Cycling Route
- **Source:** DRAVA_CYCLE-PATH.gpkg (6 segments)
- **Tags:** cycling, bike path, drava
- **Summary:** Main and alternative cycling paths following Drava river through Slovenia
- **Symbology:** Red line, 3-4px width

#### 3. Drava - River Bed
- **Source:** DRAVA_RIVER-POLYGON.gpkg (49 features)
- **Tags:** river, hydrology, drava
- **Summary:** Physical extent of Drava river bed along cycling corridor
- **Symbology:** Light blue (azzurro), 30-40% transparency

#### 4. Drava - River Watercourse ⚠️ [ISSUE]
- **Source:** DRAVA_RIVER-LINE.gpkg (376 features)
- **Tags:** river, watercourse, drava
- **Summary:** Linear representation of the Drava river and hydrological network
- **Symbology:** Light blue line, 2px
- **STATUS:** Not displaying on ArcGIS Online - troubleshooting in progress

#### 5. Drava - Natura 2000 Sites
- **Source:** DRAVA_NATURA-2000.gpkg (6 features)
- **Tags:** natura2000, protected areas, drava
- **Summary:** EU Natura 2000 sites protecting habitats and species
- **Symbology:** Yellow (giallo), 50-60% transparency

#### 6. Drava - Protected Areas
- **Source:** DRAVA_PROTECTED-AREAS.gpkg (41 features)
- **Tags:** protected areas, conservation, drava
- **Summary:** National and regional nature reserves along Drava corridor
- **Symbology:** Light blue (azzurro chiaro), 50-60% transparency

#### 7. Drava - Railway Network
- **Source:** DRAVA_RAILWAY-NETWORK.gpkg (86 features)
- **Tags:** railway, infrastructure, drava
- **Summary:** Railway lines for spatial reference and orientation
- **Symbology:** Black dashed line, 2px

#### 8. Drava - Natural Features
- **Source:** DRAVA_FEATURES-POINT_750.gpkg (26 features)
- **Tags:** natural heritage, features, drava
- **Summary:** Protected natural monuments and valuable features along route
- **Symbology:** Green symbols (tree/triangle icons)

### WEB MAP CONFIGURATION

**Layer Order (bottom to top):**
1. River Bed
2. River Watercourse
3. Natura 2000 Sites
4. Protected Areas
5. Natural Features
6. Railway Network
7. Cycling Route
8. Interpretation Locations (TOP - interactive)

**Initial Extent:** Natura 2000 layer extent with 1.1x padding  
**Zoom Constraints:** minZoom 9, maxZoom 17  
**Basemap:** User-selectable via Map Viewer

---

## WEB APP TECHNICAL DETAILS

**File:** index.html (formerly dravabike-webapp.html)  
**Technology:** ArcGIS Maps SDK for JavaScript 4.28  
**Hosting:** GitHub Pages

### KEY FEATURES

1. **Header:** "DravaBike - Cycling Through Nature"

2. **Interactive Map:**
   - Loads Web Map from ArcGIS Online (ID: 9fbc757c6ceb4ea4b39b96ffc47b4ea9)
   - Hover effect on interpretation locations (300ms delay)
   - Click to zoom to feature
   - Real-time connection to ESRI Feature Services

3. **Side Panel (400px width):**
   - Type badge (color-coded by Type field)
   - Location name
   - Photo from GitHub repository
   - Description text
   - Close button

4. **Widgets:**
   - Legend (card style, collapsible, collapsed by default, top-right)
   - ScaleBar (metric, bottom-left)
   - Compass (top-left)

5. **User Experience:**
   - Hover instruction tooltip (fades in after 1s, hides after first interaction)
   - Responsive design
   - Professional styling with Tailwind-inspired colors

### PHOTO URL MAPPING

Direct mapping in code to bypass field parsing issues:

```javascript
const photoFilenames = {
  1: "LP_01_Miklavski_potok.jpg",
  2: "LP_02_Duplek.jpg",
  3: "LP_03_Zgornje_Hoce.jpg",
  4: "LP_04_Hotinja_vas.jpg",
  5: "LP_05_Zlatolipje.jpg",
  6: "LP_06_Betnava_gozd.jpg",
  7: "LP_07_Razvanjsko_jezero.jpg",
  8: "LP_08_Mariborski_otok.jpg",
  9: "LP_09_Bresternica_breg.jpg",
  10: "LP_10_Limbus_park.jpg",
  11: "LP_11_Zrkovsko_jezero.jpg",
  12: "LP_12_Mestni_park_Maribor.jpg",
  13: "LP_13_Limbusko_nabrezje.jpg"
};
```

### TECHNICAL SOLUTION

**Problem:** `hitTest()` only returns basic fields (FID, geometry) but not custom attributes.

**Solution:** Use `layer.queryFeatures()` to fetch full attributes:
```javascript
const query = layer.createQuery();
query.objectIds = [graphic.attributes.FID];
const result = await layer.queryFeatures(query);
const fullAttributes = result.features[0].attributes;
```

---

## DATA ARCHITECTURE

### DATA FLOW

```
ArcGIS Online Feature Services ←→ Web App (GitHub Pages) ←→ Images (GitHub)
         ↓                                    ↓
    Web Map configuration              Custom interactivity
    (styles, pop-ups)                  (hover, side panel)
```

### WHY THIS ARCHITECTURE?

**Web Map (ArcGIS Online):**
- Stores all geographic data
- Manages symbology and layer configuration
- Provides Feature Services API
- Modifications update web app automatically

**Web App (GitHub):**
- HTML file using ArcGIS Maps SDK for JavaScript
- Adds custom hover effect and side panel UI
- Free hosting for static files
- Easy image management

**Benefits:**
- Real-time updates: modify Web Map → app updates automatically
- No code changes needed for data/style updates
- Professional ESRI infrastructure for geodata
- GitHub simplifies collaboration and version control

---

## STORYMAP CONTENT

### INTRO TEXT
"The DravaBike cycling route transforms the Drava River into an outdoor classroom, where sustainable mobility meets environmental education. Stretching 145 kilometers through Slovenia as part of an international 510 km greenway, this route does more than connect cyclists to nature—it actively engages them with the ecological and cultural value of water ecosystems.

Along the path, interpretive units, educational trails, and learning polygons guide visitors through the river's story. Each stop reveals the dynamics of floodplains, the habitats of protected species, and the significance of Natura 2000 sites that safeguard Europe's biodiversity. The route demonstrates how cycling infrastructure can serve dual purposes: providing recreation while fostering environmental awareness and stewardship.

This Nature Interpretation Plan creates meaningful connections between people and place. Cyclists don't just pass through protected areas—they learn why these landscapes matter, how river systems function, and what role conservation plays in sustaining both natural heritage and local communities. DravaBike proves that the best classroom has no walls, just open trails and the rhythm of pedaling through living ecosystems."

### SECTION 1: THE CHALLENGE
**Title:** "A Hidden Treasure: The Drava River Ecosystem"

**Text:** The Drava River flows through Slovenia as an ecologically significant corridor, but remains largely unknown to local communities. Rich biodiversity thrives in its floodplains, wetlands, and Natura 2000 protected areas, yet these natural treasures remain isolated without accessible paths connecting people to place.

**Map:** Web Map WITHOUT cycling route and interpretation locations (only river, protected areas, natural features) - TO BE CREATED

### SECTION 2: THE SOLUTION
**Title:** "Pedaling Into Understanding: The Outdoor Classroom"

**Text:** A 145km cycling route creates an outdoor classroom along the Drava River. Interpretation centers, educational points, and learning polygons guide cyclists through the river's story. Sustainable mobility becomes the vehicle for environmental awareness, transforming passive recreation into active engagement with nature.

**Map:** Interactive web app with hover effects (CURRENT IMPLEMENTATION)

### SECTION 3: THE EVIDENCE
**Title:** "Measuring Impact: Awareness Through Action"

**Text:** Progress is tangible: 5-10km of route upgraded annually, monitoring stations tracking visitor flow, educational programs engaging schools and communities. Success is measured in kilometers completed, cyclists counted, and awareness raised one pedal stroke at a time.

**Content:** Photo + infographic with impact metrics (numbers to be provided by Barbara)

---

## PENDING TASKS

### CRITICAL PRIORITY

#### 1. River Watercourse Display Issue ⚠️
**Problem:** DRAVA_RIVER-LINE.gpkg not displaying on ArcGIS Online despite successful upload.

**Troubleshooting Steps:**
1. Open in QGIS, verify geometries visible and valid
2. Check feature count (should be 376) and CRS (EPSG:3794)
3. Run "Check Validity" tool in QGIS
4. Export clean version: QGIS → Export → Save Features As → GeoPackage
5. Try reprojecting to EPSG:3857 (Web Mercator) or EPSG:4326 (WGS84)
6. Re-upload to ArcGIS Online

**Alternative Data Source:**
- Slovenia ARSO (Environmental Agency) WFS: http://gis.arso.gov.si/geoserver/ows?service=wfs
- Atlas Vode: https://geohub.gov.si/atlasvoda
- Open Data: https://podatki.gov.si/dataset/hidrografija

### HIGH PRIORITY

#### 2. Update Legend Labels
Change Interpretation Locations legend from numbers to descriptive text:
- 1 → "Interpretation Center"
- 2 → "Interpretation Polygon"
- 3 → "Interpretation Point"

**How:** ArcGIS Online Web Map → Layer Styles → Unique symbols → Edit legend text

#### 3. Replace Placeholder Images
Awaiting final photos from Barbara (Slovenia partner).

**Instructions for Barbara:**
- Photos must maintain exact same filenames as current placeholders
- Format: JPG
- Recommended size: 600x400px or similar aspect ratio
- Upload to GitHub repository or send to us for upload

#### 4. Create Map 1 (Challenge)
Duplicate current Web Map and remove:
- Cycling Route layer
- Interpretation Locations layer
Keep only:
- River layers
- Protected areas
- Natural features
- Railway (optional)

### MEDIUM PRIORITY

#### 5. Complete StoryMap Assembly
Build full StoryMap on ArcGIS Online platform integrating:
- Intro text
- Map 1 (Challenge - to be created)
- Map 2 (Solution - embed web app or use Web Map)
- Map 3 (Evidence - photo/infographic with metrics)

#### 6. Optimize Web App Performance
- Test loading times
- Optimize image sizes if needed
- Consider lazy loading for images

---

## TEAM COMMUNICATIONS

### EMAILS SENT

**To Barbara (Slovenia Partner):**
- Shared web app draft URL
- Shared GitHub repository
- Explained photo replacement process (keep same filenames)
- Offered repository access or direct update service

**To Guido/Riccardo (ESRI Italy):**
- Explained architecture: Web Map on ArcGIS Online + custom interactivity via ArcGIS Maps SDK
- Clarified all data hosted on ESRI infrastructure
- Mentioned StoryMap in draft on platform

---

## TECHNICAL NOTES FOR FUTURE MAPS

### Data Preparation Workflow

1. **Receive raw data** (usually GPKG or Shapefile)
2. **Open in QGIS:**
   - Verify geometries visible
   - Check CRS (usually EPSG:3794 for Slovenia)
   - Run "Check Validity" if issues suspected
3. **Add required fields:**
   - Use Field Calculator or manual editing
   - Common fields: name, description, photo_url, type
4. **Clean and validate:**
   - Fix geometry errors
   - Remove unnecessary fields
   - Verify attribute values
5. **Export if needed:**
   - Reproject to Web Mercator (EPSG:3857) if ArcGIS Online has issues
   - Save as clean GeoPackage or Shapefile
6. **Upload to ArcGIS Online:**
   - Drag & drop into DravaBike folder
   - Configure metadata (title, tags, summary)
   - Set sharing to "Everyone" if public
7. **Style and configure:**
   - Add to Web Map
   - Configure symbology
   - Set up pop-ups if needed
   - Order layers appropriately

### Web App Customization Guide

**To modify hover behavior:**
- Edit `view.on("pointer-move", ...)` section
- Adjust `hoverDelay` variable (currently 300ms)

**To change side panel:**
- Modify `.side-panel` CSS
- Update `showSidePanel()` function
- Adjust panel width in styles

**To add/remove widgets:**
- Import widget from ArcGIS SDK
- Create widget instance
- Add to view: `view.ui.add(widget, position)`

**To change map extent:**
- Modify query in `natura2000Layer.queryExtent()` section
- Adjust padding: `result.extent.expand(1.1)` (1.1 = 10% padding)

### Common Issues & Solutions

**Issue:** Layer not displaying on ArcGIS Online
- **Solution:** Reproject to EPSG:3857 or EPSG:4326, re-upload

**Issue:** Pop-ups not showing images
- **Solution:** Verify photo_url field contains valid URLs, check GitHub hosting

**Issue:** Hover effect laggy
- **Solution:** Increase hoverDelay, optimize query performance

**Issue:** Web app not loading map
- **Solution:** Verify Web Map ID is correct, check CORS settings, ensure public sharing

---

## FILE LOCATIONS

### Original Data (from Barbara)
`/mnt/user-data/uploads/` (in conversation context)
- DRAVA_INTERPRETATION-LOCATIONS2.gpkg
- DRAVA_CYCLE-PATH.gpkg
- DRAVA_RIVER-POLYGON.gpkg
- DRAVA_RIVER-LINE.gpkg ⚠️
- DRAVA_NATURA-2000.gpkg
- DRAVA_PROTECTED-AREAS.gpkg
- DRAVA_RAILWAY-NETWORK.gpkg
- DRAVA_FEATURES-POINT_750.gpkg
- CUT-FRAME.gpkg

### Reference Documents
- A3_DRAVA_-_SLOVENIA_3.pdf (reference map)
- SLO_Drava_Bike_-_Outdoor_classroom.docx (content source)
- SLO_Bohinj_cycling_path.docx (related project)

### Generated Outputs
- GitHub repository: https://github.com/cyclingwaterways/drava-storymap
- ArcGIS Online folder: DravaBike (Politecnico account)

---

## LESSONS LEARNED

### What Worked Well
1. **GitHub Pages hosting** - Simple, free, reliable
2. **ArcGIS Maps SDK** - Excellent documentation, stable API
3. **Direct feature queries** - Solved attribute access issues
4. **Modular architecture** - Easy to update data vs. code separately
5. **Photo URL mapping** - Hardcoded mapping bypassed field parsing issues

### Challenges Encountered
1. **DRAVA_RIVER-LINE display** - Still unresolved
2. **hitTest() limitations** - Required queryFeatures() workaround
3. **Legend size** - Required multiple style adjustments
4. **File naming confusion** - index.html vs dravabike-webapp.html

### Best Practices Established
1. Always verify geometries in QGIS before uploading
2. Test layers individually on ArcGIS Online
3. Keep backups of original data
4. Document all custom code modifications
5. Use consistent naming conventions
6. Maintain detailed metadata for all layers

---

## NEXT PROJECTS PREPARATION

### Bohinj Cycling Path (Potential)
Similar structure possible:
- Different study area (Bohinj region)
- Same technical architecture
- Reuse web app template with new Web Map ID
- Different interpretation points and photos

### Scalability Notes
This approach can be replicated for:
- Other Interreg cycling routes
- Similar outdoor education projects
- Nature interpretation trails
- Cultural heritage routes

**Key Requirements:**
- GIS data in compatible format
- Photos for interpretation points
- Content for StoryMap narrative
- ArcGIS Online account for hosting

---

## CONTACT INFORMATION

**Project Lead:** Stefano (Politecnico di Milano)

**Partners:**
- Barbara (Slovenia) - Data provider, local coordination
- Guido/Riccardo (ESRI Italy) - Technical consultation
- Prof. Palma (Politecnico) - Project supervisor

**Technical Support:**
- ArcGIS Online: Politecnico account
- GitHub: cyclingwaterways account
- ESRI Documentation: https://developers.arcgis.com/

---

## VERSION HISTORY

**v1.0 (2026-03-27):**
- Initial web app implementation
- All layers uploaded to ArcGIS Online
- GitHub repository created
- Placeholder images generated
- Emails sent to partners

**Issues to track:**
- DRAVA_RIVER-LINE display problem
- Legend label updates needed
- Final photos pending
- Map 1 creation pending
- StoryMap assembly pending

---

## BACKUP STRATEGY

This document serves as complete project backup. Store in:
1. GitHub repository (cyclingwaterways/drava-storymap)
2. Local project folder
3. Cloud backup (Google Drive/OneDrive)
4. ArcGIS Online folder (as attachment or linked document)

**Update frequency:** After each major milestone or significant change

**Critical files to backup:**
- index.html (web app)
- All original GPKG files
- This documentation
- Email correspondence
- Screenshots of working configurations

---

*End of Documentation*

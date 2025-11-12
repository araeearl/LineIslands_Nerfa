Title
Line Islands Vegetation and Cloud-Masked NDVI Analysis (2009–2022)
Author
Allyson Earl
Platform
Google Earth Engine (JavaScript API)
Date
11/12/2025

1. Overview
This repository contains three Google Earth Engine (GEE) scripts that perform cloud and shadow masking, supervised land classification, and NDVI (Normalized Difference Vegetation Index) analysis for islands in the Line Islands chain of the Central Pacific:

- Flint Island (2009, 2021)
- Millennium Island (2009, 2021-Mar, 2021-Dec)
- Vostok Island (2010, 2022)

The goal is to create cloud-free NDVI composites for each year and island, enabling quantitative comparison of vegetation cover over time.

2. General Workflow
Each island script follows a similar structure, adapted for its specific imagery and data sources:

- Data Import
  Load satellite imagery and island boundary shapefiles from Earth Engine assets.
  Import manually delineated polygons identifying cloud, shadow, and clear regions for supervised training (when applicable).

- Visualization
  Apply consistent visualization parameters for natural-color (RGB) rendering.
  Add base imagery to the map for inspection.

- Clipping
  Clip each image to its respective island outline for focused analysis.

- Training Data and Classification (Flint & Millennium only; Vostok uses NDVI directly)
  Generate random sample points from hand-drawn polygons:
    0 = Clear
    1 = Cloud
    2 = Shadow
  Train a Random Forest classifier (500 trees) using spectral bands.
  Assess model accuracy using confusion matrices, overall accuracy, and kappa statistics.

- Mask Creation
  Apply the trained classifier to mask clouds and shadows, leaving only clear land pixels.

- NDVI Computation
  Compute NDVI for each image using band math: NDVI = (NIR - RED) / (NIR + RED)
  Add NDVI as a new image band.
  Visualize NDVI using a green–brown gradient to represent vegetation health.

- Export
  Export NDVI-masked imagery to Google Drive or Earth Engine Assets as GeoTIFFs.
  Default export resolution:
    - Flint: 1.2 m/pixel
    - Millennium: 1.2 m/pixel
    - Vostok: 2.0 m/pixel

3. Island-Specific Details
Flint Island (2009 & 2021)
  Assets: Flint_Mar092009, Flint_Sep282021, Flint_outline
  Classifier: Random Forest (500 trees), Bands: ["b1","b2","b3","b4"]
  Outputs: final_mask_NDVI_2009, final_mask_NDVI_2021
  Scale: 1.2 m

Millennium Island (2009, 2021-Mar, 2021-Dec)
  Assets: Mill_Mar212009, Mill_Mar092021, Mill_Dec152021, Millennium_outline
  Classifier: Random Forest (500 trees), Bands: ["b1","b2","b3","b4"]
  Outputs: final_mask_NDVI_mar21_2009, final_mask_NDVI_mar09_2021, final_mask_NDVI_dec15_2021
  Scale: 1.2 m

Vostok Island (2010 & 2022)
  Assets: Vos_Sep102010, vos_mar172022, Vos_outline
  Notes:
    - 2010 imagery uses 4 bands (b1–b4)
    - 2022 Maxar imagery includes 8 bands (b1–b8)
    - NDVI bands:
        2010: NIR = b4, Red = b3
        2022: NIR = b7, Red = b5
  Outputs: Vos_final_NDVI_2010, Vos_final_NDVI_2022
  Scale: 2 m

4. Input Summary
Island | Year(s) | Input Assets | Outline Asset | Training Data | Processing
Flint | 2009, 2021 | Flint_Mar092009, Flint_Sep282021 | Flint_outline | Yes | Classification + NDVI
Millennium | 2009, 2021 (Mar, Dec) | Mill_Mar212009, Mill_Mar092021, Mill_Dec152021 | Millennium_outline | Yes | Classification + NDVI
Vostok | 2010, 2022 | Vos_Sep102010, vos_mar172022 | Vos_outline | No | NDVI only

5. Output Summary
Output | Description | File Format | Recommended Use
final_mask_NDVI_* | Cloud- and shadow-masked NDVI composites | GeoTIFF | Vegetation analysis
classed_* | Land-cover classification rasters | GeoTIFF | QA/QC of cloud masking
NDVI maps | Visual NDVI renderings | Visualization only | Qualitative vegetation monitoring

6. Visualization Parameters
RGB Visualization: {bands: ["b3","b2","b1"], min: 0, max: 2000, gamma: 1.55}
NDVI Palette: ["FFFFFF","CE7E45","DF923D","F1B555","FCD163","99B718","74A901","054E0B"]

7. Notes and Recommendations
- Ensure all asset paths are accessible within your Earth Engine project before running.
- Classification accuracy depends on polygon quality and sample density.
- Run classifiers per image for best accuracy.
- Visually inspect NDVI results and adjust export scale for precision.
- NDVI ranges:
  - -1.0 to 0.0: Non-vegetated
  - 0.0 to 0.3: Sparse vegetation
  - 0.3 to 0.6: Moderate vegetation
  - >0.6: Dense vegetation
- Scripts are modular and can be toggled per section.

8. References
Google Earth Engine API: https://developers.google.com/earth-engine
NDVI Methodology: Rouse, J. W. et al. (1974). Monitoring Vegetation Systems in the Great Plains with ERTS.

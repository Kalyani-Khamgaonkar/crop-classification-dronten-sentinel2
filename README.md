# Crop Classification in Dronten, Flevoland — Sentinel-2 + Random Forest

Multi-temporal crop type classification of **4,088 agricultural parcels** in Dronten (Flevoland, Netherlands) using freely available Sentinel-2 L2A imagery and a Random Forest classifier. Achieves **82% overall test accuracy** across 9 crop classes.

## Problem

Manually identifying crop types across thousands of parcels is impractical at scale. This project builds an automated, reproducible pipeline for crop classification in one of the most intensively farmed regions in the Netherlands, using only free satellite data.

## Data

- **Imagery**: Sentinel-2 L2A monthly composites (March – October 2023), accessed via Google Earth Engine
- **Labels**: 4,088 field polygons with commercial crop annotations for Dronten, Flevoland
- **Cloud masking**: SCL-based pixel masking (classes 4, 5, 6, 7, 11)

## Method

1. Cloud-masked monthly composites generated in Google Earth Engine
2. Parcel-level zonal statistics computed with Rasterio and GeoPandas
3. Five spectral indices engineered per month: **NDVI, NDBI, NDWI, NDMI, EVI**
4. 35 total features capturing seasonal phenology across the growing season
5. Random Forest trained on 80/20 stratified split with k-fold cross-validation
6. Spatial error analysis using **Moran's I** and neighbour heterogeneity metrics

## Results

| Metric | Value |
|---|---|
| Overall test accuracy | 82% |
| Cereals & Grains (F1) | 0.92 |
| Fruit Trees (F1) | 0.89 |
| Potatoes & Root Vegetables (F1) | 0.88 |

**Key finding**: Spectral confusion between similar crops accounts for only ~3% of misclassifications. The majority of errors stem from class imbalance (minor classes like Legumes, Herbs & Spices, Oil & Fiber Crops). Moran's I analysis revealed spatial structure in the errors, suggesting field-level factors beyond spectral signature (e.g., rotation patterns, drainage) contribute to misclassification.

## Figures

![Confusion Matrix](figures/confusion_matrix.png)

![Classification Map](figures/classification_map.png)

## Tech stack

Python · Google Earth Engine · GeoPandas · Rasterio · scikit-learn · libpysal / esda · Matplotlib · Seaborn

## Repository structure

- `crop_classification_dronten.ipynb` — main notebook with the full pipeline
- `figures/` — output plots (confusion matrix, classification map)
- `requirements.txt` — Python dependencies
- `README.md` — this file

## How to run

1. Clone this repo
2. Install dependencies: `pip install -r requirements.txt`
3. Authenticate Earth Engine: `earthengine authenticate`
4. Place your parcel GeoPackage in `./data/` and update `CONFIG['GPKG_PATH']` in the notebook
5. Run the notebook cells sequentially

## Author

**Kalyani Khamgaonkar** — MSc Geo-Information Science & Earth Observation (GeoAI), University of Twente (ITC)

[LinkedIn](https://www.linkedin.com/in/kalyani-khamgaonkar-3205361b7/)

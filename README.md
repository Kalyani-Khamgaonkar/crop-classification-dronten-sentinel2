# Crop Classification in Dronten, Flevoland — Sentinel-2 + Random Forest

Multi-temporal crop type classification of **4,088 agricultural parcels** in Dronten (Flevoland, Netherlands) using freely available Sentinel-2 L2A imagery and a Random Forest classifier. Achieves **82% overall test accuracy** across 9 crop classes.

## Problem

Manually identifying crop types across thousands of parcels is impractical at scale. This project builds an automated, reproducible pipeline for crop classification in one of the most intensively farmed regions in the Netherlands, using only free satellite data.

## Data

- **Imagery**: Sentinel-2 L2A monthly composites (February – September 2023), accessed via Google Earth Engine
- **Labels**: 4,088 field polygons from EuroCrops v2.0 (derived from Dutch BRP/PDOK farmer declarations)
- **Cloud masking**: SCL-based pixel masking (classes 4, 5, 6, 7, 11) with a 95% scene threshold

## Method

1. Cloud-masked monthly composites generated in Google Earth Engine
2. Parcel-level zonal statistics computed with Rasterio and GeoPandas (mean reflectance per polygon)
3. Five spectral indices engineered per month: **NDVI, EVI, NDMI, NDBI, RE-NDVI**
4. 105 total features — 70 raw band reflectances + 35 derived indices across 7 months
5. Random Forest trained on pre-assigned 60/20/20 split with GridSearchCV (f1-macro scoring)
6. Spatial error analysis 

## Results

| Metric | Value |
|---|---|
| Overall test accuracy | **82.0%** |
| F1-macro (9 classes, equal weight) | 0.65 |
| Cohen's Kappa | 0.77 (substantial agreement) |
| Val–Test gap | 1.6% (no overfitting) |
| Cereals & Grains F1 | 0.92 |
| Fruit Trees F1 | 0.89 |
| Potatoes & Root Veg F1 | 0.88 |

**Key findings**:

- **RE_NDVI_Jul** emerged as the top feature — the red-edge band (705nm) separates broadleaf from narrow-leaf crops at peak canopy, where standard NDVI saturates
- Class imbalance drives the accuracy/F1-macro gap — Potatoes (1,107 parcels) vs Herbs & Spices (26) is a 43:1 ratio
- Moran's I analysis showed spatial structure in misclassifications, suggesting field-level factors (rotation patterns, drainage) contribute beyond spectral signature
- Spectral confusion between similar crops accounts for only ~3% of total errors

## Full presentation

A complete walkthrough of the methodology, feature engineering, hyperparameter tuning, and critical reflection is available in:

**[`Crop_Classification_Dronten.pdf`](Crop_Classification_Dronten.pdf)**

## Tech stack

Python · Google Earth Engine · GeoPandas · Rasterio · scikit-learn · libpysal / esda · Matplotlib · Seaborn

## Repository structure

- `crop_classification_dronten.ipynb` — main notebook with the full Random Forest pipeline
- `presentation/` — full methodology walkthrough (PDF)
- `requirements.txt` — Python dependencies
- `README.md` — this file

## How to run

1. Clone this repo
2. Install dependencies: `pip install -r requirements.txt`
3. Authenticate Earth Engine: `earthengine authenticate`
4. Place your parcel GeoPackage in `./data/` and update `CONFIG['GPKG_PATH']` in the notebook
5. Run the notebook cells sequentially

## Context

This project was carried out as part of the Machine Learning course at the University of Twente (ITC). The same study area, ground truth, and class labels were used by a 4-person group to compare four different ML approaches in parallel. This repository contains the **Random Forest — Sentinel-2** approach.

## Author

**Kalyani Khamgaonkar** — MSc Geo-Information Science & Earth Observation (GeoAI), University of Twente (ITC)

[LinkedIn](https://www.linkedin.com/in/kalyani-khamgaonkar-3205361b7/)

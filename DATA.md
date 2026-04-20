# Data Sources

This repository does **not** include the raw data due to licensing terms and file size constraints. To reproduce the analysis, the data must be obtained from its original sources as described below.

## 1. Ground Truth — EuroCrops v2.0

Parcel-level crop type labels for 4,088 fields in Dronten, derived from Dutch BRP (Basisregistratie Gewaspercelen) farmer declarations.

- **Source**: [EuroCrops project](https://www.eurocrops.tum.de/) — official releases on [Zenodo](https://zenodo.org/records/8229128)
- **Country subset**: Netherlands (NL), 2023
- **Spatial filter**: Dronten municipality, Flevoland Province
- **Attributes used**: parcel geometry, declared crop type (aggregated to 9 classes), pre-assigned train/val/test split
- **License**: Redistribution governed by EuroCrops terms and underlying BRP/PDOK licensing — please refer to the original source

## 2. Satellite Imagery — Sentinel-2 L2A

Multi-temporal multispectral imagery accessed on-the-fly through Google Earth Engine — **no manual download required**.

- **Collection**: `COPERNICUS/S2_SR_HARMONIZED`
- **Temporal extent**: February – September 2023 (7 monthly composites)
- **Spatial resolution**: 20 m (resampled)
- **CRS**: EPSG:32631 (UTM zone 31N)
- **Cloud masking**: Scene Classification Layer (SCL) — classes 4 (vegetation), 5 (not vegetated), 6 (water), 7 (unclassified), 11 (snow) retained
- **Compositing**: median per month at parcel level
- **Access requirement**: a free [Google Earth Engine](https://earthengine.google.com/) account with Python API authentication (`earthengine authenticate`)

The notebook pulls the imagery directly via the Earth Engine Python API and does not require any raster files to be pre-downloaded.

## 3. Expected Local Folder Structure

After obtaining the EuroCrops GeoPackage, place it in a `data/` folder at the project root:

- `data/commercial_crops_ml_ready.gpkg` — Dronten parcels from EuroCrops v2.0
- `data/S2_Dronten_2023/` — monthly Sentinel-2 composites will be cached here by the notebook (created automatically)

Update the path in the notebook's `CONFIG` dictionary if your setup differs:

```python
CONFIG = {
    'GPKG_PATH': "./data/commercial_crops_ml_ready.gpkg",
    'EXPORT_FOLDER': "./data/S2_Dronten_2023",
    'EXPORT_CRS': "EPSG:32631",
    ...
}
```

## 4. Reproducibility Notes

- **Stochasticity**: Random Forest results use `random_state=42` throughout for reproducibility
- **Split**: The 60/20/20 train/val/test split is **pre-assigned** in the EuroCrops GeoPackage itself — the notebook does not re-split, ensuring consistent comparison across different ML approaches used in the wider group project
- **Earth Engine quota**: Pulling 7 monthly composites for 4,088 parcels stays well within the free-tier GEE limits

## 5. Citation

If you use this data in your own work, please cite the original sources:

**EuroCrops**:
> Schneider, M., Schelte, T., Schmitz, F., & Körner, M. (2023). EuroCrops: The Largest Harmonized Open Crop Dataset Across the European Union. *Scientific Data*, 10, 612. https://doi.org/10.1038/s41597-023-02517-0

**Sentinel-2**:
> Copernicus Sentinel data (2023), processed by ESA. Accessed via Google Earth Engine.

---

*For questions about the data or the analysis, contact the repository author — see main [README](README.md).*

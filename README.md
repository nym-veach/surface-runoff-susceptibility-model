# Mapping Surface Runoff Susceptibility in the Nisqually River Watershed, WA

## Project Overview

This project maps surface runoff susceptibility within the Nisqually River watershed in Washington State. The goal is to identify where surface runoff is highest and evaluate how spatial patterns may change under future climate conditions.

Surface water runoff is directly tied to flooding, which is one of the most common and impactful natural hazards. In regions like the Puget Sound, climate change is expected to increase winter precipitation and extreme rainfall events, which can push stormwater systems beyond their designed capacity. This creates risks to human safety, infrastructure, water quality, and ecosystems such as salmon habitat.

This project was developed to better understand where runoff susceptibility is highest within the watershed and how those patterns may shift under future climate conditions. Mapping these patterns can support more proactive, data-driven decisions related to land use, infrastructure investment, and climate adaptation.

This work is intended as a screening-level tool for water resource analysts, environmental planners, scientists, and other environmental professionals.

## Data

This project uses multiple spatial datasets as drivers and inputs for surface runoff:

- **Watershed Boundary**: USGS Watershed Boundary Dataset (HUC8 – Nisqually River, WA)  
- **Climate**: MACAv2 daily downscaled climate data  
  - Wet season mean precipitation (December–February)  
  - Rx1day (maximum 1-day precipitation) as a proxy for storm intensity / extreme rainfall
  - Historical and future scenarios  
- **Topography**: NASA SRTM (30 m resolution)  
  - Elevation  
  - Derived slope and flow accumulation  
- **Soils**: NRCS gSSURGO  
  - Hydrologic soil groups (A–D), used to represent soil infiltration capacity  
- **Land Cover**: USGS NLCD  
  - Impervious surface percentage (30 m resolution)  

These datasets were selected to capture the main drivers of surface runoff, including precipitation, terrain, soil infiltration, and land surface characteristics.

## Workflow

Data prep and processing were completed to create input rasters for a future fuzzy logic model.  
Key steps included:

- Acquiring and organizing datasets for topography, climate, soils, and land cover  
- Clipping all datasets to the Nisqually watershed boundary  
- Reprojecting all spatial data to a common coordinate system (EPSG:5070)  
- Preparing impervious surface data from NLCD  
- Deriving terrain variables from the DEM, including slope and flow accumulation  
- Converting hydrologic soil groups (A–D) into a continuous runoff susceptibility score (0–1)  
- Processing climate data (MACA) to calculate:
  - Wet season mean precipitation  
  - Rx1day (extreme precipitation)  
  - for both historical and future scenarios  
- Harmonizing all raster datasets to a common grid (same CRS, resolution, extent, and shape)

Final outputs were saved as harmonized raster inputs for use in a surface runoff susceptibility model.

## Model Inputs

Final model inputs include:

- Slope  
- Flow accumulation  
- Soil runoff score (derived from hydrologic soil groups)  
- Wet season mean precipitation (historical and future)  
- Extreme precipitation (Rx1day; historical and future)  

## Future Work

These inputs will be integrated using a fuzzy logic approach to model relative surface runoff susceptibility. The model will compare historical and future conditions to identify areas of higher runoff risk and evaluate how susceptibility may shift under changing climate scenarios.

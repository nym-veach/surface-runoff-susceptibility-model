# Mapping Surface Runoff Susceptibility in the Nisqually River Watershed, Washington
## Using a Fuzzy Logic Model Under Historical and Future Climate Conditions

**Author:** Nym Griggs  
**Course:** Earth Data Analytics Capstone - University of Colorado Boulder  
**Science Mentor:** Dr. Lilly Jones  
**Date:** July 2026  

#### DOI & Data Release
[![DOI](https://zenodo.org/badge/1207999619.svg)](https://doi.org/10.5281/zenodo.19838492)

The project release is available through Zenodo. This release contains the project repository, environment configuration, and the input datasets used in the surface runoff susceptibility workflow.

## Project Overview

This project maps surface runoff susceptibility within the Nisqually River watershed in Washington State. The goal is to identify where surface runoff is highest and evaluate how spatial patterns may change under future climate conditions.

Surface water runoff is a precursor to flooding, which is one of the most common and impactful natural hazards. In regions like the Puget Sound, climate change is expected to increase winter precipitation and extreme rainfall events, which can push stormwater systems beyond their designed capacity. This creates risks to human safety, infrastructure, water quality, and ecosystems such as salmon habitat.

This project was developed to better understand where runoff susceptibility is highest within the watershed and how those patterns may shift under future climate conditions. Mapping these patterns can support more proactive, data-driven decisions related to land use, infrastructure investment, and climate adaptation.

The resulting susceptibility maps are intended to serve as a watershed-scale screening tool for identifying areas that may be more susceptible to surface runoff and how that susceptibility may shift with climate change. This fuzzy logic model was selected as it is less resource and data intensive than many physically based hydrologic models, supporting targeted resource allocation and prioritization of high-risk areas. It is not intended to replace detailed hydrologic modeling but rather offer a complementary tool to provide an accessible first-pass watershed-scale assessment when time, data, or computational resources are limited. It may be especially helpful for vulnerable communities without those resources.

**Research Questions**

1. Where is surface runoff susceptibility highest in the Nisqually River Watershed under historical conditions?
2. Do projected mid-century increases in wet-season precipitation and storm intensity redistribute that risk, or simply intensify it where it already exists?

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

### Data Download & Setup (Required Downloads)
Before running the notebook, download the required input datasets and place them in the appropriate project folders.

Run the notebook through Section 1.3 (Configure Project Directories) first - this automatically creates the folder structure below. Once created, place the downloaded datasets into their corresponding folders before continuing to run the rest of the notebook.

The SRTM Digital Elevation Model (DEM) and MACA climate datasets are accessed and downloaded programmatically within the notebook and do not require manual download.

Note: Although this workflow is configured for the Nisqually River Watershed (HUC8: 17110015), it could be adapted to other HUC8 watersheds with minor modifications.

#### 1. Watershed Boundary (USGS WBD)
- Source: https://www.usgs.gov/national-hydrography/watershed-boundary-dataset  
- Map Downloader Link: https://apps.nationalmap.gov/downloader/#/
- Download the HUC8 watershed boundary that includes the Nisqually River watershed (HUC8: 17110015)  
- Place files in: data/watershed-boundary-dataset/

#### 2. NLCD Impervious Surface
- Source: https://www.mrlc.gov/viewer/
- Product: NLCD Impervious Surface (30 m) for Study Area
- Place files in: data/nlcd-impervious/

#### 3. Soils (NRCS gSSURGO)
- Source: https://www.nrcs.usda.gov/resources/data-and-reports/gridded-soil-survey-geographic-gssurgo-database
- Download gSSURGO for Washington State (geodatabase)
- Place files in: data/soils/

All three datasets above (watershed boundary, NLCD impervious surface, and soils) are also included in the [Zenodo data release](https://doi.org/10.5281/zenodo.19838492) as a bundled alternative to downloading them individually from the sources above.

## Environment Setup

Create the project environment:

conda env create -f environment.yml

Activate the environment:

conda activate surface-runoff-susceptibility-model

## Workflow

To reproduce the analysis, run **`notebooks/01-runoff-fuzzy-logic-model.ipynb`** from top to bottom after setting up the environment and placing the data in the appropriate folders. The folder structure will set up the data folders for this. 

This notebook covers the full workflow:

- Acquiring and harmonizing input datasets (topography, climate, soils, land cover) to a common grid (EPSG:5070)
- Deriving terrain variables (slope, flow accumulation) and processing climate data (wet-season precipitation, Rx1day)
- Fuzzifying each input and combining runoff generation and concentration layers using a fuzzy gamma overlay (γ = 0.85)
      - Note: It is possible to test other gamma operator and tailor the breakpoints to data if applied to another watershed as the sigmoid testing steps have been left in the workflow so it may be adapted. 
- Producing the final historical, future, and change-in-susceptibility maps

## Methods

Surface runoff susceptibility was modeled using a fuzzy logic framework that integrates topographic, impervious surface, hydrologic soil group, and climate variables to estimate the relative likelihood of surface runoff generation and concentration across the Nisqually River Watershed. The topographic inputs such as slope were derived from a Digital Elevation Model (DEM) and flow accumulation was calculated using the `pysheds` [Python library](https://github.com/pysheds/pysheds). 

From the MACA climate dataset, mean wet-season (December–February) precipitation and Rx1day (maximum 1-day precipitation) were calculated. Rx1day was included as a proxy for storm intensity. Historical (1976–2005) and mid-century future (2041–2070) climate scenarios were analyzed using the Community Climate System Model Version 4 (CCSM4) under the Representative Concentration Pathway (RCP) 4.5 emissions scenario. CCSM4 was selected because its projections are representative of the average response across climate models for the region. The mid-century future period was chosen to balance capturing projected climate change impacts while remaining relevant for near- to medium-term planning and decision-making. Although this analysis uses CCSM4, the workflow can be readily adapted to other climate models available through the MACA dataset with minor modifications.

Gamma values ranging from 0.5 to 0.9 were evaluated during model development. The gamma parameter controls the balance between the fuzzy algebraic product (more conservative) and fuzzy algebraic sum (more optimistic). A gamma value of 0.85 was selected because it provided a balanced representation of runoff susceptibility while avoiding overly restrictive or overly permissive predictions.

Each model input was transformed into a standardized fuzzy membership value ranging from 0 to 1, where higher values indicate a greater contribution to runoff susceptibility. Runoff generation and runoff concentration variables were modeled separately to better represent hydrological conditions, before being combined to produce the final surface runoff susceptibility map. The fuzzified layers were then combined using a fuzzy gamma overlay (γ = 0.85). The final workflow produced maps of historical runoff susceptibility, projected future runoff susceptibility, and projected changes in runoff susceptibility.

## Results

The change in runoff susceptibility between climate scenarios was highest in urban, high-impervious areas, and lowest near Mount Rainier's upper slopes and a northeast region of the watershed, likely due to permeable soils in those areas and vegetation offsetting the steeper terrain. Under projected mid-century conditions, 69% of the watershed showed some change in susceptibility, and 93% of that change was an increase. Areas that were moderately susceptible saw the greatest change, especially in the central watershed. This suggests climate change is more likely to intensify existing risk than create new hotspots. See `reports/` for the full write-up and discussion.

## Next Steps

- Validate the model against observed runoff and flood data
- Compare results against a machine learning approach (e.g., Random Forest)
- Explore additional climate scenarios (e.g., RCP 8.5) or models beyond the ensemble mean
- Apply the workflow to other HUC8 watersheds

## Data Citations
- Abatzoglou, J. T., & Brown, T. J. (2012). A comparison of statistical downscaling methods suited for wildfire applications. International Journal of Climatology, 32(5), 772–780. https://doi.org/10.1002/joc.2313

- NASA Jet Propulsion Laboratory (JPL). (2013). NASA Shuttle Radar Topography Mission Global 1 arc second [Data set]. NASA Land Processes Distributed Active Archive Center. https://doi.org/10.5067/MEASURES/SRTM/SRTMGL1.003

- Soil Survey Staff. Gridded Soil Survey Geographic (gSSURGO) Database for Washington. United States Department of Agriculture, Natural Resources Conservation Service. Available online at https://gdg.sc.egov.usda.gov/. Accessed April 7, 2026.

- U.S. Geological Survey (USGS), 2024, Annual NLCD Collection 1 Science Products: U.S. Geological Survey data release, https://doi.org/10.5066/P94UXNTS

- U.S. Geological Survey. Watershed Boundary Dataset (WBD), 8-digit Hydrologic Unit Code 17110015 — Nisqually. National Geospatial Technical Operations Center, 2025.

## Disclaimer

This repository reflects independent academic work completed as part of my graduate capstone at CU Boulder. It was not conducted on behalf of, and does not represent the views of, my employer.

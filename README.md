# 🛰️ IAPS Risk Mapper — M1 Motorway Corridor

Satellite-based Invasive Alien Plant Species screening using Sentinel-2 and Random Forest classification.

**Live map:** https://siriuspm.github.io/IAPS-risk-mapper/outputs/index.html

Developed by **Ridwan Shittu** — Geospatial Analyst |
[linkedin.com/in/ridwan-shittu-e](https://linkedin.com/in/ridwan-shittu-e) |
[github.com/SiriusPM](https://github.com/SiriusPM)

---

## 📌 Executive Summary

Infrastructure operators in the UK, including National Highways, Network Rail,
and the Environment Agency, are legally obligated to manage Invasive Alien
Plant Species (IAPS) on their land. Traditional monitoring relies on manual
field surveys which are cost-prohibitive to deploy across entire networks.

This tool provides a digital triage layer. By analysing four years of
multispectral satellite imagery, it identifies 100-metre corridor segments
showing spectral and phenological signatures consistent with IAPS presence,
such as abnormal spring growth or rapid autumn die-back. Operators can
prioritise field resources by deploying ecologists to the highest-risk
locations first.

Without satellite pre-screening, surveying the full 120.3 km M1 corridor costs
between £60,000 and £240,600 per cycle at published field survey rates. This
tool reduces the area requiring active field investigation to 28.7 km — a 76%
reduction in survey area — by identifying where the satellite evidence is
strong enough to justify deploying an ecologist. The remaining 91.6 km moves
to desktop monitoring until the next annual screening cycle.

---

## ⚠️ The Problem

**The Cost of Damage:** The Environment Agency estimates that Japanese Knotweed
alone causes £166 million in damage per year in England and Wales.

**The Scale Challenge:** The Strategic Road Network managed by National Highways
covers 10,400km. The national rail network managed by Network Rail encompasses
approximately 32,000km of track.

**The Monitoring Gap:** Field ecology surveys typically cost between £500 and
£2,000 per kilometre. Due to these costs, most operators currently monitor
reactively, after a problem is reported, rather than proactively across the
whole network.

---

## 🛠️ The Technical Solution

The system uses a five-stage geospatial pipeline to convert raw satellite
pixels into operational priority tiers:

1. **Corridor pre-processing** — extraction of road geometries from OS
   OpenRoads data with 50m operational buffers segmented into 100m units
2. **Spectral stacking** — processing of Sentinel-2 Level-2A imagery in
   Google Earth Engine to calculate monthly composites of NDVI, NDRE, EVI,
   NDWI, and GLCM texture variance
3. **Phenological feature extraction** — extraction of timing features from
   the NDVI time series, including peak growth month, spring green-up rate,
   and autumn senescence rate, to distinguish IAPS from native vegetation
4. **Machine learning** — a Random Forest classifier trained on 2,216 NBN
   Atlas confirmed occurrence records, incorporating proximity to watercourses
   as a habitat context feature
5. **Output generation** — GeoJSON export and interactive Leaflet web map
   deployed to GitHub Pages

---

## 🔬 Key Finding

Autumn senescence rate and proximity to watercourses together account for 56%
of classifier feature importance. This suggests that temporal phenological
features and habitat context variables are more diagnostic than peak-season
spectral indices alone for IAPS detection in disturbed lowland corridor
landscapes.

---

## 💻 Technical Stack

| Tool | Purpose |
|---|---|
| Google Earth Engine (Python API) | Sentinel-2 processing, spectral indices |
| scikit-learn | Random Forest classifier |
| GeoPandas | Corridor geometry, spatial joins, GeoJSON export |
| Shapely | Buffering and segmentation |
| Rasterio | GeoTIFF reading and pixel extraction |
| SHAP | Feature importance explanation |
| PyProj | Coordinate system transformation |
| Leaflet.js | Interactive web map |

**Data sources:** Copernicus Sentinel-2, OS OpenRoads, OS Open Rivers,
NBN Atlas, CartoDB Dark Matter basemap


---

## ⚖️ Limitations

- **Resolution** — 10m pixel size. Cannot detect patches below approximately
  50 square metres. Field verification required for all P1 and P2 segments.
- **Spectral overlap** — a high risk score indicates elevated probability,
  not confirmed presence.
- **Training data** — 554 positive samples from NBN Atlas records. Coverage
  is uneven across the corridor.
- **Missing data** — September 2022 had zero cloud-free observations.
  Senescence rate could not be calculated for 37.9% of pixels.
- **Spatial generalisation** — spatial block cross-validation achieved 66.3%
  accuracy on the northern test block, reflecting landscape variation along
  the corridor.
- **This tool is a screening aid, not a survey.** Outputs do not constitute
  a formal ecological survey and should not be used as the sole basis for
  legal or management decisions.

---

````markdown
## 📂 Repository Structure and Reproducibility

The pipeline runs across five Jupyter notebooks, one per stage:

iaps-risk-mapper/
├── notebooks/
│   ├── 01_corridor_extraction.ipynb    — corridor geometry and segmentation
│   ├── 02_gee_processing.ipynb         — Sentinel-2 processing and index calculation
│   ├── 03_phenology_features.ipynb     — phenological feature extraction
│   ├── 04_classifier.ipynb             — Random Forest training and risk scoring
│   └── 05_output_generation.ipynb      — GeoJSON export and web map generation
├── data/
│   ├── corridor/       # M1 corridor GeoJSON files
│   ├── features/       # Pixel feature table and NDVI time series
│   └── reference/      # OS Open Rivers
├── outputs/
│   ├── index.html      # Leaflet web map (GitHub Pages)
│   └── m1_iaps_risk_segments.geojson
└── README.md
````

---


## 📄 Data Attribution

- Sentinel-2 imagery: European Space Agency Copernicus programme
- OS OpenRoads and OS Open Rivers: Ordnance Survey Open Data,
  Crown copyright and database right 2024
- NBN Atlas occurrence records: National Biodiversity Network,
  accessed via NBN Atlas API
- CartoDB Dark Matter basemap: CARTO, OpenStreetMap contributors



*Ridwan Shittu | May 2026
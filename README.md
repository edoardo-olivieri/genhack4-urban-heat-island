# genhack4-urban-heat-island
GenHack 4 (École Polytechnique): analysis of Urban Heat Island effect in Europe

## Overview

International hackathon organized by **École Polytechnique Paris** (November–December 2025).

**Challenge:** Quantify and explain discrepancies between ERA5-Land satellite data and ECAD ground stations — investigating whether the Urban Heat Island (UHI) effect explains why satellite temperature estimates differ from ground reality.

## Team
| Name | University | Github | LinkedIn |
|------|------------|--------|----------|
| Edoardo Olivieri | Politecnico di Milano | [GitHub](https://github.com/edoardo-olivieri) | [LinkedIn](https://www.linkedin.com/in/edoardo-olivieri-6b73a52a1) |
| Giacomo Fullin | Università Milano-Bicocca | [GitHub](https://github.com/giacomofullin18) | [LinkedIn](https://www.linkedin.com/in/giacomo-fullin-85b82b33b/) |
| Luca Iaria | Università Milano-Bicocca | [GitHub](https://github.com/lu01ca) | [LinkedIn](https://www.linkedin.com/in/luca-iaria-b7b49b23a/) |
| Paolo Portanova | Università Milano-Bicocca | [GitHub](https://github.com/paoloportanovacsn-sys) | |
| Francesca Verna | Università Milano-Bicocca | [GitHub](https://github.com/francescaverna) | [LinkedIn](https://www.linkedin.com/in/francesca-verna-b3584b316)


## Problem
Urban areas experience higher temperatures than surrounding rural areas due to dense construction, reduced vegetation, and altered radiative properties. This project investigates whether the lack of vegetation (UHI effect) explains systematic errors in satellite-based temperature estimates.

## Data Sources
| Source | Description | Resolution |
|--------|-------------|------------|
| **ECAD** | Ground-truth station observations | ~8,500 stations |
| **ERA5-Land** | Satellite model temperature | ~9 km |
| **Sentinel-2 NDVI** | Vegetation index | High-res |
| **GADM** | Administrative boundaries | - |

**Period:** Summer months (June–August) from 2020 to 2023

**Observations:** ~1.56 million station-day records from 4,416 European stations

## Methodology

### 1. Data Pipeline
- **Metadata Registry:** Filtered stations with valid data (2020–2023 coverage)
- **Coordinate Conversion:** DMS to decimal degrees for spatial matching
- **Vectorized Extraction:** Retrieved pixel values from NetCDF/GeoTIFF at station locations

### 2. Bias Definition
```
Bias = T_station - T_ERA5
```
Positive bias = station warmer than satellite estimate (potential UHI signal)

### 3. Statistical Models
The choice of linear and panel models was driven by the objective of interpretability and causal insight rather than pure predictive performance.

**OLS Regression (without intercept):**
```
Bias ~ Precipitation + Wind_Speed + NDVI + Month + Latitude + Longitude + Altitude
```
- R² ≈ 55% at European scale
- Key finding: Wind speed is the dominant cooling factor

**Panel Data Models:**
- **Fixed Effects (PanelOLS):** Entity effects for station-level heterogeneity
- **Random Effects (Mixed LM):** Hierarchical structure with station groups

### 4. Stratification Analysis
- Elevation-based stratification (threshold: 250m)
- Low-altitude vs. high-altitude regime comparison
- Spatial residual mapping to identify model limitations

## Key Results

| Driver | Effect | Interpretation |
|--------|--------|----------------|
| Wind Speed | Strong negative | Main UHI suppressor - ventilates urban boundary layer |
| Precipitation | Weak negative | Evaporative cooling during rain events |
| NDVI | Context-dependent | Sign changes between altitude regimes |
| Elevation | Complex | Different behavior in low vs. high altitude |

**Main findings:**
- ERA5-Land systematically underestimates urban summer maximum temperatures by 1–3°C
- Largest biases occur on hot, dry, low-wind days (stagnant conditions)
- Elevation stratification reveals regime-dependent coefficient behavior
- Mountainous regions show strongest residual patterns (model limitations)

## Repository Structure
```
├── README.md
├── Jupyter.ipynb          # Main analysis notebook (Google Colab)
└── Report.pdf             # Full methodological report with results and discussion
```

## Tools & Libraries
```python
pandas · numpy · xarray · rasterio · rioxarray · geopandas
statsmodels · linearmodels (PanelOLS) · scikit-learn
matplotlib · seaborn · contextily
```

## How to Run
1. Open notebook in Google Colab
2. Mount Google Drive with data folder
3. Run cells sequentially

**Note:** Original data provided by hackathon organizers (ERA5-Land NetCDF, ECAD station files, Sentinel-2 GeoTIFF).

## Limitations & Future Work
- Linear model cannot capture non-linearities and interaction effects
- Missing covariates: urban fraction, building height, distance to coast
- Spatial autocorrelation not fully addressed
- Potential extensions: geographically weighted regression, gradient boosting, spatial autoregressive models

## Links
- [GenHack 4 Official Page](https://www.polytechnique.edu/en/genhack-4-hackathon-generative-modelling)

---
*Code developed during a 4-week hackathon. Focus was on methodology and insights rather than production-ready code.*

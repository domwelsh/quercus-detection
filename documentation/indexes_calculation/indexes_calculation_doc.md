# Detailed Workflow of the Notebooks in `notebooks\Index_calculation_planet_scope`

## Introduction
The purpose of these scripts is to integrate cloud services, specifically **Google Cloud Storage (GC)** and **Google Earth Engine (GEE)**, to facilitate the processing and analysis of satellite data. This architecture provides scalability, efficient handling of large datasets, and advanced computational capabilities, making it ideal for remote sensing and geospatial analysis projects.

- [Google Cloud Console (Storage)](https://console.cloud.google.com/storage/browser/)
- [Google Earth Engine Code Editor](https://code.earthengine.google.com/) (Python libraries are used for programing access)

The process of create a bucket in GC creates a easy way to work with GEE, for avoid hard computational proceses in the local machines for the indexes calculations and export the images to Google Drive, that is linked with Google Colaboratory. 

For more details of how to create a bucket in GC see: [Google Cloud creation of a bucket](https://cloud.google.com/storage/docs/creating-buckets)

---

## Workflow of Script 1: Uploading Images from Google Cloud to Google Earth Engine

### 1. Initial Setup
- **Key Libraries:**
  - `google-cloud-storage`: Access to data stored in Google Cloud Storage.
  - `earthengine-api`: Interaction with Google Earth Engine.
- **Authentication:**
  - Authenticates with credentials for Google Cloud and Google Earth Engine.
  - Ensures secure and controlled access to resources.
- **Required Input:**
  - Name of the Google Cloud Storage bucket.
  - Destination collection name in GEE.

### 2. Connecting to Google Cloud Storage
- The script connects to the specified bucket using `google-cloud-storage`.
- Lists and filters files in the bucket based on required formats (e.g., `.tif`, `.geotiff`).

### 3. Image Validation
- **File Processing:**
  - Ensures files are in the expected format and ready for processing in GEE.
  - Converts files to a compatible format using libraries like `rasterio` if needed.
- **Justification:**
  - GC serves as a reliable repository for massive remote sensing data. It allows for high-resolution image storage and efficient access with low latency.

### 4. Uploading to Google Earth Engine
- Configures parameters such as:
  - **Collection ID**: The name of the target collection in GEE.
  - **Metadata**: Additional information like region and spatial resolution.
- Uploads each file to the GEE collection using the Earth Engine API.

### 5. Verification
- Performs queries in GEE to confirm that the images were uploaded successfully.
- **Example Output:**
  - Displays the names of the uploaded images and their details in the console.

---

## Workflow of Script 2: Index Calculation and Download

### 1. Initial Setup
- **Key Libraries:**
  - `earthengine-api`: Access to satellite data and geospatial calculations.
  - `google-auth`: Authentication for Google APIs.
- **Authentication:**
  - Authenticates with GEE.
- **Required Input:**
  - Region of interest, date range, and type of index to calculate.

### 2. Retrieving Data from GEE
- **Dataset Query:**
  - Selects satellite data collections (e.g., Sentinel-2 or Landsat).
  - Filters images based on defined parameters (date, region, quality).
- **Justification:**
  - GEE provides the serves for the process of the index calculations in the all collections. 

### 3. Index Calculation
- Functions are defined to calculate remote sensing indices, such as:

| **Index**                                | **Equation**                                               | **Source**                           |
|------------------------------------------|-----------------------------------------------------------|---------------------------------------|
| Difference Vegetation Index (DVI)        | NIR - RED                                                 | (Huang et al., 2023)                 |
| Atmospherically Resistant Vegetation Index (ARVI) | ((NIR - RED - γ (RED - BLUE)) / (NIR + RED - γ (RED - BLUE))) | (Liu et al., 2004)                   |
| Enhanced Vegetation Index (EVI)          | G * ((NIR - RED) / (NIR + C1 * RED - C2 * BLUE + L))      | (Mo et al., 2012)                    |
| GCVI (Green Chlorophyll Vegetation Index) | NIR / GREEN - 1                                           | (Gitelson et al., 2003)              |
| GNDVI (Green Normalized Difference Vegetation Index) | ((NIR - GREEN) / (NIR + GREEN))                          | (Gitelson et al., 1996)              |
| GRVI (Green Ratio Vegetation Index)      | NIR / GREEN                                               | (Sripada et al., 2006)               |
| NDVI (Normalized Difference Vegetation Index) | ((NIR - RED) / (NIR + RED))                              | (Rouse et al., 1974)                 |
| NDWI (Normalized Difference Water Index) | ((GREEN - NIR) / (GREEN + NIR))                          | (Gao, 1996)                          |
| SAVI (Soil Adjusted Vegetation Index)    | ((NIR - RED) / (NIR + RED + 0.5) × (1 + 0.5))            | (Huete, 1988)                        |
| SR (Simple Ratio)*                       | NIR / RED                                                 | (Jordan, 1969)                       |
| VARI (Visible Atmospherically Resistant Index) | ((GREEN - RED) / (GREEN + RED - BLUE))                  | (Gitelson et al., 2002)              |

*The literature also defines this as VIN (Vegetation Index Number) or RVI (Ratio Vegetation Index).*

- **Use of GEE:**
  - Calculations are performed directly on GEE servers, avoiding the need to process large data volumes locally.

### 4. Exporting Results
- Configures data export to Google Drive.
- Exports calculated indices in raster format (GeoTIFF).

### 5. Local Download
- Downloads exported files from Google Drive.


---

## Justification for Using Google Cloud and Google Earth Engine

### 1. Google Cloud Storage (GC)
- **Advantages:**
  - Scalable storage for large data volumes.
  - High availability and redundancy ensure data integrity.
  - Native integration with other Google tools.
- **Use Case:**
  - Acts as the initial repository for satellite images, enabling an organized and accessible workflow.

### 2. Google Earth Engine (GEE)
- **Advantages:**
  - Access to vast collections of satellite data. 
  - Advanced geospatial processing capabilities in the cloud, removing the need for local infrastructure.
  - Powerful API for complex index calculations without downloading massive datasets.
- **Use Case:**
  - Enables efficient calculation of indices like NDVI and exports processed results to Google Drive.

---

## Conclusion
This workflow leverages the storage capabilities of **Google Cloud Storage** and the computational power of **Google Earth Engine** to efficiently handle satellite data. It is ideal for projects that require:
1. Large-scale data processing.
2. Repetitive calculations (e.g., vegetation indices) over extensive or many tiles areas.
3. Scalable and exportable results for use in local systems or other platforms.

## References

- Barnes, E., Clarke, T., Richards, S., Colaizzi, P., Haberland, J., Kostrzewski, M., Waller, P., Choi, C., Riley, E., Thompson, T., Lascano, R. J., Li, H., & Moran, M. (2000). Coincident detection of crop water stress, nitrogen status and canopy density using ground-based multispectral data.

- Gao, B. C. (1996). NDWI—A normalized difference water index for remote sensing of vegetation liquid water from space. *Remote Sensing of Environment, 58*(3), 257–266. https://doi.org/10.1016/S0034-4257(96)00067-3

- Gitelson, A. A., Kaufman, Y. J., Stark, R., & Rundquist, D. (2002). Novel algorithms for remote estimation of vegetation fraction. *Remote Sensing of Environment, 80*(1), 76–87. https://doi.org/10.1016/S0034-4257(01)00289-9

- Gitelson, A. A., Merzlyak, M. N., & Lichtenthaler, H. K. (1996). Detection of Red Edge Position and Chlorophyll Content by Reflectance Measurements Near 700 nm. *Journal of Plant Physiology, 148*(3–4), 501–508. https://doi.org/10.1016/S0176-1617(96)80285-9

- Gitelson, A. A., Vina, A., Arkebauer, T. J., Rundquist, D. C., Keydan, G., & Leavitt, B. (2003). Remote estimation of leaf area index and green leaf biomass in maize canopies. *Geophysical Research Letters, 30*(5). https://doi.org/10.1029/2002GL016450

- Gitelson, A., & Merzlyak, M. (1994). Spectral Reflectance Changes Associated with Autumn Senescence of *Aesculus hippocastanum* L. and *Acer platanoides* L. Leaves. Spectral Features and Relation to Chlorophyll Estimation. *Journal of Plant Physiology, 143*(3), 286–292. https://doi.org/10.1016/S0176-1617(11)81633-0

- Huang, Z., Zhong, L., Zhao, F., Wu, J., Tang, H., Lv, Z., Xu, B., Zhou, L., Sun, R., & Meng, R. (2023). A spectral-temporal constrained deep learning method for tree species mapping of plantation forests using time series Sentinel-2 imagery. *ISPRS Journal of Photogrammetry and Remote Sensing, 204*, 397–420. https://doi.org/10.1016/J.ISPRSJPRS.2023.09.009

- Huete, A. R. (1988). A soil-adjusted vegetation index (SAVI). *Remote Sensing of Environment, 25*(3), 295–309. https://doi.org/10.1016/0034-4257(88)90106-X

- Jordan, C. F. (1969). Derivation of Leaf-Area Index from Quality of Light on the Forest Floor. *Ecology, 50*(4), 663–666. https://doi.org/10.2307/1936256

- Liu, G. R., Liang, C. K., Kuo, T. H., Lin, T. H., & Huang, S. J. (2004). Comparison of the NDVI, ARVI and AFRI vegetation index, along with their relations with the AOD using SPOT 4 vegetation data. *Terrestrial, Atmospheric and Oceanic Sciences, 15*(1), 15–31. https://doi.org/10.3319/tao.2004.15.1.15(a)

- Mo, D., Yan, E., Lin, H., Sun, H., Li, J., & Zhang, G. (2012). Development and validation of 2-band EVI with MODIS data in Southeast China. *Proceedings of 2012 International Conference on Measurement, Information and Control (MIC)*, 1, 88–91. https://doi.org/10.1109/MIC.2012.6273306

- Rouse, J. W., Jr., Haas, R. H., Schell, J. A., & Deering, D. W. (1974). Monitoring vegetation systems in the Great Plains with ERTS. *NASA. Goddard Space Flight Center, 3rd ERTS-1 Symposium, Vol. 1, Section A.*

- Sripada, R. P., Heiniger, R. W., White, J. G., & Meijer, A. D. (2006). Aerial color infrared photography for determining early in-season nitrogen requirements in corn. *Agronomy Journal, 98*(4), 968–977. https://doi.org/10.2134/AGRONJ2005.0200


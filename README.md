# RemoteSens_INdices

RemoteSens_INdices is written in python and used to calculate different indices like NDVI, NDWI, NDMI, SAVI, EVI etc. for satellite imagery data.

## Prerequisites

Before running this code in your system you have to install `numpy` and `rasterio` library in your system. To install the `numpy` library follow this [link](https://numpy.org/install/). 

Geographic information systems use GeoTIFF and other formats to organize and store gridded raster datasets such as satellite imagery and terrain models. `rasterio` reads and writes these formats and provides a Python API based on `Numpy` N-dimensional arrays and `GeoJSON`. To install the `rasterio`, [clik here.](https://rasterio.readthedocs.io/en/stable/installation.html)

## RemoteSens_INdices
Now we will breakdown and see how to execute the code: 

```bash
import numpy as np
import rasterio
```
These are import statements. The `numpy` library is imported with the alias `np`, and the `rasterio` library is imported. These libraries will be used to perform mathematical operations and read/write geospatial raster data, respectively.
```bash
input_file = r'E:/Python_Tutorial/Indices/Sentinel-2_Bardhaman.tif'
```
This line defines the path to the input raster file, which is a Sentinel-2 image. You replace the path as per your desired location.

```bash
ndvi_file = r'E:/Python_Tutorial/Indices/ndvi.tif'
ndwi_file = r'E:/Python_Tutorial/Indices/ndwi.tif'
ndmi_file = r'E:/Python_Tutorial/Indices/ndmi.tif'
evi_file = r'E:/Python_Tutorial/Indices/evi.tif'
savi_file =r'E:/Python_Tutorial/Indices/savi.tif'
```
These lines define the output file paths for the various indices that will be calculated. Five indices are being calculated: **NDVI**, **NDWI**, **NDMI**, **EVI**, and **SAVI**. For each index, a separate output file is created.

 ```bash
with rasterio.open(input_file) as src:
    data = src.read()
    profile = src.profile
    profile.update(count=1)
```
Opening the input file using `rasterio`, reading the data, and storing it in a `numpy` array data. The metadata of the input file is stored in a dictionary called `profile`, and the `count` parameter is updated to 1 to indicate that the output files will have a single band.

 ```bash
red = data[2].astype(np.float32)
nir = data[3].astype(np.float32)
ndvi = (nir - red) / (nir + red)
with rasterio.open(ndvi_file, 'w', **profile) as dst:
    dst.write(ndvi, 1)
```
This block of code calculates the `NDVI` (Normalized Difference Vegetation Index) using the `red` and `nir` bands from the input raster data. The red band is at index 2 of the data array, and the nir band is at index 3. The `astype` method is used to convert the data type of the `red` and `nir` arrays to `np.float32`, which is necessary for performing mathematical operations on them. The `NDVI` formula is then applied to the `red` and `nir` arrays, and the resulting `NDVI` values are written to a new raster file using `rasterio`.

```bash
green = data[1].astype(np.float32)
ndwi = (green - nir) / (green + nir)
with rasterio.open(ndwi_file, 'w', **profile) as dst:
    dst.write(ndwi, 1)
```
The first line loads the `green` band data from the `Sentinel-2` image into a `numpy` array and converts the data type to 32-bit float by using `np.float32`.
The second line calculates the `NDWI` by subtracting the `near-infrared (NIR)` band data from the `green` band data and then dividing the result by the sum of the green band data and the NIR band data. The third line creates a new GeoTIFF file using `rasterio.open()` and writes the NDWI data to it with `dst.write()`.

```bash
swir = data[4].astype(np.float32)
ndmi = (nir - swir) / (nir + swir)
with rasterio.open(ndmi_file, 'w', **profile) as dst:
    dst.write(ndmi, 1)
```
Same as the previous lines of code. Using `SWIR` band for calculate `NDMI`.

```bash
blue = data[0].astype(np.float32)
evi = 2.5 * ((nir - red) / (nir + 6 * red - 7.5 * blue + 1))
with rasterio.open(evi_file, 'w', **profile) as dst:
    dst.write(evi, 1)
```
the first step is to extract the `Blue` band from the input data and convert it to float32 data type. Next, the `EVI` formula is applied using the extracted bands (Red, Near Infrared, and Blue) to compute a vegetation index that is sensitive to changes in vegetation density and structure. This formula multiplies the difference between the `NIR` and `Red` bands by a constant `(2.5)` and then divides the result by a combination of the `NIR`, `Red`, and `Blue` bands. The combination of bands in the denominator is designed to minimize the effects of atmospheric interference and improve the sensitivity of the `EVI` to vegetation density. Finally, the resulting `EVI` raster is written to a file using the `Rasterio` library. The output file is specified by the variable `evi_file`, and the file format and metadata are specified by the profile variable.

```bash
L = 0.5  # the soil adjustment factor
savi = ((nir - red) / (nir + red + L)) * (1 + L)
with rasterio.open(savi_file, 'w', **profile) as dst:
    dst.write(savi, 1)
```
The first step is to set the soil adjustment factor `L` to a value of `0.5`. Next, the `SAVI` formula is applied using the extracted bands (Red and Near Infrared) to compute a vegetation index that is sensitive to changes in vegetation density while correcting for the effects of soil background. This formula multiplies the difference between the `NIR` and `Red` bands by a factor that corrects for soil background `(1 + L)` and then divides the result by the sum of the `NIR` and `Red` bands plus the `soil adjustment factor (L)` . The soil adjustment factor helps to account for the effect of soil reflectance on the vegetation index. Finally, the resulting `SAVI` raster is written to a file using the `Rasterio` library. The output file is specified by the variable `savi_file`, and the file format and metadata are specified by the `profile` variable.

**Here is the full code:**
```bash
import numpy as np
import rasterio

# Define the input file path
input_file = r'E:/Python_Tutorial/Indices/Sentinel-2_Bardhaman.tif'

# Define the output file paths
ndvi_file = r'E:/Python_Tutorial/Indices/ndvi.tif'
ndwi_file = r'E:/Python_Tutorial/Indices/ndwi.tif'
ndmi_file = r'E:/Python_Tutorial/Indices/ndmi.tif'
evi_file = r'E:/Python_Tutorial/Indices/evi.tif'
savi_file =r'E:/Python_Tutorial/Indices/savi.tif'

# Load the data into a numpy array
with rasterio.open(input_file) as src:
    data = src.read()
    profile = src.profile
    profile.update(count=1)
    
# Calculate NDVI
red = data[2].astype(np.float32)
nir = data[3].astype(np.float32)
ndvi = (nir - red) / (nir + red)
with rasterio.open(ndvi_file, 'w', **profile) as dst:
    dst.write(ndvi, 1)
    
# Calculate NDWI
green = data[1].astype(np.float32)
ndwi = (green - nir) / (green + nir)
with rasterio.open(ndwi_file, 'w', **profile) as dst:
    dst.write(ndwi, 1)
    
# Calculate NDMI
swir = data[4].astype(np.float32)
ndmi = (nir - swir) / (nir + swir)
with rasterio.open(ndmi_file, 'w', **profile) as dst:
    dst.write(ndmi, 1)
    
# Calculate EVI
blue = data[0].astype(np.float32)
evi = 2.5 * ((nir - red) / (nir + 6 * red - 7.5 * blue + 1))
with rasterio.open(evi_file, 'w', **profile) as dst:
    dst.write(evi, 1)

# Calculate SAVI
L = 0.5  # the soil adjustment factor
savi = ((nir - red) / (nir + red + L)) * (1 + L)
with rasterio.open(savi_file, 'w', **profile) as dst:
    dst.write(savi, 1)
```


# Benefit of using RemoteSens_INdices

Calculating multiple vegetation indices (such as NDVI, NDWI, NDMI, EVI, SAVI) in a single time using Python can provide several benefits for remote sensing analysis:

1. **Efficiency**: By processing multiple vegetation indices in a single script, you can save time and processing power by avoiding redundant calculations and loading data into memory multiple times.

2. **Comparative analysis**: Having multiple vegetation indices calculated in one script allows for a more comprehensive analysis of vegetation conditions, which can help in comparative studies and change detection analysis.

3. **Diverse data sources**: Different vegetation indices respond to different vegetation characteristics, allowing for a more diverse set of data to be used in remote sensing analysis. With Python, you can calculate all of these indices from a single dataset, enabling a more holistic analysis.

4. **Visualization**: Python has many tools for data visualization, and having multiple vegetation indices calculated in a single script allows for easy visualization and comparison of the different indices.

5. **Flexibility**: Python is a flexible programming language that can be used to automate and customize workflows for remote sensing analysis. By processing multiple vegetation indices in one script, you can easily modify the workflow to suit your research question and the properties of the input data.

In summary, calculating multiple vegetation indices in a single Python script can help to save time, provide a more comprehensive analysis, enable the use of diverse data sources, facilitate visualization, and offer flexibility in workflow customization

## Contact ME
* Email: koushikghosh1a2s@gmail.com.

* LinkedIn: [Koushik Ghosh](https://www.linkedin.com/in/koushik-ghosh-490761192/)

* ResearchGate: [Koushik Ghosh](https://www.researchgate.net/profile/Koushik-Ghosh-23)




# USDA GEMS TASK1: Creating a Phenotype Pipeline For Ground Cover

## Overview

The purpose of this repository is to create a pipeline for processing images to find clover ground cover in a 2021 USDA Oat Study. A series of Jupyter Notebooks (Ananconda) are used to perform this task.   


## Data 
RGB JPEGS taken ~8ft above ground from a USDA 2021 Oat study. 120 plots taken over 7 time points. All data and programs will be stored on an external hard drive.




## Environment
The Anaconda environment was "ag_env" and used several installed image processing packages including OpenCV and Scikit-Image.


## Notebooks

The following diagram shows the flow process for each notebook. 

### Rename Image

This notebook copies all images to new folders and save the outputs as a TIFF...

### Crop Images

This notebook crops each image to the center 1/9th and  changes the output to show the date after the image.

(Include Crop math)

### Vegetation Index

This notebook perfroms various vegetation indices on the crop images and saves it to a new output. 

(Include VI math)


### Otsu's Threshold

This notebook performs Otsu's Threshold on each image VI. It also records plant ground cover. 


(Include method code)


### Time Series

This notebook creates a time series of the VI and Otsu's method.








!["Figure 1: Data Flow Diagram"](https://github.com/LRosen656/USDA_GEMS_RGB_COVER/blob/main/USDA%20Task%201_higherarchy.png)



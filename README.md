# USDA GEMS TASK1: Creating a Phenotype Pipeline For Ground Cover

## Overview

The purpose of this repository is to create a pipeline for processing images to find clover ground cover in a 2021 USDA Oat Study. A series of Jupyter Notebooks (Ananconda) are used to perform this task.   


## Data 
RGB JPEGS taken ~8ft above ground from a USDA 2021 Oat study. 120 plots taken over 7 time points. All data and programs was stored on an external hard drive.




## Environment
The Anaconda environment was "ag_env" and used several installed image processing packages including OpenCV and Scikit-Image (see requirements.txt).


## Notebooks

The following diagram shows the flow process for each notebook. 

!["Figure 1: Data Flow Diagram"](https://github.com/LRosen656/USDA_GEMS_RGB_COVER/blob/main/USDA_Task_1_Simplified.png)

### Rename Image


This notebook renames the images taken from an oat study over the summer of 2021. A for loop was used for each date directory to copy the image to a new date directory and labeled with the correct plot number and row. Date 7/22 was skipped because it just had soybean images. Date 7/8 was already labeled so it just needed to be copied. 


### Crop Images

This Notebook uses openCV to crop the plot images made in "Rename_Images.ipynb" and saves all dates to a new directory. It also relables the images as "Plot#_YYYYMMDD" so that the date follows the image as four-digit year and converts the file format from a jpeg to a tiff to avoid compression.

First, the output name is created by taking the date of the folder and splitting it to year, month, day. Next, the image is open using OpenCV. The image is then cut to a square and recentered depending on if image taller than wide or vice versa. Another offset is added based on where noninterest objects (e.g. the cart bar) are in the image. These exception offsets were found experimentally by looking at images without offsets and meauring the cart bar.

Square image (offset)= (taller - smaller)/2

cropping amount = image/3 

avoidance (exception) = manual input

final image = [Height_Offset + Height_Crop + Height_Exception: Height - Height_Offset + Height_Crop + Height_Exception, 
				Width_Offset + Width_Crop + Width_Exception: Width - Width_Offset + Width_Crop + Width_Exception] 



### Vegetation Index

This notebook takes the prepared images from "Crop Image" and the dataframe from "Dataframe_Mean_Green" to create four vegetation indicies for each image. The indicies used are Excess Green (ExG), Excess Green minus Red (ExGR), Green Leaf Index (GLI), and Visible Atmospherically Resistant Index (VARI). First Index directories are created (if they don't already exist). Then Each index is defined:

ExG = 2Green(n) - Red(n) - Blue(n)

ExGR = 3Green(n) - 2.4Red(n) - Blue(n)

GLI = 2Green(c) - Red(c) - Blue(c)/2Green(c) + Red(c) + Blue(c)

VARI = Green(c) - Red(c)/Green(c) + Red(c) - Blue(c)

Where Band(c) is the extracted band from the image ranging from 0-255 and Band(n) is a normalized version of that band defined as:

Band(n) = Band(c)/Red(c) + Green(c) + Blue(c)

The outputs collect the indicies to their respective outputs and the mean values of each image index.

A few notes: first, all the color bands were converted from dtype 'uint8' to a float so that the math would work. Second any invaid value (e.g. 0/0) was redefined as 0.


### Otsu's Threshold

#### Local
This notebook uses the vegetation indicies created in '1.0_Vegetation_Index.ipynb' and the dataframe created in '0.3_DataFrame_Mean_Green.ipynb' to classify images as vegetation or non-vegetation. First, the dataframe is uploaded and used to call the images in each vegetation index. Finally Otsu's Threshold from Scikit Image will be used to reclassify the images. 



    Thresh = threshold_otsu(img)                  Threshold (number)
    classified = np.where(img>Thresh, 1, 0)       Reclassifies everything greater than thresh as 1 else zero


#### Global
This notebook uses the vegetation indicies created in '1.0_Vegetation_Index.ipynb' and the dataframe created in '0.3_DataFrame_Mean_Green.ipynb' to get Otsu's Threshold value for each index globally. First, the dataframe is uploaded and used to call the images in each vegetation index. Then, Numpy is used to used to append and flatten the array for each index. Finally Otsu's Threshold from Scikit Image is used on the large matrix and output number is appended to the dataframe.


        Index = np.append(Index, img.flatten())
         Thresh = threshold_otsu(Index) 


### Zero Threshold

This notebook uses the vegetation indicies created in '1.0_Vegetation_Index.ipynb' and the dataframe created in '0.3_DataFrame_Mean_Green.ipynb' to classify images as vegetation or non-vegetation. First, the dataframe is uploaded and used to call the images in each vegetation index. Finally a zero will be used to reclassify the images. 


 classified = np.where(img>0, 1, 0)



### Ground Truthing

This notebook ground truths the classified images made in several previous notebooks by user input to the reference image (i.e inital image). First, the reference and each classified image are called. Then the user inputs a number (0 for nonvegetation and 1 for vegetation) based on 30 randomly sampled points represented by a red x.

Eight images were randomly selected per date for a total of 56 images.

### Accuracy Assessment

This notebook uses the spreadsheets created in "Ground_Truth.ipynb" to find accuracy of each image, index and threshold. First, the overall accuracy from Scikit Learn will be collected from each image and exported to a spreadsheed. Then, a confusion maxrix will be made for each threshold and the overall, precision, and recall accuracy will be collected and exported to spreadsheet.

### Percent Vegetation

This notebook uses ExGR-Zero-Threshold created in "2.2_Otsu_Thresh_Zero.ipynb", the prepared images ceated in "0.2_Image_Crop.ipynb", and the vegetation indicies created in "1.0_Vegetation_Index.ipynb" to summarize vegetation cover. The reason why ExGR-Zero-Threshold is used is because it had the highest overall accuracy. First, a dataframe is created using "Oat_Data.csv". Then, percent vegetation is found by taking the average of each classified image (total vegetation/ area). The normalized green is found from the prepared images and clipped to the classified vegetation. EXG, EXGR, GLI, and VARI indicies are also clipped to the classified vegetation. Finally, the average is found by taking the sum of the value and dividing it by the sum of classified vegetation.

Percent Vegetation = Classified Vegetation/Area
# identify_glomerili_in_human_kidney_tissue_images
The challenge is to detect functional tissue units (FTUs) across different tissue preparation pipelines. An FTU is defined as a “three-dimensional block of cells centered around a capillary, such that each cell in this block is within diffusion distance from any other cell in the same block” (de Bono, 2013). The goal is the implementation of a successful and robust glomeruli FTU detector.

This is a semantic segmentation task to identify glomeruli in a given kidney tissue image. This is done as a part of a Kaggle competition. The provided images were really large images with average size is equal to 25 x 30 (k). Such image does not fit in the memory for processing. However one image contained a large number of gromeruli and by tiling the large image in to small set of images created a reasonably managable and representative training and testing image data sets. Therefore the solution is mainly consist of two steps.

Example of original iamge and its mask (31299 x 44066 pixels)
![image](https://user-images.githubusercontent.com/74288317/141694937-c3ab106f-2df2-43b4-9f43-af09a3490db5.png)


Step 01: Create tiled images datasets
=====================================
- Install a library called patchify to tile large images into small parts
- Make two folders to hold images and masks
- Make a list of tiff image file names in a given floder
- Read tiff files one by one (each file differ by number of channels and where the color chanel is located on)
- Use patchify to break the image to 4000 x 4000 tiles, do this by every 2000 pixels
- Read the corresponding mask for the image given by json specification file
- Generate mask tiles using patchify as per the same parameters
- For each image and mask tile calculate the percentage of the mask covered in the whole image
- Select all images where the mask is between 10-90% comapred the whole image
- Resize each image and mask to 400 x 400
- Rename those in a way that it can be identified uniquely using the row and column in which those are located in the original image
- Save the resized image and mask in the corresponding folder

Example of image and its corresponding mask saved in the disk
![image](https://user-images.githubusercontent.com/74288317/141658128-1e1e29fd-e42e-4c4e-ac76-07b111627ce2.png)

Step 02: Read the images and train a Unet Model
================================================
- Read image and mask file names into a list and shuffle it
- Create training and test images list using above list
- Generate tensorflow datasets for both training and test images
- Define custom evaluation matrics calle dice coefficient and dice loss
- Install segmentation models library
- Define a Unet model with a desired backbone and pre loaded weights
- Test the model with the test dataset
- Train the Unet model and save the model in h5 format

Example of a prediction in the test set using the saved model
From left to right, The original image, its orignal mask and the predicted mask using the trained Unnet model
![image](https://user-images.githubusercontent.com/74288317/141658343-f03091f8-29c5-4eec-b659-b88595cc315e.png)

Evaluation Metric
==================
This is evaluated on the mean Dice coefficient. The Dice coefficient can be used to compare the pixel-wise agreement between a predicted segmentation and its corresponding ground truth. The formula is given by:

![image](https://user-images.githubusercontent.com/74288317/141693111-689569fe-e161-4853-9544-97610cff18aa.png)

where X is the predicted set of pixels and Y is the ground truth. The Dice coefficient is defined to be 1 when both X and Y are empty. The leaderboard score is the mean of the Dice coefficients for each image in the test set.

Results
========
- Dice coefficient: 0.86

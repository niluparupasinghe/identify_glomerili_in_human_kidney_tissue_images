# identify_glomerili_in_human_kidney_tissue_images
This is a semantic segmentation task to identify glomeruli in a given kidney tissue image. This is done as a part of a Kaggle competition. The provided images were really large images with average size is equal to 25 x 30 (k). Such image does not fit in the memory for processing. However one image contained a large number of gromeruli and by tiling the large image in to small set of images created a reasonably managable and representative training and testing image data sets. Therefore the solution is mainly consist of two steps.

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

Results
========
- Dice coefficient: 0.86 (out ot 1.00)

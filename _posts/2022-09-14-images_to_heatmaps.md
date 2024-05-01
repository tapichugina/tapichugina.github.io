---
layout: post
title: Create LinkedIn backgrounds by turning images into heatmaps
date: 2022-09-14 11:59:00-0400
description:  creative way to make a LinkedIn background
tags: image-processing, dataviz
categories: #sample-posts
#giscus_comments: true
related_posts: false
toc:
  beginning: true
---

This post shows creative way to make a LinkedIn background using Python Scikit-image  
Link to  <a href="https://github.com/tapichugina/Dataviz-turning-images-to-heatmaps">Github</a>  
Posted on <a href="https://medium.com/@ttatyana.pichugina/turning-images-into-heatmaps-a703ce545755"> Medium</a>

## Motivation
If you are looking for a creative way to make a LinkedIn background, you may play with turning images into heatmaps. Heatmaps are beautiful and can be an artistic masterpiece. They will give you a lot of freedom in colors and effects. Here are some steps to consider.
{% include figure.liquid loading="eager" path="assets/img/image_to_heatmap/image_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

## Image preparation
Find RGB image (pixabay is a great source of images). The format does not play a huge role. It could be jpg or png. The LinkedIn recommended background image size is 2000px by 600 px (width,high). Finding such an image strip is difficult, so the image often should be cropped or resized beforehand.

In my experience images with big distinctive objects work the best. I have selected a pretty butterfly from pixabay <a href="https://pixabay.com/ru/photos/%d0%b1%d0%b0%d0%b1%d0%be%d1%87%d0%ba%d0%b0-lycaena-dispar-7320158/"> link </a>. The RGB image size was 1293px in height and 1920px in width with 3 color channels. I have cut the 600 px high stripe.

The heatmap is a tool to visualize a 2D matrix, so we need to convert the stripe to grayscale, which turns a 3D matrix into a 2D matrix.

``` python
# Load original image and cut the stripe

import numpy as np 
import matplotlib.pyplot as plt
import skimage.io as io


img=io.imread("img_file.jpg")
img_cut=img[400:1000,:,:]
plt.imshow(img_cut)

```
{% include figure.liquid loading="eager" path="assets/img/image_to_heatmap/Image_2.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

```python
# Convert to gray

from skimage.color import rgb2gray
gray_img = rgb2gray(img_cut)
plt.imshow(gray_img,cmap='gray')

```

{% include figure.liquid loading="eager" path="assets/img/image_to_heatmap/Image_3.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

## Pixelated artwork effect
The aim of this step is to make the image look like pixelated artwork. The image is divided into non-overlapping blocks. How to work with blocks I have found  <a href="https://tirthajyoti.github.io/Scikit-image-book/Block_view_mean_max_median_sampling.html">here</a>. The bigger the size of the block the more pixelated it looks.


For each block, some representative value was calculated. Here, I use the minimum value across the block, but maximum, or median, any metric you like should work as well.
```python
from skimage import util

# select box size
block_shape = (10, 10) 
view = util.view_as_blocks(gray_img, block_shape)

# collapse the last two dimensions in one
flatten_view = view.reshape(view.shape[0], view.shape[1], -1)

# choose min within box
min_view = np.min(flatten_view, axis=2)

plt.imshow(min_view,cmap='gray')
```

{% include figure.liquid loading="eager" path="assets/img/image_to_heatmap/Image_5.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}

## Choose colormap for heatmap
This is the step allowing the most artistic freedom. The seaborn color palette could be a good starting point <a href="https://seaborn.pydata.org/tutorial/color_palettes.html">link</a>. I personally like diverging color palettes.

``` python
import seaborn as sns

# save figure for LinkedIn recommended size (2000 width, 600 height)
my_dpi=96
plt.figure(figsize=(2000/my_dpi, 600/my_dpi), dpi=my_dpi) 
sns.heatmap(max_view,xticklabels=False,yticklabels=False,cmap="coolwarm",cbar=False)
plt.savefig("coolwarm.png",bbox_inches='tight')
```
{% include figure.liquid loading="eager" path="assets/img/image_to_heatmap/Image_6.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
{% include figure.liquid loading="eager" path="assets/img/image_to_heatmap/Image_7.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
{% include figure.liquid loading="eager" path="assets/img/image_to_heatmap/Image_8.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
Choose what you like the best!
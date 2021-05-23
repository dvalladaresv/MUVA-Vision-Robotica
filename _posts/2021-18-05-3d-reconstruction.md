---
layout:     post
title:      3D Reconstruction
date:       2021-05-18 18:00:00
excerpt:    This exercise consists of reconstructing a 3D scene using a robot with two cameras.
categories: general
share-img: /img/posts/follow_line/poll.png
---
## Introduction
This exercise consists of reconstructing a 3D scene using a robot with two cameras calibrated.   
## Image preprocessing

The first step is to obtain the images from both cameras:

![Images](/MUVA-Vision-Robotica/img/posts/3d-reconstruction/imagenes.png)   

Once the images are obtained, they are converted on: 
   - The one hand to grayscale, which will be necessary for contour detection.    
        ![Images Gray](/MUVA-Vision-Robotica/img/posts/3d-reconstruction/gray.png)   
   - And on the other hand to the HSV(Hue, Saturation, Value) color space, which will be used to calculate the correlation in the correspondence search stage.           
        ![Images HSV](/MUVA-Vision-Robotica/img/posts/3d-reconstruction/hsv.png)   
        
## Algorithm   

### 1. Feature Point Detection
The first step is to extract the minutiae that we want to reconstruct in 3D. For this we use a Canny filter, which allows us to detect the edges in the image. In addition we add a morphological Closure operation to eliminate possible errors and further refine the edges.    
![Images HSV](/MUVA-Vision-Robotica/img/posts/3d-reconstruction/canny.png)    

### 2. Matching search
Taking the left image as a reference, for each of the characteristic points, we look for its counterpart in the right image. For each point we look at its neighborhood (patch, in our case it is 23*23) and we look for a similar patch in the other camera.    
- To avoid having to search the whole image, we search on the epipolar line, the projection of the back projection ray of the pixel in the right camera. We will not only search on the epipolar line, but also on the epipolar fringe, we give a margin of +-10 pixels, for possible errors in the calibration.     
- We also use the attentive restriction, not to search the whole epipolar fringe, but only those that are characteristic points in the right image.   
- For the patch comparison we have used the HSV color space correlation, which is more robust to illumination changes.    
<div style="text-align: center">
    <video width="600px" height="400px" controls preload> 
        <source src="/MUVA-Vision-Robotica/img/posts/3d-reconstruction/macthcing_video.mp4"></source> 
    </video>
</div>

### 3. Triangulation
Once the correspondences are obtained, for each point we throw the back projection ray, and calculate the minimum point between these two lines. If the Euclidean distance is less than a threshold (in our case 0.4), we consider it to be a good 3d point and project it.    
![Triangulation](/MUVA-Vision-Robotica/img/posts/3d-reconstruction/proyeccion.png)
                Fuente: José Miguel Buenaposada Biencinto       

## Solution   
<div style="text-align: center">
    <video width="600px" height="400px" controls preload> 
        <source src="/MUVA-Vision-Robotica/img/posts/3d-reconstruction/3d_reconstruction_video.mp4"></source> 
    </video>
</div>
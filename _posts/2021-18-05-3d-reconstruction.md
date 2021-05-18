---
layout:     post
title:      3D Reconstruction
date:       2021-05-18 18:00:00
excerpt:    This exercise consists of reconstructing a 3D scene using a robot with two cameras.
categories: general
share-img: /img/posts/follow_line/poll.png
---
## Introduction
This exercise consists of getting our simulated formula 1 to complete the circuit by following a red line painted on the ground using the robot's camera. 

## Image preprocessing

The first step is to obtain the images from both cameras:

![Images](/MUVA-Vision-Robotica/img/posts/3d-reconstruction/imagenes.png)   

Once the images are obtained, they are converted on: 
   - The one hand to grayscale, which will be necessary for contour detection    
        ![Images Gray](/MUVA-Vision-Robotica/img/posts/3d-reconstruction-line/gray.png)   
   - And on the other hand to the HSV(Hue, Saturation, Value) color space, which will be used to calculate the correlation in the correspondence search stage.           
        ![Images HSV](/MUVA-Vision-Robotica/img/posts/3d-reconstruction/hsv.png)   
        
## Algorithm   

### Feature Point Detection

### Matching search

### Triangulation

## Conclusions
With this algorithm, it has been possible to complete a lap of the circuit in about 50 seconds. Trying with other algorithms for example using only the PD controller for the turn and determining the speeds in a staggered manner with fixed values, I have been able to reach a time of 30 seconds. Even not getting such a good time in the chosen algorithm, I consider that it is adequate, since the speed is adjusting in a controlled way being more realistic. It also allows to adjust better to the line regardless of whether it is a straight or a curve.    

One of the difficulties I have encountered with the argorithm, is that when using two PD, four parameters are needed to be adjusted experimentally.   

Finally, thanks to this practice I have been able to better understand how this type of controller works and its potential.    

<div style="text-align: center">
    <video width="600px" height="400px" controls preload> 
        <source src="/MUVA-Vision-Robotica/img/posts/follow-line/p1_follow_line.mp4"></source> 
    </video>
</div>
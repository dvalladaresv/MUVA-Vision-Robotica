---
layout:     post
title:      Follow Line
date:       2021-02-26 18:00:00
excerpt:    This exercise consists of getting a simulated Formula1 to complete the circuit following a red line. Using the robot camera for this.
categories: general
share-img: /img/posts/follow_line/poll.png
---
## Introduction
This exercise consists of getting our simulated formula 1 to complete the circuit by following a red line painted on the ground using the robot's camera. 

## Image preprocessing

The first step is to obtain the image:

![Image](/MUVA-Vision-Robotica/img/posts/follow-line/frame.png)

And once obtained a series of processes are performed on it to obtain the line and the points of interest. In this case the processing that has been carried out has been:

   1. The original image contains an excess of information, for example the sky part is not useful. So we reduce the length of the image. With this we get a smaller image and the operations on it are faster.   
        ![Image Reduce](/MUVA-Vision-Robotica/img/posts/follow-line/frame_reduce.png)
   2. Now a thresholding is performed on the image, in such a way that we have a binary image, where the white line appears and the rest is black.
        ![Thresholding](/MUVA-Vision-Robotica/img/posts/follow-line/filtrado.png)
   3. We obtain the contours of the thresholded image, this allows us to detect the line and its respective measures.
        ![Countour](/MUVA-Vision-Robotica/img/posts/follow-line/contour.png)
   4. Now we have three points that are necessary for the implemented algorithm. These are the centroid of the contour, the top point of the contour and the midpoint between the centroid and the top point.
        ![Puntos](/MUVA-Vision-Robotica/img/posts/follow-line/puntos.png)
        
## Algorithm


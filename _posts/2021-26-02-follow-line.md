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

### PD controller for rotation
A PD controller has been used to control the necessary rotation of the robot so that it does not go out of line.   
   1. The calculation of the error is determined by the average of the error of the reference with respect to the centroid and the error of the reference with respect to the upper point. The reason for using this error is because, on the one hand, if we only look at the centroid, this point is very close to the camera so it will not be able to anticipate the curves well. And if we rely only on the upper point if we anticipate the curves well, but it may be the case that the curve is very far away and we are anticipating too much. So we have opted for a compromise between the centroid and the upper point.    
   > error_line = ((reference - cx) + (reference - topx))/2
   2. Then the expression that allows us to adjust is determined by the current error made and the previous error and weighted by the parameters kd (proportional) and kv (derivative), Then the expression that allows us to adjust is determined by the current error committed and the previous error and weighted by the parameters kd(proportional) and kv(derivative), whose values have been obtained experimentally.   
   >  giro = kp * error_line + kd * (error_line - last_error)

### PD controller for speed
A PD controller has also been used for speed. In order to adjust the speed according to the straights and curves.   
   1. We calculate the error committed in a similar way as we did for the PD of the turn. The only difference is that we now use the parameters kpv(proportional) and kdv(derivative), also adjusted experimentally.   
   > vel_error = kpv * abs(error_line) + abs(kdv * (error_line - last_error))

### Case-based controller
We use a case-based controller to further fine-tune the speed, depending on whether we are on a straight, small curve, medium curve and large curve. To estimate what the curve looks like, we base it on the dispersion committed between the central point and the upper point.    
     ![Puntos](/MUVA-Vision-Robotica/img/posts/follow-line/curva.png)   
    1. If the error being made is small and the dispersion is also small. This indicates that we are on a straight line. So we increase the speed to twice the optimal speed.   
    2. If the error that is being made is not very big and the dispersion is not so big either. This is due to a curve that is very light. So we go to the optimum speed.   
    3. If the error is very large, this is because the curve is a bit steep. So the speed at which it will go will be the division between the optimal speed and the error_speed chosen in the PD.  
    4 If finally the dispersion is very large, this is due to a very steep curve. So the speed will be given by the division between the optimum speed and the PI error speed, but penalizing it a little more, by a factor of 1.2.   

## Conclusions
With this algorithm, it has been possible to complete a lap of the circuit in about 50 seconds. Trying with other algorithms for example using only the PD controller for the turn and determining the speeds in a staggered manner with fixed values, I have been able to reach a time of 30 seconds. Even not getting such a good time in the chosen algorithm, I consider that it is adequate, since the speed is adjusting in a controlled way being more realistic. It also allows to adjust better to the line regardless of whether it is a straight or a curve.    

One of the difficulties I have encountered with the argorithm, is that when using two PD, four parameters are needed to be adjusted experimentally.   

Finally, thanks to this practice I have been able to better understand how this type of controller works and its potential.    

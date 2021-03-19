---
layout:     post
title:      Follow Line
date:       2021-03-19 18:00:00
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
   > error = ((reference - cx) + (reference - point_topx))/2
   2. Then the expression that allows us to adjust is determined by the current error made and the previous error and weighted by the parameters kd (proportional) and kv (derivative), whose values have been obtained experimentally.   
   >  giro = kp * error + kd * (error - last_error)

### PD controller for speed
A PD controller has also been used for speed. In order to adjust the speed according to the straights and curves.   
   1. We calculate the error committed in a similar way as we did for the PD of the turn. The only difference is that we now use the parameters kpv(proportional) and kdv(derivative), also adjusted experimentally.   
   > error_vel = kpv * abs(error) + abs(kdv * (error - last_error))

### Case-based controller
To further refine the velocity, we use a case-based controller, depending on whether we are on a straight line, a small curve, a medium curve, a large curve or a steep curve. To estimate what the curve looks like, we rely on the dispersion between the center point and the top point and the displacement error made.   
   ![Puntos](/MUVA-Vision-Robotica/img/posts/follow-line/curva.png)    
   > disper = point_topx - point_med
       
   1. If the error is too small (<5 px) or the dispersion is too small(<5 px). This indicates that we are dealing with a straight line. So we go to twice the optimal speed. We give the car a "turbo".     
   > vel = vel_opt*2
   2. If the error is not too large (<20 px) or the dispersion is not too large (<25 px). It indicates that we are facing a slightly curved terrain. So we can go to the optimal speed. This speed has been calculated experimentally.      
   > vel = vel_opt
   3. If the dispersion is between 25 and 40 pixeles. This indicates that we are dealing with a medium curve terrain. So we adjust the velocity as the optimal velocity minus the error velocity obtained by the PD and it is also weighted by a factor of 0.2.   
   > vel = vel_opt - error_vel*0.2
   4. If the dispersion is between 40 and 60 pixels. This is a very curved terrain. So we add the same velocity as in the previous case but now the error velocity is multiplied by a factor of 0.3.    
   > vel = vel_opt - error_vel*0.3
   5. If the dispersion is already higher than 60 pixels. This is a very steep curve. So the speed is calculated as in the previous case but now the error rate is multiplied by 0.5.   
   > vel = vel_opt - error_vel*0.5
   6. If we have lost the line, we try to recover it by turning the car.   
   Note: If the error speed is higher than the optimum speed, we assume that the error speed will be equal to the optimum speed, to avoid problems in the speed setting.   
   
## Conclusions
With this algorithm, it has been possible to complete a lap of the circuit in about 50 seconds. Trying with other algorithms for example using only the PD controller for the turn and determining the speeds in a staggered manner with fixed values, I have been able to reach a time of 30 seconds. Even not getting such a good time in the chosen algorithm, I consider that it is adequate, since the speed is adjusting in a controlled way being more realistic. It also allows to adjust better to the line regardless of whether it is a straight or a curve.    

One of the difficulties I have encountered with the argorithm, is that when using two PD, four parameters are needed to be adjusted experimentally.   

Finally, thanks to this practice I have been able to better understand how this type of controller works and its potential.    

<div style="text-align: center">
    <video width="600px" height="400px" controls preload> 
        <source src="/MUVA-Vision-Robotica/img/posts/follow-line/p1_follow_line.mp4"></source> 
    </video>
</div>
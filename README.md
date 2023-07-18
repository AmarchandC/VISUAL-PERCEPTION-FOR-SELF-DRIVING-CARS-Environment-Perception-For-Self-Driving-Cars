#Environment-Perception-For-Self-Driving-Cars
Using the output of semantic segmentation neural networks to implement drivable space estimation in 3D.

Using the output of semantic segmentation neural networks to implement lane estimation.

Using the output of semantic segmentation to filter errors in the output of 2D object detectors.

the filtered 2D object detection results to determine how far obstacles are from the self-driving car.
## The Camera Sensor

![image](https://github.com/AmarchandC/VISUAL-PERCEPTION-FOR-SELF-DRIVING-CARS-Environment-Perception-For-Self-Driving-Cars/assets/82858194/1a5077e4-18cf-4ced-a37c-727910783707)

All the required 3D estimation will be performed in the camera coordinate frame

The camera as a sensor is usually oriented with its y-axis pointing downward.

The only thing you will need to be careful about, is the sign of the height value of pixels.

Points higher than the camera will have a negative height, while points lower than the camera will have a positive height.


![image](https://github.com/AmarchandC/VISUAL-PERCEPTION-FOR-SELF-DRIVING-CARS-Environment-Perception-For-Self-Driving-Cars/assets/82858194/f47019e5-8e4a-4728-aed2-98092f155034)

![image](https://github.com/AmarchandC/VISUAL-PERCEPTION-FOR-SELF-DRIVING-CARS-Environment-Perception-For-Self-Driving-Cars/assets/82858194/85b2267c-d118-4c72-a5c6-8cfed0346f5d)


To filter out the horizontal lines, we can rely on the slope of the estimated lines.

Horizontal lines and images tend to have a slope very close to zero. However, we also want to remove heavily slanted lines.

As such, a threshold is introduced as a lower limit of allowed slopes for the output of this filtering step. The exact value of this threshold needs to be determined empirically, try values between 0.1 and 0.3 for best results.

![image](https://github.com/AmarchandC/VISUAL-PERCEPTION-FOR-SELF-DRIVING-CARS-Environment-Perception-For-Self-Driving-Cars/assets/82858194/8aef1da4-b05f-4629-9f97-762f75502022)



A simple clustering algorithm would be to first choose a cluster center at random from the remaining filter lines, then add to the cluster any lines that have a similar slope or intercept to the cluster line.

A line is considered close to the cluster center if the difference between its slope and slope of the cluster center is less than a specific slope threshold, and the distance between its intercept and the intercept of the cluster center is less than a specific intercept threshold as well.

The slope difference threshold is usually chosen to be a maximum of 0.3, while the intercept difference threshold is defined in pixels, and is usually chosen between 20 and 50 pixels.

![image](https://github.com/AmarchandC/VISUAL-PERCEPTION-FOR-SELF-DRIVING-CARS-Environment-Perception-For-Self-Driving-Cars/assets/82858194/7ccbe6df-1946-414a-96ab-ccb59fa47a67)


![image](https://github.com/AmarchandC/VISUAL-PERCEPTION-FOR-SELF-DRIVING-CARS-Environment-Perception-For-Self-Driving-Cars/assets/82858194/64928d8d-c9ba-4fa4-8098-932c1501cd3f)

![image](https://github.com/AmarchandC/VISUAL-PERCEPTION-FOR-SELF-DRIVING-CARS-Environment-Perception-For-Self-Driving-Cars/assets/82858194/a239c1fc-ff5a-4414-9446-646b9af82eaa)

The output of object detection is usually reliable. But for this assessment, we are given a high recall low precision detector that detects all objects in the scene, but also provides some false positives.

We are required to use the output from semantic segmentation to eliminate these false positives before estimating the distance to the obstacles.
The results should be bounding boxes that reliably contain obstacles. To perform this filtering, we will need to use the semantic segmentation output to count the number of pixels in the bounding box that have the same category as the classification output from the 2D object detector.

The trick here, is that this number will depend on the size of the bounding box. We will need to normalize the pixel count by the area of the bounding box before attempting to filter out the detections with a threshold. The final normalized count is equivalent to computing the area inside the bounding box occupied by pixels belonging to the correct category.

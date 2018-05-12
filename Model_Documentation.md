## Writeup Report

---

**Path Planning Project**

The broad goals / steps of this project are the following:

* Read in the waypoints for the highway track from the CSV file and convert to car co-ordinates shifted to origin to make the maths simple.
* Read in the telemetry data from the Simulator.
* Generate the predictions from the Sensor fusion data.
* Based on the predictions decide lane action i.e - Keep Lane, Change Lane right or Change Lane left.
* Calculate the 'Best Trajectory'.
* Calculate the required path by fitting to 'spline' library.


## [Rubric](https://review.udacity.com/#!/rubrics/1020/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Path Planning

#### 1. There is a reflection on how to generate paths. The code model for generating paths is described in detail.

As discussed in the classroom and by general sense, highways have a limited number of maneuvers possible, viz. Change lane right, Change lane left and Keep Lane. These are the key behaviours which control other vehicle parameters such as speed and velocity, for example -  because if a lane change is not possible (because there's another vehicle in the other lane) and there's a vehicle ahead, velocity has to be decreased to avoid a collision. 

To generate the paths , the following pipeline is followed:

1. The vehicle starts with an initial velocity of 0 mph and the max speed is set as 49.5 mph. 

2. Based on the sensor fusion data, which has the data on the predicted location of the vehicles around us, we choose the behaviour of our vehicle as follows:

  a) If the current lane has no vehicles in a range of 30 m in front, the same lane is kept, while following the speed constraints. 

  b) If the above is not true, other options are checked for. This includes either changing lane to left or changing lane to right , provided left and right lanes are present (i.e - no left if the vehicle is in leftmost lane and no right if the vehicle is in the rightmost lane). 
  
  c) If a lane change is possible then a marging of +-30 m is kept from other vehicles to avoid a collision. This means that only if the lane is clear +-30 m from our vehicle's position, will the lane change be considered safe. 

  d) There may be cases when neither left nor right lane change is safe.  In such a case, the model slows down the speed and the same lane is kept.

3. Once the above behavioral decisions have been decided upon, the next step is trajectory generation. For this purpose, the 'Spline' library is used, imported as a header file. 

4. The waypoints are inputted into the spline. The last two waypoints from from the previous timestamp's trajectory are set as the first two waypoints for the new trajectory , to ensure smooth movement and keep jerk minimum. It also takes into account lane change, if any.

5. The remaining waypoints from the previous trajectory is used in setting the next trajectory. The points required further are calculated by the spline. A point 30 metres ahead of the current is considered as the goal and  points between are calculated as '50-points from previous trajectory', with spacing based on the velocity reference. 

6. The output from spline is converted back to map co-ordinates.

### Discussion

#### 1. Briefly discuss what could you do to make the implementation more robust?

I have mostly followed and used the code and approach discussed by Aaron and David in the QA video. I'd have liked to play around with polynomial trajectory generation and also, setting a cost function for implementing the lane change logic. As discussed in the QA , a cost function which incorporates the number of cars in each lane, both front and back; the distance from these cars; and calculates the associated cost for each lane transition would definitely be a more robust way of deciding lane change. I'd like to improve the solution by implementing this approach.



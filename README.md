# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program
   

### Goals
In this project your goal is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. Provided are the car's localization and sensor fusion data, there is also a sparse map list of waypoints around the highway. The car should try to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible. Other cars will try to change lanes too. The car should avoid hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car should be able to make one complete loop around the 6946m highway. Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car should not experience total acceleration over 10 m/s^2 and jerk that is greater than 10 m/s^3.

### Solution
My approach calculates scores for each lane based on traffic (cars ahead and behind and their respective speed) and distance from the actual lane then chooses the lane with the highest score. The score function itself is quite simple but it contains some logic which might need some detailed explanation. 

if(dist_ahead_<10) score_ = 0;
else if(dist_behind_<8) score_ = 0;
else if(dist_behind_<12 && min_speed_ > act_speed) score_ = 0; //other car too close
else score_ = 2*max_speed_ + dist_ahead_ + id_;

If there is a car close in front of out car in the given lane (assuming where our car might be in that lane) then the score will be zero. If there is one next to us (but slightly behind) or is behind us but its speed is higher than ours then the score of the lane will be zero as well. Otherwise it is calculated as two times the possible speed on the lane (in mph) plus the distance to the closest car ahead. The ID is actually the number of the lane (as an integer number) to make the car prefer the rightmost lane in case all other parameters are equal.

The lane changing logic contains some constraints as well. First it calculates and compares the score of each lane and marks the one with the highest score as candidate. If it is too far from our position (e.g. we are in the leftmost lane but the best would be the rightmost one) we must pass the middle lane as well so it must also be accessible and choosen first. I also defined a dead zone to allow lane changes only if the gain is considerable (otherwise the car might switch back and forth between two lanes too often).

The path itself is calculated using the suggested spline library. First I used 30 path points but the car responded too slow so I decreased this list to only 10 points (I have a powerful computer so the calculations happened really fast but on a slower computer more points might be needed).

As the car progresses along the path the remaining path points are kept in each iteration which guarantees that the car follows a jerk-free path.

I made and uploaded a video of my performance to YouTube:
https://youtu.be/V4ruTO1bIX0

The video is 22 minutes long (without incidents) but the runtime was actually 30 minutes. In 30 minutes the car covered 23.67 miles which means the average speed was 47.34mph (it is really great considering sometimes there is high traffic on the lanes and the max speed I used is 49.5mph)

## Remaining problems

I have seen some incidents when other cars tried to change lanes (and they changed to our lane fast and unexpectedly). This, of course, ended up in a collision as it could only be prevented with emergency braking (which is not allowed in the project). It might be avoidable if all the other cars' trajectories would be estimated (right now I only use the speed information and assume that the car will keep its lane and this could cause problems). Nevertheless, the solution I programmed is pretty reliable and this particular issue did not happen during the last couple of hours of testing.

## Basic Build Instructions

You can download the Term3 Simulator which contains the Path Planning Project from the [releases tab (https://github.com/udacity/self-driving-car-sim/releases/tag/T3_v1.2).

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.


## Details

1. The car uses a perfect controller and will visit every (x,y) point it recieves in the list every .02 seconds. The units for the (x,y) points are in meters and the spacing of the points determines the speed of the car. The vector going from a point to the next point in the list dictates the angle of the car. Acceleration both in the tangential and normal directions is measured along with the jerk, the rate of change of total Acceleration. The (x,y) point paths that the planner recieves should not have a total acceleration that goes over 10 m/s^2, also the jerk should not go over 50 m/s^3. (NOTE: As this is BETA, these requirements might change. Also currently jerk is over a .02 second interval, it would probably be better to average total acceleration over 1 second and measure jerk from that.

2. There will be some latency between the simulator running and the path planner returning a path, with optimized code usually its not very long maybe just 1-3 time steps. During this delay the simulator will continue using points that it was last given, because of this its a good idea to store the last points you have used so you can have a smooth transition. previous_path_x, and previous_path_y can be helpful for this transition since they show the last points given to the simulator controller with the processed points already removed. You would either return a path that extends this previous path or make sure to create a new path that has a smooth transition with this last path.


## Dependencies

* cmake >= 3.5
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```





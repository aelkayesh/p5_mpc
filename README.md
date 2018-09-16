# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

Model
In this project, we used basic kinematic vehicle model: we (and simulator) do not have information about vehicle mass, engine, tires, road surface etc. We know (receive from the simulator):

vehicle position at moment t (xt, yt) - and other moments
vehicle velocity v
course angle ψ
We can control our vehicle using two controls variables: throttle (used both for gas pedal and brakes as a single acceleration control) and steering angle δ.

Knowing current vehicle state, it is easy to predict the next one, in a dt seconds:

xt+1 = xt + vt * cos(ψt) * dt
yt+1 = yt + vt * sin(ψt) * dt
ψt+1 = ψt + vt / Lf * δt * dt
vt+1 = vt + at * dt
Where Lf is the distance between the vehicle's center of gravity and its front (estimated as 2.67 meters for current project).

MPC
To control the vehicle, we used Model Predictive Controller (MPC), which optimized control variables (acceleration and steering angle) with regard to the provided cost functions.

As MPC operates in vehicle coordinate system, and simulator provides us with values in it's own 'world' coordinates, coordinate transformation is performed before sending values to the MPC (lines 107-113 of the main.cpp).

Two of the critical parameters for the MPC are number of points to look into the future N and distance between these points dt:

If N is too big and dt is too small, where a really big number of steps to computer Xs,Ys for and that degrades performance a lot that the vehicule won't even move. If N is too small and dt is too small, the model won't see enough points in the future to predict the right actuator to manipulate. After many trials of setting different values for N and dt, I found that a fast update step represented by dt = 0.001 was essential to keep the car smooth at speed up to 30 mph. N was set to 25 at this point and the car seemed to move nicely and comfortablly. The only thing with this very little dt is that the points generated from MPC are all in the range of 0.xx like 0.05 and 0.002 ...etc. To properly visulize these points I had to multiply each point with a scaling factor, at this point it's set to 100;

Another set of hyper-parameters with which we can affect MPC is the choice of cost function: which components to add and with which weights.

My cost function is composed of the sqaured root of errors of concerned components, in addition to setting a high cost for changing steering angle or acceleration suddenly in big values.

Delay
To account for the delay in the control loop, I send to the MPC not the current vehicle state, but state in a delay seconds, predicted by the kinematic model (lines 101-104 of the main.cpp).

##Reflections on the solution
It would be nice to see the car driving at higher speeds, for this, extra effort is needed to find more accurate cost function and correct setting of N and dt.

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
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
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.

* **Ipopt and CppAD:** Please refer to [this document](https://github.com/udacity/CarND-MPC-Project/blob/master/install_Ipopt_CppAD.md) for installation instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* Not a dependency but read the [DATA.md](./DATA.md) for a description of the data sent back from the simulator.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## Tips

1. It's recommended to test the MPC on basic examples to see if your implementation behaves as desired. One possible example
is the vehicle starting offset of a straight line (reference). If the MPC implementation is correct, after some number of timesteps
(not too many) it should find and track the reference line.
2. The `lake_track_waypoints.csv` file has the waypoints of the lake track. You could use this to fit polynomials and points and see of how well your model tracks curve. NOTE: This file might be not completely in sync with the simulator so your solution should NOT depend on it.
3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.)
4.  Tips for setting up your environment are available [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)
5. **VM Latency:** Some students have reported differences in behavior using VM's ostensibly a result of latency.  Please let us know if issues arise as a result of a VM environment.

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/b1ff3be0-c904-438e-aad3-2b5379f0e0c3/concepts/1a2255a0-e23c-44cf-8d41-39b8a3c8264a)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).

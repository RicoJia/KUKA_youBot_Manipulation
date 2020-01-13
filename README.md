
#KUKA youBot Pick & Place
In this project, I developed a control algorithm and a V-REP simolator for a KUKA youBot to accomplish a pick and place task. 

![alt text](https://i.ytimg.com/vi/zfYSGZto2Gk/hqdefault.jpg)
### Key Concepts and Steps
Key Steps include:
1. End-effector trajectory planning
2. Wheel and joint speed calcolation
3. Position Simolation with joint speeds
4. Visualization on V-REP 

Key Concepts include: 
1. Straight line trajectory generation with time scaling
2. Forward kinematics using screw theory and Lie algebra
3. Omnidirectional robot wheel speed calculation
4. Velocity Kinematics using Jacobian for calculating robot arm joint speeds
5. PD+Feedforward for end-effector position control
6. V-REP Physics Engine


### Design 

The KUKA youBot consists of a mobile platform with 4 omnidirectional wheels (3 degrees-of freedom) and one 5 degrees-of-freedom arm. Therefore, there are
7 degrees of freedom associated to the system, making the system a redundant system. 

#### Trajectory Generation
[Point to point linear Trajectory Generation technique](https://youtu.be/1JRMqfEm79c) is used for generating the desired trajectory as a series of 
SE(3) Transform matrices <img src="https://latex.codecogs.com/gif.latex?X_d"/>, separated by <img src="https://latex.codecogs.com/gif.latex?\Delta_t"/>

#### Joint Velocity Control
A hybrid feedforward+PI controller is applied here 
     
<img src="https://latex.codecogs.com/gif.latex? V(t) = [Ad_{X^{-1}X_d}] V_d(t) + K_{p} X_{err}(t) + K_{i} \int_{0}^{t} X_{err}(t) dt" /> 
     
  - V is the required twist to take the end effector from the **current pose** to the **next desired pose**. Xd is the desired end-effector pose in 3D transformation matrix, i.e, Lie Group SE(3).
  Vd is the twist required to take current desired end effector configuration to the next desired end effector configuration and is given by
<img src="https://latex.codecogs.com/gif.latex? [V_d] = (1 / \Delta t) log(X_d^{-1} X_{d, next})" />, where delta_T is the time step between the current configuration and the next desired configuration). 
  
  -  X_err is the error twist between the current pose and the desired pose, generated from the SE(3) transformation matrix from the current pose to the desired pose.
    Therefore we can have an error term which react to the error of the end effector  <img src="https://latex.codecogs.com/gif.latex?K_{p} X_{err}(t)"/>
  
  - An integral term is also added so it keeps a bringing down the steady state error between the end effector and the current pose, if there is any
    <img src="https://latex.codecogs.com/gif.latex? K_{i} \int_{0}^{t} X_{err}(t) dt" /> 

#### Joint and Wheel Speeds Calculation
The joint speeds of the arm and the wheel speed of the mobile platform are determined by: 

<img src="https://latex.codecogs.com/gif.latex?\begin{pmatrix} u \\ \dot{\theta} \end{pmatrix} = J_e^{+} V" /> 
, Where u and theta_dots are wheel and joint speeds, and J_e is the 6x9 Jacobian matrix that maps joint and wheel speeds to end-effector twists. J_e is developed from 

[omnidirectional wheel speed control](https://youtu.be/NcOT9hOsceE)

#### Current Pose Update
To calculate the current pose, we calculate:

1. new arm joint angles = (old arm joint angles) + (joint speeds) * Δt, , from which we can also 
get <img src="https://latex.codecogs.com/gif.latex?X_{chassis-to-end-effector}"/>
2. new wheel angles = (old wheel angles) + (wheel speeds) * Δt
3. new chassis configuration (Heading, x, y) is obtained from odometry with new wheel angles, from which we can also 
get <img src="https://latex.codecogs.com/gif.latex?"/>
4. End-effector pose in SE(3) Transformation Matrix = <img src="https://latex.codecogs.com/gif.latex?X = X_{chassis}X_{chassis-to-end-effector}"/>

### More Info
For more details about this project, check out [the project instructions here](http://hades.mech.northwestern.edu/index.php/Mobile_Manipulation_Capstone). 

Also, check out the live demo of the project on Youtube:
[![Screenshot from 2020-01-13 06-50-05](https://user-images.githubusercontent.com/39393023/72257326-fe0dac00-35d0-11ea-8b9e-05db4616e818.png)](https://youtu.be/zfYSGZto2Gk)

# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

## Background and Purpose
This repository contains my programming model and discussion for the Udacity SDC nanodegree Model Predictive Control (MPC) project. MPC is to use optimization approach to actuate autonomous vehicles following the road lane. It uses a vehicle dynamics model to predict and optimize the future behavior of systems. This implementation was built in C++ and tries to calculate an optimal trajectory for the car simulator to follow around the track.


### Model Description 
This implementation uses a Kinetic model, which is a simplification of a vehicle model. Model inputs include: vehicle location (x,y), orientation (psi), velocity (v), cross track error (cte), error of psi, as well as acceleration (a) and steering angle (delta). Acceleration and steering angle are used as actuators to propel and guide the car. The update step employs the following equation:

<br>
<img src="https://github.com/joshpierro/self-driving-car-term2/blob/master/p5/images/update.png"/>
<br>

### Timestep Length and Elapsed Duration Rationale
I selected the values 10 for N and .1 for dt. This represents the number of time steps (N) and duration of a time-step (dt), and together they define the duration of time where the model can predict the trajectory. The more time steps, the slower the computation is. However, enough time steps is a crucial element to obtain the accuracy of this discrete model. 

<pre>
    //one second into the future
    size_t N = 10;
    double dt = 0.1;
    
    //I also tried
    N = 100
    dt = 0.1
    This results a much slower prediction step and the vehicle in the simulator cannot follow the track.
    
    // and 
    N = 10
    dt = 1
    This combination of time and step parameter consider too far in the fututre, which makes the car drive slowly to minimize all the errors in the predicted track.
</pre>


### Polynomial Fitting and MPC Preprocessing Discussion 
At each step, a bit of per-processing was performed on the car's location data (waypoints) before a polynomial was calculated. First the values of x,y,and psi were normalized, in order to simplify the math in the polynomial calculation. Next the x and y vectors had to be cast into VectorXd collection, so they could be used in the provided polynomial method (polyfit()). 

<pre>
     //convert vector double to VectorXd for polyfit function
      Eigen::Map<Eigen::VectorXd> waypoints_x_eig(ptrx, 6);
      Eigen::Map<Eigen::VectorXd> waypoints_y_eig(ptry, 6);
</pre>
 
 Polyfit() returns the coefficients of a third order polynomial. A 3rd-order polynomial is used to fit the line.
 
 <pre>
     auto coeffs = polyfit(waypoints_x_eig, waypoints_y_eig, 3);
 </pre> 
 
 
### MPC Latency
The purpose of latency in the model is to simulate real world driving conditions where the car does respond to commands instantly, as well as the latency between getting sensor data and processing it. I use dt larger than 100 ms to copy with this problem. 

### Result
The vehicle could reach 85 mph on the straight track and reduce speed to follow the trach at the sharp turn. 



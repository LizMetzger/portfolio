--- 
layout: post
title: Physics Simulator from Scratch
image: /videos/jack_resized.gif
author: Liz Metzger
---

(Python -- Sympy, Dynamics)

The purpose of this project was to simulate the physics of a jack falling inside of a rotating box by using the forced and constrained Euler-Lagrange equations to calculate and simulate the impacts.


![jack_in_the_box](https://user-images.githubusercontent.com/113066141/210467844-670e1588-c467-4a01-9a24-bbaf9cabc363.gif)


In the image below you can see all the frames I used and the basic setup of my system. I had a world frame at the center of my box and five frames for my jack and my box where each body had a frame (a) at its center of mass. The (a) frame of both the box and the jack were related to the world frame with both a rotation and translation in a matrix. The other four frames for the box (b, c, d, e) are in the middle of each of its walls related to the (a) frame and the jack's other four frames are at each of its ends also related to the jack's (a) frame.
 
 
![314picture](https://user-images.githubusercontent.com/113066141/207946114-df89a786-2211-44e4-9f02-1fb38b484762.jpg)
 
 
I initialized my setup so that the jack and the box had their center of mass’ aligned and both bodies aligned with the world frame.
 
To calculate my kinetic energy I found the transforms from each of my frames relative to the world frame then solved for the twists of each wall of my box and the center of mass of my jack. I calculated the twists by getting g^-1\*g for each of my frames. Once I had my twists I used the formula .5\*V_b.T\*Is\*V_b to get the kinetic energy for each wall of my box and my jack. I add each of these components together to get the total kinetic energy of the system. I calculated my potential energy by using the y coordinates of each of my box walls and the jack as calculated my my frame transformations to plug into the equation m\*g\*h which I also sum for all of my bodies. Once I had my kinetic and potential energy I calculate my Lagrangian with the equation L=KE\*V.
 
After getting my lagrangian I calculated my phis by getting the inverse transform of my box in the world frame matrix multiplied by my jack in the world frame matrix multiplied by the coordinates of the position vector. I used the x and y coordinates from the calculation to get four constraints for each corner of my jack -- one for each wall of the box. This gave me a total of 16 constraint equations all in a form similar to: phi = x/y coord +/- Lb/2. This checks that my jack is within the two sides of the box and the top and the bottom of the box. If one of these equations is true (equals 0) then it indicates that an impact is occuring.
 
I found my external forces by applying a force in the +y direction to my box which counteracted gravity and the mass of the jack to keep the box mostly centered and in the air. I also created a torque force that acts on the center of mass of my box to rotate it around its origin.
 
To check for impacts I evaluate my phi values at my initial condition when I know that no impact is happening then compare them to the evaluated phis at each time step. I am checking for a change in sign and if I find a change in sign then I know that an impact has occurred at that impact equation. Then I am able to return the number of the contraine equation and pass it to my update impact function which uses my phi and my updated impact equations with dummy variables to solve for lambda. I then check for the solution that has a non-zero lambda and uses those values to assign new qd values and I return to the normal loop. This happens until my code evaluates at all time steps.
 
Doing this gives me a list of x,y, and theta values for the jack and the box at each timestep which I used in my animation function to animate the entire system.



![Jack-in-the-box](https://user-images.githubusercontent.com/113066141/208222492-8223640a-71ec-4858-81c7-d2e8da4e67f0.gif)


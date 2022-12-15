--- 
layout: post
title: Physics Simulator from Scratch
intro: Python (sympy), Dynamics
image: /img/home.jpg
---

In the image below you can see all the fames I used and the basic set up of my system. I had five frames for my jack and my box where each body had a frame (a) at its center of mass which was related to the world frame through a rotation and a translation. The box also had four frames (b, c, d, e) with one in the middle of each of its walls related to the a frame and the back had four frames with one at each of its ends also related to the jack's a frame.


![Alt text](images/314picture.jpg)


I initialized my set up so that the jack started in the center of the box, with its center of mass aligned with the box's and with both bodies aligned with the world frame. 

I calculated my kinetic energy by getting the transforms from each of my frames appropriately then I solved for my twists for each wall of my box and the center of mass of my jack. I calculated the twists by getting g^-1\*g for each of my frames. Once I had my twists I used the formula .5\*V_b.T\*Is\*V_b to get the kinetic enrgy for each wall of my box and my jack. I add each of these components together to get the total kinetic energy of the system. I calculated my potential energy by using the y coordinates of each of my box walls and the jack as calculated my my frame transformations to plug into the equation m\*g\*h which I also sum for all of my bodies. Once I had my kinetic and potential energy I calulated my Lagrangian with the equation L=KE\*V. 

After getting my lagrangian I calculated my phis by getting the inverse transform of my box in the world frame @ by my jack in the world frame @ the coordinates of the position vector. I used the x and y coodinates from the calculation to get four constraints for each corner of my jack -- one for each wall of the box. This gave me a total of 16 constraint equations all in a form similar to: phi = x/y coord +/- Lb/2. This checks that my jack is within the two sides of the box and the top and the bottom of the box. 

I found my external forces by applying a force in the +y direction to my box which counteracted gravity and the mass of the jack to keep the box mostly centered and in the air. I also created a torque force that acts on the center of mass of my box to rotate it around its origin. 

For my impact updates I used the sim_impact function from a past homework but I modified it to take t so that the force equation could be calculated. To check for impacts I evaulate my phi values at my initial condition when I know that no impact is happening then compare them to the evaluated phis at each time step. I am checking for a change in sign and if I find a change in sign then I know that an impact has occured at that impact equation. Then I am able to return the number of the contraine equation and pass it to my update impact function which used my phi and my updated impact equations with dummy variables to solve for lambda. I then check for the solution that has a non-zero lambda and uses those values to assign new qd values and I return to the normal loop. This happens until my code evalulates at all time steps. 
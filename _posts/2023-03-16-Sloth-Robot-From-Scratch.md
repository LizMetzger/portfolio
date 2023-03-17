--- 
layout: post
title: Sloth Robot From Scratch
image: /videos/sloth_cropped2.gif
author: Liz Metzger
---

(C++, Solidworks, 3D Printing)

This was a 10 week project where the objective was to build a robot that could autonomously climb a ladder from scratch. I went through the whole process of designing, building, and programming this robot.

**Mechanical Design:**

This robot uses two *degrees of freedom -roll and pitch- to climb up and down a ladder. The robot is designed to be lightweight since there is a large load being placed on the servos that rotate the body of the robot upward. I did force calculations to make sure that the load I was asking the servos to carry was reasonable and within their limitations. I did these calculations before I knew the final design of my robot so I used a 1.5x margin of safety and rounded the weight of all my components up. This calculation was a big factor in which servos I ended up choosing since I wanted them to be light but strong and reliable. 


![sloth_calcs](https://user-images.githubusercontent.com/113066141/226038104-bfa1cc96-ddd6-4439-8552-48f7dd453667.jpg)


As a proof of concept of my mechanical design, I created a prototype out of laser cut acrylic with simple axle joints so that I could test the way that the robot would move. After this test I was pretty comfortable with the way that the robot would move and decided to move forward.


![sloth_prototype](https://user-images.githubusercontent.com/113066141/225827604-8a6fa98d-1ae1-4140-b1ad-941c487c1daf.jpg)


I designed the arms of the robot so that the hands could be easily removed and had the least amount of printing needed to increase the speed that I could iterate the design. I knew that the hands would be a really crucial design point so I felt really strongly about them being easy to change. The main challenge that I faced with them was that they needed a way to latch onto the bar and secure the hand from rotating while it raised the body of the robot. The rotating hand was a big issue for autonomy because it was really inconsistent about how much the hand would rotate so it was unpredictable how far the joint needed to turn to be able to reach the bar above it. 


![sloth_hand_rotating](https://user-images.githubusercontent.com/113066141/225822850-87aa8c21-42bd-42e6-a3c7-29513b03ae34.jpg)


To fix this rotation issue I added a lip onto the back of the hand that would lock the hand onto the bar so it wouldn’t rotate. This made releasing the hand from the bar more difficult but since the load from rotating was only on one side of the hand I ended up tapering the lip so that it was easier to create a reliable autonomous release motion. The image below shows my first design (left) and my final design (right).


![sloth_hands](https://user-images.githubusercontent.com/113066141/225815053-bbb5e9d2-afa9-4a78-9903-7b8fb94945fb.jpg)


I went through a few other design ideas like adding a “foot” to the robot to anchor it on the bottom of the body, but that meant that I would never be able to climb rungs that were not a set distance apart. I had a similar issue with adding a lip onto the lower part of the arm that would engage the rung below the robot because I thought it was limiting and when I tested adding that lip it actually made it even harder to disengage the hand from the bar than the design I ended up going with. 

**Electrical Design:**

One of the main reasons I chose the dynamixel servos was that I knew they had a relatively straightforward power and communication protocol. This simplified the design of my robot significantly since I just needed to connect my lipo battery to the powerhub and then chain my servos and connect them to the hub. Communication was also handled through the hub as I attached the dynamixel U2D2 communication converter to the power hub and that allowed me to control the servos from my laptop and the Jetson nano. I was also able to power the jetson nano off of the same lipo battery by creating a power cable that went to its DC barrel jack. 


![SlothWiringDiagram](https://user-images.githubusercontent.com/113066141/225979822-29700090-b754-4848-a92c-a14cbae265f8.jpeg)


**Software:**

I used the dynamixel SDK to interface and control my servos. The first test I did to make sure that my servos were communicating as I expected was using the SDK read_write example to lift the robot with one arm. I wrote different scripts in C++ to control the robot in different ways including: full teleop, full autonomy, and a hybrid mode where the climb motion of each arm is autonomous but can be commanded by the user. I was able to compile my code so that it ran on the Jetson Nano and my robot could run unteathered.


**Autonomy:**

To implement autonomy I constrained the problem to a single configuration of the ladder to make sure that I could create a consistent climbing gait. I started doing this by finding a series of motions that worked well to release the hands, pull the arm out of the ladder, lift the hand up, and grab the next rung. Once I had each of those sequences worked out I started to string them together into longer sequences until I had a fully autonomous climbing sequence.


{% include youtube.html id="ZVxYDPtrugs" %}


Check out my [GitHub Repo](https://pages.github.com/)!

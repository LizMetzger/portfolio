--- 
layout: post
title: Dynamic Impedance Prosthetic Ankle From Scratch
image: /videos/SLAM_demo.gif
author: Liz Metzger
---

(C, Embedded Systems, Rapid Prototyping)

[insert working video]

In this project I designed a mechanism to change the k constant of a spring by engaging a variable number of active coils which changed the stiffness of the spring. I also built a control system for a servo that uses embedded C libraries I wrote to receive readings from a quadrature rotary encoder and a force resistive sensor (FSR). The control system sends position messages to the servo which causes modulations in the joint impedance when the foot is lifted off the floor in a stepping gait.

The goal was to create a prosthetic ankle that provides a higher degree of biomimicry but is low-profile, low-cost, and modular. This meant that I was trying to avoid using big, expensive servos with complicated algorithms to simulate impedance, but also trying to create a device that would provide dynamic modulation of the impedance without the user having to manually change anything. It was also important that this be ank ankle prosthetic rather than a foot+ankle or ankle+lower leg prosthetic so that it can be combined with other prosthetics to provide patients with alternative options. 

Mechanism Design
Prototyping:
I explored a lot of different options for modulating the impedance in a joint. I started looking at ways to use cables to tension the joint, but decided this approach would require too many active servos to maintain the low-profile, low-cost design I was aiming for. I also looked at methods involving hydraulic pressure, piezoelectric materials, smart materials, servo control, and modulating friction. For various reasons I decided not to pursue these options.

One technology that I found particularly interesting were these binary stiffness compliant mechanisms. The binary stiffness comes from the angled flexures on the mechanism either being locked out causing them to be completely rigid or being released which allows them to be very compliant. My theory was that if I could make a system where these supports were not a binary stiffness and instead had a range of stiffnesses, I would be able to create a mechanism that could effectively provide a range of impedances for a prosthetic ankle. To learn more about these mechanisms you can watch [this video](https://youtu.be/CV-zewG3lBY?si=0uB2ktMFIXk5ecfW) and I've included an image of one down below with the important features labeled.


![labeled_compliant_mech](https://github.com/LizMetzger/Sloth-bot/assets/113066141/69dad754-f5d0-4d69-9ad3-eb84bfd0a211)


My concept to create angled flexures which had a range of stiffnesses was to use a spring and change the number of active coils. Changing the number of active coils actually changes the k constant of the spring which changes the amount of force needed to compress the spring x distance, thereby changing the stiffness of the spring. This leverages the formula: 

I initially designed a winged piece that could be threaded through a spring and spun through the coils to either engage more or less coils. Once I had a first prototype of this design I did some preliminary force testing to see if the force to compress the spring changed depending on the number of coils engaged. I found that there was an increase in the stiffness when less coils were engaged and a decrease in the more coils that were engaged. After obtaining this proof of concept I moved on to refining my design.
[insert image/video of first design]

Design refinement:
My initial design had many flaws but one key issue: I couldn’t change the number of coils without changing the distance between the top and bottom plate. To combat this, I decided to make the winged piece circular so it had more surface area to engage active coils. I also made a hole in the center of the winged piece and designed a linear guide that went in the hole and engaged with the circular piece. This allowed me to attach a servo to the linear guide which drove the rotational motion of the winged piece through the coils of the spring while allowing the circular piece to move in a passive manner up and down the guide.

For this mechanism to work properly it was important to have as tight of a fit between the circular piece and the linear guide as possible to decrease the amount of slack in the system. Having more features that were engaging between the pieces also helped it rotate easier even under loads. It is also important that the pitch of the wing piece matches exactly with the pitch of the spring. This improves the ease with which it can be threaded through the spring and prevents any unwanted compression of the spring.

[insert video of the linear guide and the winged piece interacting]
** indicate the plates and the pieces **

This was a good first step to solving the problem of changing the distance between the plates, but I soon realized that when you actually applied a force to the bottom plate there was nothing to stop the winged circular piece from sliding all the way up the linear guide until it hit the servo. This provided absolutely no resistance to the compression and created a large range of motion with zero impedance.

[insert before and after with pressing]

I fixed this problem by designing a threaded outer housing that the circular winged piece could engage with and thread through as it was driven through the coils of the servo. I fixed this outer housing to the top plate using standoffs so it was always a constant distance from the plate and servo. This meant that as the servo drove the winged piece through the spring it was also being threaded through the outer housing so that when the bottom plate was pressed the winged piece could not move and the spring would be immediately engaged.

[insert picture/video of this]

At this point I had a working mechanism for modulating impedance and I could move forward to integrating it into an ankle.

Ankle Prosthetic Design
Design Inception:
The overall structure of the design was inspired by the binary stiffness compliant mechanisms as the springs are acting like the angled flexures and the position of the arms on my device are meant to reflect the piece that the angled flexures connect to. The rotary flexures are replaced by the arms and their ability to rotate around a pivot point. I tried to position the arms of the device so that the springs would deflect as little as possible during rotation.

[insert drawings]

Mechanism Integration:
Since dorsiflexion is much more important to preserving a natural gait and has a much larger range of impedances than plantarflexion I decided to only install my dynamic impedance mechanism on the front of the ankle which controls dorsiflexion. This allowed me to design faster, decrease the overall profile of the design, and kept the system simpler. In the future it would not be unreasonable or difficult to install a second dynamic stiffness mechanism in the back of the foot.

Both of the arms of the device rotate around a common axle but rotate independent of each other so when one side compresses the other side does not stretch. The springs are press slots on the arms that keep them in place during ankle rotation, but allows the spring some movement so that it doesn’t stretch. The back spring – responsible for plantarflexion– is also tightly press fit into the housing of the ankle which keeps it in place. 

[insert picture of mechanism in the ankle]

Sensor Choices and Installation:
TODO
The foot is rigidly connected to the shaft so that as the angle of the ankle changes the encoder value changes accordingly. This ridgid attachment is achieved by screwing into the top of the part that connects the foot to the shaft and into a key in the shaft. This ensures that the foot and shaft rotate together for accurate encoder readings.

Software and Electrical
Power and Wiring:
To power my device I used three different power supplies. I used a 6V battery pack that passes through a 5V buck converter to power my TIva Launchpad,SN75HC126N non-inverting buffer, and encoder. I use the 3.3V from the Tiva to power the FSR and I also use a 11.2V lipo battery to power my servo. 

My Tiva board had two pins configured to GPIO TX and RX which were used to communicate with the servo. I also had two pins on the Tiva configured for use with a quadrature encoder and set to read an A and B channel. By comparing the rising and falling edges of these two channels the direction of motion and the position of the encoder can be extrapolated. Finally I configured a pin to read ADV values so that I could measure the change in resistance of my FSR. Reading this change in resistance let me approximate the force that my sensor was experiencing.

[insert circuit diagram]

In my design I used a non-inverting buffer to fake whole duplex communication between the Tiva (uses whole duplex) and the servo (used half  duplex). Basically the Tx of my Tiva was wired to the input of one of the buffers and the data cable from the circuit was connected to the output of that buffer. The servo data was also connected to the input of another buffer that was wired to the Rx of my Tiva board. This was done so that when one buffer was high the board was only transmitting information to the servo and the other line was essentially dead. Then when the transmission was done the Tx pin could be set to low and the Rx pin would get set to high switching the board from being in transmitting mode to receiving. This system ensures that the board is always either transmitting or receiving as it would with half duplex communication allowing me to interface with the servo.

[insert half to full duplex diagram]

Sensor Enabling and Integration:

--- 
layout: post
title: Dynamic Impedance Prosthetic Ankle From Scratch
image: /videos/prosthetic_final.gif
author: Liz Metzger
---

(C, Embedded Systems, Rapid Prototyping)

{% include youtube.html id="rJ3PUlvMFKA " %}

In this project I designed a mechanism to change the k constant of a spring by engaging a variable number of active coils which changed the stiffness of the spring. I also built a control system for a servo that uses embedded C libraries I wrote to receive readings from a quadrature rotary encoder and a force resistive sensor (FSR). The control system sends position messages to the servo which causes modulations in the joint impedance when the foot is lifted off the floor in a stepping gait.

The goal was to create a prosthetic ankle that provides a higher degree of biomimicry but is low-profile, low-cost, and modular. This meant that I was trying to avoid using big, expensive servos with complicated algorithms to simulate impedance, but also trying to create a device that would provide dynamic modulation of the impedance without the user having to manually change anything. It was also important that this be ank ankle prosthetic rather than a foot+ankle or ankle+lower leg prosthetic so that it can be combined with other prosthetics to provide patients with alternative options. 

# Mechanism Design

## Prototyping:

I explored a lot of different options for modulating the impedance in a joint. I started looking at ways to use cables to tension the joint, but decided this approach would require too many active servos to maintain the low-profile, low-cost design I was aiming for. I also looked at methods involving hydraulic pressure, piezoelectric materials, smart materials, servo control, and modulating friction. For various reasons I decided not to pursue these options.

One technology that I found particularly interesting were these binary stiffness compliant mechanisms. The binary stiffness comes from the angled flexures on the mechanism either being locked out causing them to be completely rigid or being released which allows them to be very compliant. My theory was that if I could make a system where these supports were not a binary stiffness and instead had a range of stiffnesses, I would be able to create a mechanism that could effectively provide a range of impedances for a prosthetic ankle. To learn more about these mechanisms you can watch [this video](https://youtu.be/CV-zewG3lBY?si=0uB2ktMFIXk5ecfW) and I've included an image of one down below with the important features labeled.

![labeled_compliant_mech](https://github.com/LizMetzger/Sloth-bot/assets/113066141/69dad754-f5d0-4d69-9ad3-eb84bfd0a211)

My concept to create angled flexures which had a range of stiffnesses was to use a spring and change the number of active coils. Changing the number of active coils actually changes the k constant of the spring which changes the amount of force needed to compress the spring x distance, thereby changing the stiffness of the spring. This leverages the formula: 

I initially designed a winged piece that could be threaded through a spring and spun through the coils to either engage more or less coils (seen below). Once I had a first prototype of this design I did some preliminary force testing to see if the force to compress the spring changed depending on the number of coils engaged. I found that there was an increase in the stiffness when less coils were engaged and a decrease in the more coils that were engaged. After obtaining this proof of concept I moved on to refining my design.

![mechanism_v1](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/22f153f3-67a2-400d-95a8-6acfa69f7d81)

## Design refinement:

My initial design had many flaws but one key issue: I couldn’t change the number of coils without changing the distance between the top and bottom plate. To combat this, I decided to make the winged piece circular so it had more surface area to engage active coils. I also made a hole in the center of the winged piece and designed a linear guide that went in the hole and engaged with the circular piece. This allowed me to attach a servo to the linear guide which drove the rotational motion of the winged piece through the coils of the spring while allowing the circular piece to move in a passive manner up and down the guide.

![Figure1](https://github.com/LizMetzger/Sloth-bot/assets/113066141/17999379-ffa5-464e-a3ab-b6e7f6bb07a5)

For this mechanism to work properly it was important to have as tight of a fit between the circular piece and the linear guide as possible to decrease the amount of slack in the system. Having more features that were engaging between the pieces also helped it rotate easier even under loads. It is also important that the pitch of the wing piece matches exactly with the pitch of the spring. This improves the ease with which it can be threaded through the spring and prevents any unwanted compression of the spring.

This was a good first step to solving the problem of changing the distance between the plates, but I soon realized that when you actually applied a force to the bottom plate there was nothing to stop the winged circular piece from sliding all the way up the linear guide until it hit the servo. This provided absolutely no resistance to the compression and created a large range of motion with zero impedance. It also meant that once the spring actually started being compressed all of the coils were still being engaged.

![first_iter_before_after](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/2ebf623e-008b-47a7-b7dc-448a2436a715)

I fixed this problem by designing a threaded outer housing that the circular winged piece could engage with and thread through as it was driven through the coils of the servo. I fixed this outer housing to the top plate using standoffs so it was always a constant distance from the plate and servo. This meant that as the servo drove the winged piece through the spring it was also being threaded through the outer housing so that when the bottom plate was pressed the winged piece could not move and the spring would be immediately engaged.

{% include youtube.html id="enZqdokqWhk" %}

## Testing and Results:

To evaluate if my mechanism was actually changing the stiffness of the spring in the predicted way, I did some quantitative testing of the mechanism using an Instron force machine. I placed the canister in the machine and recorded the force required to reach a constant displacement. The machine kept track and stored the data for the detected force at all displacements so I was able to graph and extrapolate a k value.

{% include youtube.html id="MuNFp_xrCB8" %}

I recorded five trials at each of the different numbers of active coils (2, 2.5, 3, 3.5, 4, 4.5, 5, 5.5) and plotted the calculated k of the spring against the theoretical values of the spring. The k of the spring was found using 

![k_formula](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/6cb34d63-8336-4e99-aea1-ec37d4f9bd41)

and the theoretical values were calculated using 

![k_spring_formula](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/293addd3-3c1c-41be-a554-cb1b494a9715)

where d = 0.125in (3.175), G = 12,000,000 psi (79,00 Mpa), D = 1.219in (30.9626mm), and na is the number of active coils. Below is a graph of my experimental results obtained by using the Instron compared to the theoretical values and I found that my results almost perfectly matched the theoretical results. I believe that the offset is due to compliance in the 3D printed and laser cut components of the device.

![linear_cansiter_test_results](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/3efbd44f-4fe8-40c4-aad6-b05f0c2b12cf)

# Ankle Prosthetic Design

## Design Inception:

The overall structure of the design was inspired by the binary stiffness compliant mechanisms as the springs are acting like the angled flexures and the position of the arms on my device are meant to reflect the piece that the angled flexures connect to. The rotary flexures are replaced by the arms and their ability to rotate around a pivot point. I tried to position the arms of the device so that the springs would deflect as little as possible during rotation.

![hand_drawings](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/50cb8ba0-e7d9-4151-8c50-00ff1988c49e)

## Mechanism Integration:

Since dorsiflexion is much more important to preserving a natural gait and has a much larger range of impedances than plantarflexion I decided to only install my dynamic impedance mechanism on the front of the ankle which controls dorsiflexion. This allowed me to design faster, decrease the overall profile of the design, and kept the system simpler. In the future it would not be unreasonable or difficult to install a second dynamic stiffness mechanism in the back of the foot.

Both of the arms of the device rotate around a common axle but rotate independent of each other so when one side compresses the other side does not stretch. The springs are press slots on the arms that keep them in place during ankle rotation, but allows the spring some movement so that it doesn’t stretch. The back spring – responsible for plantarflexion– is also tightly press fit into the housing of the ankle which keeps it in place. 

![ankle_prosthetic](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/2e6b5909-95be-4453-b8cf-d6e60981c24e)

## Testing and Results:

At this point I wanted to validate that embedding the linear design I had into an ankle resulted in a change in the impedance of the joint. I did this by using the Instron machine to measure the required force to reach a specific displacement. I placed the whole ankle design into the machine and positioned it so that the foot would press down on the ball of the foot to rotate the joint as the machine pressed down. I once again recorded five trials at each of the different numbers of active coils (2, 2.5, 3, 3.5, 4, 4.5, 5, 5.5) and saved the force and displacement data from the Instron. In this testing we also put an inclinometer on the end of the foot to make sure that the angle the foot was reaching was relatively constant as the displacement was constant.

{% include youtube.html id="XcNMX_NZiDs" %}

I used the captured data to calculate the impedance using this equation: 

![impedance_calc](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/96f95e08-7e09-48c4-8263-c09f41bf07bc)

And I found that changing the number of coils changed the impedance of the joint as can be seen in the graph below and I moved onto integrating sensors into my design.

![ankle_prosthetic_impedance_graph](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/bf045a81-03ba-4e92-aafb-c7bc8381ff0a)

## Sensor Choices and Installation:

In order to get the information I needed to design a control loop for my ankle I included a FRS and an encoder into the design of my device. The FRS provides readings about the amount of force that is being placed on the prosthetic, so when a person is standing only on the prosthetic there should be the highest force readings and when the foot is in the air there should be little to no force detected. I accomplished this by sandwiching the FRS between two parts on the bottom of the ankle so when the user steps down the pieces get pushed together and exert force on the sensor, but the gap is big enough that when the foot is off the floor there is zero force on the sensor. Knowing the force that is on the foot is important because I only want adjust the stiffness of the spring while the user’s foot is off the ground to minimize the force that the servo has to overcome to adjust the wings. Having less force allows for the use of a smaller and less expensive servo 

I also chose to use a quadrature rotary encoder to measure the angle that the ankle is at. I chose this specific encoder because the Tiva API has a library meant to interface with quadrature encoders. The encoder was mounted on the side of the device with heat inserts that were put into the housing. An 8mm shoulder was used to track the rotation of the angle since the foot is rigidly connected to the shaft so that as the angle of the ankle changes the encoder value changes accordingly. This ridgid attachment is achieved by screwing into the top of the part that connects the foot to the shaft and into a key in the shaft. This ensures that the foot and shaft rotate together for accurate encoder readings which are important to characterize the user’s gait. 

![labeled_sensors](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/0e52f510-7e84-4ff3-a23e-cecacfbcdc1e)

# Software and Electrical

## Power and Wiring:

To power my device I used three different power supplies. I used a 6V battery pack that passes through a 5V buck converter to power my TIva Launchpad,SN75HC126N non-inverting buffer, and encoder. I use the 3.3V from the Tiva to power the FSR and I also use a 11.2V lipo battery to power my servo. 

My Tiva board had two pins configured to GPIO TX and RX which were used to communicate with the servo. I also had two pins on the Tiva configured for use with a quadrature encoder and set to read an A and B channel. By comparing the rising and falling edges of these two channels the direction of motion and the position of the encoder can be extrapolated. Finally I configured a pin to read ADV values so that I could measure the change in resistance of my FSR. Reading this change in resistance let me approximate the force that my sensor was experiencing.

![circuit_rrosthetic](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/923645ca-a908-45b9-8e8e-b983a6319354)

In my design I used a non-inverting buffer to fake whole duplex communication between the Tiva (uses whole duplex) and the servo (used half  duplex). Basically the Tx of my Tiva was wired to the input of one of the buffers and the data cable from the circuit was connected to the output of that buffer. The servo data was also connected to the input of another buffer that was wired to the Rx of my Tiva board. This was done so that when one buffer was high the board was only transmitting information to the servo and the other line was essentially dead. Then when the transmission was done the Tx pin could be set to low and the Rx pin would get set to high switching the board from being in transmitting mode to receiving. This system ensures that the board is always either transmitting or receiving as it would with half duplex communication allowing me to interface with the servo.

![half_duplex](https://github.com/LizMetzger/prosthetic_ankle/assets/113066141/07d90424-90e3-4175-8511-54c0891c1133)

## Code Development:

The code for this project revolved around writing a controller that could take the data from the FRS and the encoder and control the servo to adjust the impedance of the joint accordingly. All of the development was done using C specifically for my embedded system. I wrote a library for each one of my sensors and one for my servo which was used in my main controller script. 

The first step in getting my controller working was getting each of my individual components working with the Tiva. To get my FRS integrated I enabled its pin on the Tiva for reading ADC and wrote a function to read the data off the pin and convert it into units of force. I then wrote a function to write the data over UART so I could see the sensor readings. 

To enable my encoder I configured two of the Tiva’s pins, one to read the encoder’s A channel and one to read the encoder’s B channel. To use the QEI library for quadrature encoders I had to use one of the locked pins on the Tiva, so I made sure that the data was being read correctly using an oscilloscope. I was also able to verify that the pins were configured correctly by writing functions to send the encoder data over UART as well. 

The most difficult element to get working with the Tiva was the servo since there was not a version of the Dynamixel SDK that compiles on this microcontroller. Due to this I wrote functions that recreate the packets that dynamixel uses to send commands to their servo. I used one of their functions to generate the right CRC’s for each packet and sent them byte-wise over UART TX to the servo. When the message is done being sent I switch the controller from TX to RX mode so that it can receive a status packet or a reply from the servo. The controller only switches into TX mode when it is sending a message, otherwise it waits in RX mode. To debug these functions I used a logic analyzer to see exactly what bytes my code was sending to the servo compared to the standard Dynamixel SDK.

Once I had all of the components working individually I wrote a simple controller that would read the force data and wait for the user’s foot to be off the ground before it was sent a position control message to the servo and adjust the impedance. The foot being off the ground was detected by the FRS readings being below a certain threshold and the servo would only receive one position to move to per step. In the video below you can see how the wings adjust when my foot is off the ground.


{% include youtube.html id="EPWIFkha0Og" %}


Check out my [github repo](https://github.com/LizMetzger/prosthetic_ankle) for this project! And feel free to reach out with questions!
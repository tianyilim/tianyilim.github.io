---
layout: page
title: Portfolio
permalink: /portfolio/
---

Here is a (non-exhaustive) list of the things I've been working on.

I always try to learn something new in each of these projects, and most of the time I do.

Hopefully these projects inspire you to do something cool of your own.

If you have any questions about these projects, I would love to get in touch!

---
<br>
<a name="sumobot2022">

# SumoBot 2022 (Work in Progress)
Coming back to the UK to study has been wonderful and I can't wait to build robots properly again. This robot will be an evolution of the previous Sumobot my team and I built in [2020](#sumobot2020).

<p align="center">
  <img width="500" src="../assets/Sumobot_2022/Pagoda v2 v144_hires.PNG">
  <br>
  <i>A rendering of the robot, showing a bit of the front scoop, RPLidar, and drive system.</i>
</p>

We plan to use a RPLidar to perform some localisation of our robot with our opponent, opening up some interesting strategy possibilities.
Perhaps we will integrate an optical flow sensor, an IMU, and motor encoders to track where our robot currently is on the field.
My hope is that I will _finally_ learn how to use ROS through this!

---
<br>
<a name="hexapod">

# Hexapod Robot
Over my Summer break in 2020, I built a 18-DOF hexapod robot. It had always been a dream for me to build something like this, and I'm glad that (perhaps because of COVID?) I finally had the time to do just that.

I mostly tried to follow Oscar Liang's wonderful [pointers](https://oscarliang.com/inverse-kinematics-implementation-hexapod-robots/) on Inverse Kinematics, adapting it to my use case. I found the trigonometry quite engaging to do, but stopped short of implementing rotational/translational matrices.

I mainly used a Raspberry Pi Zero, two PCA9685 I2C servo controllers, and a mix of SG90/MG90 hobby servos for the hardware. The chassis was modelled in Fusion 360.

<p align="center">
  <img width="400" src="../assets/Hexapod/hex_design_process.png">
  <br>
  <i>Desiging the robot in Fusion 360.<br>There were multiple components in the model and it taught me a lot about proper parametric design.</i>
</p>

The robot was then programmed in Python. I had just picked up how to use Matplotlib, Jupyter, and Numpy, so I decided to develop and test my walking algorithms using these technologies.

<p align="center">
  <img width="400" src="../assets/Hexapod/hex_render.jpg">
  <br>
  <i>A render of the robot. Eventually I would use MG90 metal gear servos for the inner leg joints.</i>
</p>

On the firmware front, I interfaced with the PCA9685 servo controllers with Adafruit's software library. As I had opted to use a 2-cell LiPo battery as my power source, I opted to include an INA219 I2C current/voltage sensor to act as a rudimentary battery management system. A SSD1306 OLED screen was used so I could read out the current battery voltage (and other parameters). Again, Adafruit's Python libraries proved invaluable.

<p align="center">
  <img width="400" src="../assets/Hexapod/hex_photo.jpg">
  <br>
  <i>The final robot, in the flesh!</i>
</p>

I managed to complete the robot in time for a showacase the Imperial College Robotics Society was running at the time, and my video below impressed the juding commitee enough for me to win £40 from them :)

As I do more projects, my desire to stay closer to the algorithms and software increases. Perhaps I'm getting too old to solder...

_Video:_ <https://www.youtube.com/watch?v=wyfHojq9Sp4&ab_channel=TianYiLim><br>
_GitHub Repo:_ <https://github.com/tianyilim/walkybot_code>

---
<br>
<a name="marsrover">

# Mars Rover

FPGA stuff here! Implementing the Hough Transform in Verilog was quite a fun experience.

---
<br>
<a name="sumobot2020">

# SumoBot 2020

In 2020, my first year at Imperial, I joined the Imperial College Robotics Society's Sumobot competition.

<p align="center">
  <img width="400" src="../assets/Sumobot_2020/sb2020_team.jpg">
  <br>
  <i>Some members of my fantastic team, along with our robot.</i>
</p>

My team had grand plans for autonomy, but _someone_ (read: the author) accidentally plugged the batteries into the robot the wrong way the night before the competition. I then had to fix up a RC reciever to it along with an Arduino to give remote control to the robot.

<p align="center">
  <img width="400" src="../assets/Sumobot_2020/sb_wiring.jpg">
  <br>
  <i>Perhaps our wiring could have been re-thought.</i>
</p>

The robot chassis performed well, but unfortunately its autonomous performance will always remain a what-if for me.

<p align="center">
  <img width="400" src="../assets/Sumobot_2020/pagoda.jpeg">
  <br>
  <i>Can you guess why we named our robot "Pagoda"?</i>
</p>

---
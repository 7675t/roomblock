# Roomblock [![Build Status](https://travis-ci.org/tork-a/roomblock.svg?branch=master)](https://travis-ci.org/tork-a/roomblock) [![Snap Status](https://build.snapcraft.io/badge/7675t/roomblock.svg)](https://build.snapcraft.io/user/7675t/roomblock)

Roomblock is a robot platform consists of a Roomba, a Raspberry Pi 2,
a RPLIDAR A2 and a mobile battery. It is good for the learning of the
ROS navigation system.

<img src="doc/img/roomblock_1.jpg" alt="Overview of Roomblock system" width="320"><img src="doc/img/roomblock_2.jpg" alt="Overview of Roomblock system" width="320">

## Hardware

### Roomba

We use Roomba 535 and 780. Roomba 500, 600, 700 and 800 series is
available to build the system. Caution, Roomba 900 series are not
available because they have no serial port.

### Raspberry Pi

We use Raspberry Pi 2 Model B. Raspberry Pi 3 may be available but
not confirmed yet.

### WiFi dongle

We use this WiFi dongle for the Raspberry Pi.

- [Planex GW-USNANO2A](https://www.amazon.co.jp/gp/product/B00ESA34GA)

This is small and no problem on Raspbian kernel. However, on new
Ubuntu kernel, it may cause a problem of frequent disconnection. To
avoid this, you need to install
[the fixed driver](https://github.com/pvaret/rtl8192cu-fixes). See:

- https://adamscheller.com/systems-administration/rtl8192cu-fix-wifi/
- http://l-w-i.net/t/ubuntu/wifi_001.txt

### USB serial cable

You need a USB-serial converter to make the Roomba and Raspberry Pi
communicate. We notice this product is very handy to make the
cable. Just cut the cable and solder to mini-DIN 9 pin connector.

- [FTDI USB-Serial converter cable(5V)](http://akizukidenshi.com/catalog/g/gM-05841/)

### Frame

3D printable data (STL) are available in
[Thingiverse](http://www.thingiverse.com/).

- [Roomblock: robot for learning navigation on ROS](http://www.thingiverse.com/thing:2209131)

## Software

### Ubuntu

Download the Ubuntu image file for Raspberry Pi2 from https://wiki.ubuntu.com/ARM/RaspberryPi#Raspberry_Pi and dump to a micro SD card.

```
$ unxz ubuntu-16.04.2-preinstalled-server-armhf+raspi2.img.xz
$ dd bs=4M if=ubuntu-16.04.2-preinstalled-server-armhf+raspi2.img of=<dev file of your SD card>
```

### ROS

Install ROS Kinetic following the install guide:

- [Ubuntu install of ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu)

Install script install_roomblock.sh may be helpful if you have same
hardwares to us.

Clone roomblock source code:

```
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/src
$ git clone https://github.com/tork-a/roomblock.git
$ cd ~/catkin_ws
$ rosdep install --from-paths src --ignore-src -r -y
$ catkin_make
```

### Raspberry Pi camera module

If you have a Rasberry Pi camera module, you need to install
libraspberrypi-dev. Somehow this package conflict with system package,
you need to overwrite the package explicitly.

```
$ apt download libraspberrypi-dev
$ sudo dpkg -i --force-overwrite libraspberrypi-dev.deb
$ sudo sh -c "echo 'start_x=1' >> /boot/config.txt"
$ sudo sh -c "echo 'gpu_mem=128' >> /boot/config.txt"
```

And you can use raspicam_node to use the camera on ROS:
```
$ git clone https://github.com/UbiquityRobotics/raspicam_node.git
$ catkin_make
```

### Adjust clock both in Raspberry Pi and local PC

Raspberry Pi has no RTC(Real Time Clock), so the system clock is
always diverged from the correct time at each boot time. It causes
annoying problems. For example, move_base produce tf timeout error as:

```
[ WARN] [1456505621.806228153]: Costmap2DROS transform timeout. Current time: 1499505621.8061, global_pose stamp: 1499404347.1919, tolerance: 10.0000
```

You need to adjust the clock of Raspberry Pi and your local PC.
Do one-shot NTP(Network Time Protocol) adjustment as follows:

```
$ sudo ntpdate ntp.ubuntu.com
```

You can see the clock is adjusted by the message like:

```
20 Jul 11:37:19 ntpdate[9333]: adjust time server 91.189.91.157 offset 0.016635 sec
```

You can use 'ntpd' or 'chrony' to adjust the clock
automatically. Please search by yourself how to do so.

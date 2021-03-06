# sick_scan
## Table of contents

- [Supported Hardware](#supported-hardware)
- [Start node](#start-node)
- [Bugs and feature requests](#bugs-and-feature-requests)
- [SLAM-Support](doc/slam.md)
- [Radar](doc/radar.md)
- [Testing](#testing)
- [Creators](#creators)

This stack provides a ROS driver for the SICK laser and radar sensors mentioned in the following list.
The SICK MRS6124 is a multi-layer, multi-echo 3D laser scanner that is geared
towards rough outdoor environments. 

## Supported Hardware

This driver should work with all of the following products.

ROS Device Driver for SICK laser and radar sensors - supported scanner types: 



| **device name**    |  **part no.**                                                                                                                | **description**                                | **tested?**     |
|--------------------|------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------|:---------------:|
| MRS6124            | [6065086](https://www.sick.com/de/en/detection-and-ranging-solutions/3d-lidar-sensors/mrs6000/c/g448151)                         | 24 layer max. range: 200m, ang. resol. 0.13 [deg] hor., 0.0625 [deg] ver. | ✔ [stable]|
|                    |                                                                                                                                  | Scan-Rate: 10 Hz                       |                 |
| MRS1104            | [1081208](https://www.sick.com/sg/en/detection-and-ranging-solutions/3d-lidar-sensors/mrs1000/mrs1104c-111011/p/p495044)         | 4 layer max. range: 64m, ang. resol. 0.25 [deg] hor., 2.50 [deg] ver.                                         | ✔ [stable]|
|                    |                                                                                                                                  | Scan-Rate: 50 Hz, 4x12.5 Hz            |                 |
| LMS1104            | [1092445](https://www.sick.com/ag/en/detection-and-ranging-solutions/2d-lidar-sensors/lms1000/c/g387151)                         | 1 layer max. range: 64m, ang. resol. 0.25 [deg] |  ✔ [stable]|
|                    |                                                                                                                                  | Scan-Rate: 150 Hz, 4x37.5 Hz   |                 |
| TiM551             | [1060445](https://www.sick.com/de/en/detection-and-ranging-solutions/2d-lidar-sensors/tim5xx/tim551-2050001/p/p343045)                 | 1 layer max. range: 10m, ang. resol. 1.00[deg] | ✔ [stable]|
|                    |                                                                                                                                  | Scan-Rate: 15 Hz   |                 |
| TiM561             | [1071419](https://www.sick.com/de/en/detection-and-ranging-solutions/2d-lidar-sensors/tim5xx/tim561-2050101/p/p369446)                 | 1 layer max. range: 10m, ang. resol. 0.33 [deg]| ✔ [stable]|
|                    |                                                                                                                                  | Scan-Rate: 15 Hz   |                 |
| TiM571             | [1079742](https://www.sick.com/de/en/detection-and-ranging-solutions/2d-lidar-sensors/tim5xx/tim571-2050101/p/p412444)                 | 1 layer max. range: 25m, ang. resol. 0.33 [deg]| ✔ [stable]|
|                    |                                                                                                                                  | Scan-Rate: 15 Hz   |                 |
| LMS511-10100 PRO   | [e.g. 1046135](https://www.sick.com/de/en/detection-and-ranging-solutions/2d-lidar-sensors/lms5xx/c/g179651)     | 1 layer max. range: 80m, ang. resol. 0.167 [deg]| ✔ [stable]|
|                    |                                                                                                                                  | Scan-Rate: 100 Hz   |                 |
| LMS1xx-Family      | [e.g. 1041114](https://www.sick.com/de/en/detection-and-ranging-solutions/2d-lidar-sensors/lms1xx/c/g91901) | 1 layer max. range: 28m, ang. resol. 0.25 [deg]| ✔ [stable]|
|                    |                                                                                                                                  | Scan-Rate: 15 Hz   |                 |
| RMS3xx             | [8021530](https://cdn.sick.com/media/docs/4/04/504/Operating_instructions_RMS3xx_en_IM0075504.PDF)| Radar Sensor | ✔ [stable]|
|                    |                                                                                                   | Opening angle: +/- 50 [deg]   |                 |

##  Start Node

Use the following command to start ROS node:

For MRS6124:
roslaunch sick_scan sick_mrs_6xxx.launch

For MRS1104:
roslaunch sick_scan sick_mrs_1xxx.launch

For LMS1104:
roslaunch sick_scan sick_lms_1xxx.launch

For TiM551, TiM561, TiM571:
roslaunch sick_scan sick_tim_5xx.launch

For LMS1xx-family:
roslaunch sick_scan sick_lms_1xx.launch

For LMS5xx-family:
roslaunch sick_scan sick_lms_5xx.launch

For RMS3xx-family:
roslaunch sick_scan sick_rms_3xx.launch (under development)

### Setting ip adresse for scanner

To start the scanner with a specific IP address, the launch command can be used for most launch files as follows.
The hostname is the ip-address of the scanner:

```bash
roslaunch <launch-file> hostname:=<ip-address>
```
e.g.
```bash
roslaunch sick_scan sick_tim_5xx.launch hostname:=192.168.0.71 
```

### Start multiple Nodes

Take the launchfile "sick_tim_5xx_twin.launch" as an example.
Rempping the scan and cloud topics is essential to distinguish the scanndata and provide TF information.

## Sopas mode
This driver supports both COLA-B (binary) and COLA-A (ASCII) communication with the laser scanner. Binary mode is activated by default. Since this mode generates less network traffic.
If the communication mode set in the scanner memory is different from that used by the driver, the scanner's communication mode is changed. This requires a restart of the TCP-IP connection, which can extend the start time by up to 30 seconds. 
There are two ways to prevent this:
1. [Recommended] Set the communication mode with the SOPAS ET software to binary and save this setting in the scanner's EEPROM.
2. Use the parameter "use_binary_protocol" to overwrite the default settings of the driver.
3. Setting "use_binary_protocol" to "False" activates COLA-A and disables COLA-B (default)


## Bugs and feature requests

- Stability issues: Driver is experimental for the RMS3xx 
- Sopas protocol mapping:
-- All scanners: COLA-B (Binary)
- Software should be further tested, documented and beautified
- Setting of "topic" should not be hardcoded to /cloud in the future. This allows the simultaneous operation of several scanners. Each point cloud can then be converted using its own TF transformation.

## Troubleshooting 

1. Check Scanner IP in the launch file. 
2. Check Ethernet connection to scanner with netcat e.g. ```nc -z -v -w5 $SCANNERIPADDRESS 2112```.
   For further details about setting up the correct ip settings see [IP configuration](doc/ipconfig/ipconfig.md) 
3. View node startup output wether the IP connection could be established 
4. Check the scanner status using the LEDs on the device. The LED codes are described in the above mentioned operation manuals.
5. Further testing and troubleshooting informations can found in the file test/readme_testplan.txt
6. If you stop the scanner in your debugging IDE or by other hard interruption (like Ctrl-C), you must wait until 60 sec. before
   the scanner is up and running again. During this time the MRS6124 reconnects twice. 
   If you do not wait this waiting time you could see one of the following messages:
   * TCP connection error
   * Error-Message 0x0d
7. Amplitude values in rviz: If you see only one color in rviz try the following:
   Set the min/max-Range of intensity display in the range [0...200] and switch on the intensity flag in the lauch file  
8. In case of network problems check your own ip address and the ip address of your laser scanner (by using SOPAS ET).
   * List of own IP-addresses: ifconfig|grep "inet addr"
   * Try to ping scanner ip address (used in launch file) 
9. If the driver stops during init phase please stop the driver with ctrl-c and restart (could be caused due to protocol ASCII/Binary cola-dialect).
   
## SUPPORT
 
* In case of technical support please open a new issue. For optimal support, add the following information to your request:
 1. Scanner model name,
 2. Ros node startup log,
 3. Sopas file of your scanner configuration.
  The instructions at http://sickusablog.com/create-and-download-a-sopas-file/ show how to create the Sopas file.
* In case of application support please use [https://supportportal.sick.com ](https://supportportal.sick.com).


## Installation

In the following instructions, replace `<rosdistro>` with the name of your ROS distro (e.g., `indigo`).

### From binaries

The driver is released at longer intervals as a binary package and can therefore be installed via the package manager. To be able to use all new functions of the driver, the driver should be built from the sources published in this reposity:

`sudo apt-get install ros-<rosdistro>-sick_scan`

### From source

```bash
source /opt/ros/<rosdistro>/setup.bash
mkdir -p ~/ros_catkin_ws/src/
cd ~/ros_catkin_ws/src/
git clone -b devel --single-branch git://github.com/SICKAG/sick_scan.git
cd ..
catkin_make install
source ~/ros_catkin_ws/install/setup.bash
```

## Quick Start

```bash
roslaunch sick_scan sick_mrs6xxx.launch
rosrun rviz rviz
publish to point cloud
```
## Testing
The sick_scan_test program was developed for testing the driver. This program checks elementary properties of the scanner. In a first implementation stage, the shots per scan are checked. The test program works according to the following principle:
1. The parameters from an original launch file are read.
2. These parameters are modified according to the instructions in the test control file.
3. The modified parameters including all other parameter settings from the original launch file are copied to a test launch file.
4. The test launch file is started.
5. The parameters are checked.
6. The result of the check is transferred to a result file.
The basic procedure can be seen in the following figure:
![Alt text](./sick_scan_test.png?raw=true "princile of test program")
More information about the structure of the individual files in the test run can be found [here](test/sick_scan_test.md):


## Keywords

MRS1000 
MRS1104 
LMS1000 
LMS1104 
MRS6000 
MRS6124 
RMS3xx 
RMS320 
ROS LiDAR 
SICK LiDAR 
SICK Laser 
SICK Laserscanner 
SICK Radar 
LMS1xx 
MRS1xxx 
LMS1xxx 
MRS6xxx 
TiM5xx 
TiM551 
TiM561 
TiM571 
LMS5xx 
LMS511 


## Creators

**Michael Lehning**

- <http://www.lehning.de>

on behalf of SICK AG 

- <http://www.sick.com>

------------------------------------------------------------------------

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/f1/Logo_SICK_AG_2009.svg/1200px-Logo_SICK_AG_2009.svg.png" width="420">

![Lehning Logo](http://www.lehning.de/style/banner.jpg "LEHNING Logo")



# Mesquite-Mocap.github.io

The goal of this is to document the efforts for IMU Based Motion Capture

Visit the site: https://mesquite-mocap.github.io

This site documents 

1. Overview 
2. Quick Start Guide 
4. Hardware (what hardware used and changes to the hardware) - solder diagrams 
5. Software:   approach (which repos do what), architecture diagrams and deployment strategy 
6. Links to relevant documentation (presentation)  


Website theme done by:

Editorial by HTML5 UP
html5up.net | @ajlkn
Free for personal and commercial use under the CCA 3.0 license (html5up.net/license)

# Overview
The overall scope of Mesquite Mocap is to make an open-source, low-cost motion capture process using IMU devices and javascript. A traditional mocap set up can cost upwards of $100k. Each IMU device starts at $35 dollars. By creating a system of many TTGO T-Watches, we can rig 3D models using motion data. This project is open-source and available for community developers and artists. Anyone can access the code, add to the code, or change it to their needs. All Mesquite Mocap code is under the GNU General Public License.
## Application and Terminology
There are multiple applications for this system: understanding movement, extending movement, and mapping movement to name a few. The uses of this system range depending on user specified projects. The code base is meant to be flexible enough to incorporate into a wide variety of applications.
Each Mesquite Mocap system consists of many seeds (the IMU watches) that form a pod (aggregate of watches). When there are systems with multiple mocap suits, then this becomes a tree. Seed is interchangeable with TTGO T-Watch, pod is interchangeable with an individual's full mocap suit, and tree is interchangeable with a system of multiple mocap suits. This will be the terminology going forward.


## High-Level Technological Overview 

![highlevel](https://mesquite-mocap.github.io/images/image1.png)


# What you need to get started
## Hardware
*LilyGo TTGO T-Watch 2019*
![LilyGo TTGO T-Watch](https://m.media-amazon.com/images/I/51OiZN0ZQoS._AC_SL1000_.jpg)
You can purchase this from [aliexpress](https://www.aliexpress.com/item/1005001581849024.html) or [amazon](https://www.amazon.com/LILYGO-GPS-Lora-Programmable-Environmental/dp/B07ZWXV5FQ).

*bno08x chip to be added to the T-Watch*
![bno08x](https://images-na.ssl-images-amazon.com/images/I/71GmWElPVaL._AC_UL232_SR232,232_.jpg) 
You can purchase this from [aliexpress](https://www.aliexpress.com/item/4000263329804.html) or [amazon](https://www.amazon.com/Taidacent-Nine-axis-High-Precision-Accelerometer-Magnetometer/dp/B0836WJLVH/) 

*Raspberry Pi zero*
![Pi Zero](https://images.prismic.io/rpf-products/bc1a8138-03b5-4f17-8786-294e8de27fa1_Pi%20ZERO%20TOP%20DOWN%20REFRESH_.jpg?ixlib=gatsbyFP&auto=compress%2Cformat&fit=max&q=50&w=600&h=400)

You can purchase this from [Raspberry Pi’s website](https://www.raspberrypi.com/products/raspberry-pi-zero/). There are a few links to various purchasing options, so pick the option that is right for you.

*router*
You will need some basic router to allow the Seeds to push data to the Raspberry Pi.

Most routers will work fine.

### Alteration to T-Watch and Soldering Guide
Here is a short [project](https://tejaswigowda.com/t-watch-bno085/) that demonstrates the power of the bno08x in combination with the T-Watch. The code for this test project is [here](https://github.com/tejaswigowda/t-watch-bno085). Also, this project will show you how to solder the two components together. This project is mainly to test if the components work with no malfunctions. If you do not wish to do the test project. Here is a breakdown of soldering these components together.
You need a 4 wire ribbon cable. Strip each end of all wires and tin them.
Solder the two boards together in the following order: VCC goes to VCC, SCL goes to 21, SDA goes to 22, GND goes to GND (you will need to reorder the wires to do this). It should look like this:   ![solderwires](https://tejaswigowda.com/t-watch-bno085/make1.jpg)
Put some electrical tape over the solder joints to prevent any sharp edges from puncturing components. Then squeeze the two boards back into the casing and screw it together. You should put them together something like this: ![casing](https://tejaswigowda.com/t-watch-bno085/make2.jpg)

## Software
You will need [Mesquite Mocap’s main repository of code](https://github.com/Mesquite-Mocap). The Quick Start Guide outlines any other necessary software.

# Quick Start Guide
Once you have all the altered hardware and software necessary, this quick start guide should get a new user from the ground floor to a functional mocap suit. It will take a new user through installation of dependencies, setup and connection.

## Getting Arduino to communicate with the T-Watch
First, you need to get Arduino in order to run code on the individual seeds. Download and install [Arduino](https://www.arduino.cc/en/Main/Software)
![Arduino](https://mesquite-mocap.github.io/images/image7.png)

### necessary externals
Once Arduino is installed, open it and go to the preferences. In the ‘Additional Boards Manager URLs, add this URL: https://dl.espressif.com/dl/package_esp32_dev_index.json
![externals](https://mesquite-mocap.github.io/images/image9.png)

Next, you will need to install a USB to [UART Driver](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers). Our computers use the USB protocol to communicate to other devices, whereas the Pod uses the UART protocol. This driver allows our computers to translate between these two protocols and upload data to the Pods.
![USB2UART](https://mesquite-mocap.github.io/images/image2.png)

Now, you need to install the [T-Watch library](https://github.com/Xinyuan-LilyGO/TTGO_TWatch_Library) from the official github. These should be all the external dependencies needed.

### connecting to the T-Watch
Connect a Pod via USB cable and choose your port in the Arduino IDE. On MacOS it should be obvious which port you need to choose and on Windows you need to guess and check until you find the correct port.
![choosingport](https://mesquite-mocap.github.io/images/image5.png)

Open the [pod-mcu-code](https://github.com/Mesquite-Mocap/pod-mcu-code). You will need to change lines 26-28. Make sure your router is plugged in and turned on. Add your router’s SSID, password, and serverIP address as the variables for line 26, 27, and 28 respectively. Compile and run the edited pMake sure your router is plugged in and turned onod-mcu-code on your T-Watch by clicking on the check mark and arrow buttons in the top left corner of the IDE. Open up the serial monitor to make sure the watch streams data. Also, you will want to write down the mac address for each watch as you go since we will use the mac address to map each joint later on. Repeat this process for each TTGO T-Watch in the pod.

## Raspberry Pi configuration
### installing node.js and other necessary software on raspberry pi
First, you need to copy this code into your terminal to install node.js and other dependencies for the system to work properly. 
‘’’
sudo apt update
sudo apt install nodejs npm git
sudo npm install -g forever http-server
‘’’


### finishing pi setup
To finish the [Raspberry Pi Zero](https://www.raspberrypi.com/products/raspberry-pi-zero/) setup, we will put the pi-ws-server-to-serial onto the pi. This takes the streamed data from the Seeds and  converts it to serial data. This makes it much easier to aggregate the data onto any computer plugged into the pi. Go to the terminal on your Raspberry Pi and clone the [pi-ws-server-to-serial repo](https://github.com/Mesquite-Mocap/pi-ws-server-to-serial), ‘cd’ to that repo. Then type ‘npm install’ and enter. Type ‘forever start server.js’ to start the server and keep it running. To start the server on boot you will need to add ‘forever start server.js’ to your ~/.bash_profile.

## Mapping and Connecting to a 3D Model
Clone the [web-client-serial](https://github.com/Mesquite-Mocap/web-client-serial) repo on to your computer. Navigate to the [mappings.js](https://github.com/Mesquite-Mocap/web-client-serial/blob/main/docs/mappings.js) file in the docs folder of the web-client-serial directory. Change the mac addresses from "00:00:00:00:00:XX" to the specific mac addresses of each of your T-Watches. This is why we wrote down the addresses for each watch earlier. The code shows which joint each Pod should be. If you do not already have them, you can find the mac addresses (along with the output data) of your T-Watch in the serial monitor in the Arduino IDE while it is plugged in and running the code. In your terminal on MacOS or terminal emulation on Windows ([GitBash](https://gitforwindows.org/) is a good one), ‘cd’ to the web-client-serial folder, then enter in ‘node server.js’. After this, you can go to the [local host](http://localhost:1234). When you hit the Connect button at the top, a drop down menu will show you the available serial ports. Click on the serial port that your Raspberry pi is sending data to. The 3D model on the screen should now move according to the Pod. Each T-Watch controls a different joint of the model based on your custom mapping.

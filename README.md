# Wiz_iot_cam
Wiz IoT Cam using W5300 TOE and 8051 limited resource uC<br>
Project Description : <br> Goal of this project is to show capability of W5300 TOE chip to interface it with low cost (0.2$) limited resources 8051 core based MG82F6D16 uC and develop IoT based camera to capture and transfer image via MQTT using onboard ARDUCAM 2MP camera to Wizcam android application running anywhere in world. (Without having luxury to use Arduino IDE and 3rd party libraries for W5300, Arducam OV2640 and MQTT :) )
<br>
Components used in project : <br>

W5300 Module WIZ830MJ  1 Pcs
MG82F6D16 (8051 Core) Low cost 0.2$ Microcontroller 1 Pcs
Arducam Mini 2MP SPI Camera  1 Pcs
Buzzer 3V3  1 Pcs
Misc (AM1117 3.3V LDO, Resistors, Capacitors, pushbutton, connectors, LEDs)
<br>Software Used in Project : <br>

MG82F6D16 firmware build using Keil C51 and also supported SDCC open source 8051 compiler.
MG82F6D16 SDK
Megawin 8051 COM Port ISP to upload firmware via UART to MG82F6D16.
Cool Term used as UART Debug Console
Processing for Android used to develop Host Android Applications
Deliverables (everything required for replicate this project are under CC-BY opensource license and attached in documents section of this project):<br>

MG82F6D16 uC firmware for Project  
Android Application Source code and APK file 
Schematic and Hardware interfacing guide
Challenges and solution:

Challenges<br>

Solution / Hacks<br>

Interface W5300 ToE module with MG82F6D16 8051 uC having only 24 GPIO

Implemented W5300 Indirect addressing with 8 Data lines to reduce pin usage.

Only 14 pins required for w5300 interface (8 Data lines, A0, A1, A2, /CS, /RD, /WR)

Yummy.. Still I have 10 pins left to manage Camera, Buzzer, UART Console and few LED interface with MG82F6D16 uC

Arduino like IDE not available for MG82F6D16 8051uC

Developed firmware from scratch that is compatible Keil C51 and SDCC Opensource 8051 tool chain.

Library not available for W5300 Initialization and TCP Socket Programming in indirect addressing mode.

Used datasheet of W5300 and developed low level IO Driver APIs and TCP Socket function from scratch for MG82F6D16 uC that can be also used with other uC with simple IO mapping and GPIO register mapping.

MQTT 3.0 Client implementation without any available library for limited resource MG82F6D16 8051 uC having only 1KB RAM and 16KB flash.

Developed MQTT v3.0 Client APIs from scratch on top of TCP socket functions for MG82F6D16 8051 uC.

Not enough pins available for OV2640 2MP Camera sensor interface with MG82F6D16 8051 uC.

Used ArduCam Mini 2MP SPI OV2640 Camera module in project that requires only 6 MG82F6D16 8051 uC GPIOs (2 for I2C and 4 for SPI)

No Arducam Library available for MG82F6D16 8051 uC.

Developed firmware for ArduCam APIs from scratch to initialize OV2640 for 320x240 Color JPEG image capture via I2C and read Captured image bytes via SPI.

Only 1 KB RAM of MG82F6D16 8051 uC is not enough to store JPEG image file.

Used smart way in firmware to give command to camera for capture JPEG image and at same time started putting MQTT Packet publish header bytes to TCP Socket and when image is ready, read one by one byte from ArduCam via SPI and send it to W5300 TCP socket TX FIFO until JPEG termination character received. Surprisingly this approach happily  end up with only 96 bytes of RAM utilization for entire final project firmware. 

After assigning Pins to W5300 I realized that Hardware I2C Pins of MG82F6D16 8051 uC is mapped as normal GPIO for W5300 Data lines no Hardware I2C pins available for OV2640 interface. 

Implemented bit banging for I2C implementation in MG82F6D16 8051 uC firmware as I2C only used for initialization of OV2640 on startup and not time critical in image transfer as image transfer is happily done with Hardware SPI running on 4 MHz SPI clock.

Through put

Achieved 3-5 320x240 JPEG Image publishing per second on MQTT broker running on local network, and 1-2 images publish per second achieved on public MQTT Broker (HiveMQ)/ MQTT Broker hosted on Amazon AWS EC2. 

 

WizCam Assembled Hardware :

        

     

WizCam Hardware Details : Following image shows components used on Wiz IoT Cam board.


 

WizCam Hardware connection diagram :  Following image shows connection diagram of Wiz IoT Cam board.


MG82F6D16 Micro controller Pin Diagram : 


WizCam Hardware Pin Mapping :  Following image shows pin mapping used on Wiz IoT Cam board.


W5300 Interfaced with MG82F6D16 using Indirect 8 bit addressing mode by 8 Data pins, 3 address pins (A0,A1,A2) and three control pins (/RD, /WR, /CS)
Apart from W5300 to MG82F6D16 8Bit Indirect Interfacing we need to keep BIT16EN and A3 to A9 pins of W5300 to LOW level.
Arducam Interfaced with MG82F6D16 using 100kHz I2C bus for configuration and 4MHz SPI  bus to transfer JPEG image.
WizCam Android Application :   WizIoTCam Android application is made using Processing For Android. Compiled Apk file and source code attached with this project for reference.

Application having following features,

MQTT Client to receive raw JPEG image byte array and control Buzzer on board
Display Latest Image received from Wizcam board on App main screen
Save every image with time stamp to local storage of Android Device
MQTT client and ketai third party library used in application
Following are few screen shot of app showing features..


WizCam Setup :  Attached Apk file is compiled for Broker.hivemq.com public MQTT broker, so any one can use it. topic names and payload description are shown in following figure.

DNS is not implemented on WizIoT Cam board firmware so one need to use nslookup to find IP address of broker and need to modify following lines in MG82F6D16 firmware to update broker IP address given by nslookup and Local IP Address based on your local network settings as shown below..





WizCam MG82F6D16 Firmware details : 

Keil-C51 or SDCC tool is compatible to compile this firmware
No third party libraries used in this project, W5300 low level drivers and Ethernet setup functions, MQTT Client functions, OV2640 Arducam Camera related functions are developed from scratch just using data sheet. 
W5300 Indirect Addressing mode with 8 bit data bus used is used to reduce required pin count to  only 14
here is firmware folder tree

MG82F6D16 Complete well commented firmware is attached with this project, It is meaningless to describe complete firmware here, but W5300 Low level IO drivers and important code snippets related to W5300 are as following, which can be modify and used for other micro controller as well.

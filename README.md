# Home Security System 
## Authors:
- Donia Ghazy - https://github.com/doniaGhazy
- Marwan Eid - https://github.com/marwan-eid
- Mohammed Abuelwafa - https://github.com/Osos3006


## Motivation:

Security nowadays is a matter of vital importance especially in homes and local stores for example. There are multiple systems developed for the purpose of security monitoring and protection. Cameras and smart lock systems are implemented in such places to enhance their security and to prevent thefts and heists as well. A home security system protecting one’s family and property and deterring burglars would offer one piece of mind whether s/he is sleeping or away from home.

## Proposed Solution:
- Our project uses a Passive Infrared Radiation (PIR) sensor, a camera, a buzzer, a led , an ESP32 cam module for Wi-Fi connectivity and having a webserver to implement a home security system.
- The PIR sensor detects the change in infrared radiation of warm blooded moving objects in its detection range (7 meters). Such change will be reflected as a digital output generated from the sensor.
- When the system is running and a burglar gets into the detection range of the PIR system, the following happens:
 1.  the camera is triggered and an instant picture of the burglar is sent to the owner (through a web server.)
 2. The buzzer rings imitating an electronic alarm 
 3. the led will be turned ON representing the lightings of the House. 


## System block Diagram:
![diagram](https://i.imgur.com/6Iu2arD.jpg)

### Input:
- Existence of a warm blooded person using the PIR sensor 

### Output:
- GPIO output signal for the LED.
- GPIO output signal for the Buzzer.
- Live footage of the Bulgar using the ESP32 CAM module. 

### Communication:
- UART for the communication between the ESP32 Cam and the STM32.
- Wi-Fi for the communication between the ESP32 Cam and any user Device in order to view the images.

## Components:
### Hardware Components:
1. STM32l432kc → main MCU 

![stm32](https://www.st.com/bin/ecommerce/api/image.PF263436.en.feature-description-include-personalized-no-cpn-medium.jpg)

* STM32 is an ARM cortex M4 based processor 
* it is used as Our main microcontroller as a central processing unit where it gets the input from the sensor, sends the output signals to the buzzer and the LED, and communicate to the ESP32 cam when to capture the Photos 
 
2. Ai-Thinker ESP32-CAM module

![ESP](https://i1.wp.com/randomnerdtutorials.com/wp-content/uploads/2019/03/ESP32-CAM-pinout-1.png?resize=547%2C235&quality=100&strip=all&ssl=1)

* The ESP-32 CAM provides the system with Wi-Fi connectivity 
* The ESP-32 CAM takes the character 'c' from the STM32l432kc through the UART link implemented on GPIO-16 (UART 2 in ESP32-cam only has an RX which is on GPIO-16), then it takes an image using the OV260 camera module mounted on it, stores the image on the SPI flash file system (SPIFFS)
* The ESP-32 CAM enables the system to have a web-server which provides the backend for the user interface. it basically gets the image from the SPIFFS and display it to the user. Moreover, we added an extra functionality for the user to be able to take a picture whenever he wants and display it even without having the alarm triggered in case he wants to check out his outdoors or heard something suspicious.
3. Active Buzzer 


![Buzzer](https://c1.neweggimages.com/ProductImage/AK7R_1_201908101335452878.jpg)

* the buzzer in our system imitates a real electronic alarm in order to produce sound when a burglar is detected.

4. LED

![led](https://5.imimg.com/data5/DN/SE/MY-3299289/5mm-led-light-emitting-diode-500x500.jpg)

* The LED in out system imitates a the lightings of a house where they are triggered using the GPIO output ON signal from the stm32l432kc. In real world context and a production level product would have a relay module that takes this output and lights the lightings of the house.
5. HC-SR501 → PIR motion detection sensor

![PIR](https://www.electronicwings.com/public/images/user_images/images/Sensor%20%26%20Modules/PIR%20Sensor/PIR%20Sensor.jpg)
* The PIR sensor has 3 Pins ( VCC , GND, Digital Output). The digital Output is 1 when it detects a warm blooded person.
* It has two modes:
 1.  the repeatable mode which recapture motion as it occurs after the duration of High time ends. However, two detections are separated
by a period of 3 seconds where the Sensor does not capture any motion. the sensor is on the repeatable mode by default and it suits our application.
 2. the non-repeatable mode which only captures the first instance of motion and sets the output to high for the delay adjust duration, then it goes to low. 
* The Sensor has a Delay time adjust which adjusts the period of high time when motion is detected from 5 seconds to 5 minutes. For the purposes of our application, we adjusted it to be 35 seconds.
* The sensor has a sensitivity adjust which adjusts the detection range between 3 meters and 7 meters. we adjusted it to the maximum which is 7 meters for maximum detection range. 
* More about the theory of operation can be found [here](https://osoyoo.com/2017/07/27/arduino-lesson-pir-motion-sensor/)
6. USB-to-TTL Module
<img src="https://sc01.alicdn.com/kf/H963930e63152448f95872461a5e81bf37/234439048/H963930e63152448f95872461a5e81bf37.jpg" width="250" height="250">

* The USB-to-TTL Module provides an interface for programming the ESP32-CAM (by connecting the UART interface in addition connecting the GPIO-0 on the ESP32-CAM to ground and pressing reset while uploading the code). Moreover, it provides an interface for Debugging Messages. 

### Software Components:
- Keil uVision5

```Buzz()```:

* This function turns the buzzer on and is triggered if the PIR sensor detects motion.
* This function generates a standard alarm like sound using a PWM signal by varying the prescaler by iterating through i =  15 to 75 with a step of 3 and the prescaler is assigned 2i
  	
```Light()```:

* This function turns the LED on and is triggered if the PIR sensor detects motion.

```main()```:

* This function includes code that reads the output pin of the PIR sensor to detect whether there is motion or not. In case there is no motion detected, the buzzer and the LED are turned off. If, however, motion is detected, both are turned on, and a flag is sent to the ESP32-CAM to capture a picture every other small delay.

- STM32CubeMX
<img src="https://github.com/Osos3006/Home-Security-System/blob/master/Images/STM%20system/CubeMX_Final.PNG" width="500" height="400">


- Arduino IDE 

### Libraries:

1. [Wifi.h](https://github.com/arduino-libraries/WiFi)
 
* With the Arduino Wi-Fi Shield, this library allows an Arduino board to connect to the internet.

* We use it for connecting the ESP32-CAM to the machine’s local network. 

2. [ESPAsyncWebServer.h](https://github.com/me-no-dev/ESPAsyncWebServer)

*  An Async HTTP and WebSocket Server for ESP8266 Arduino.

*  We use it for building the web server.

3. [SPIFFS.h](https://github.com/pellepl/spiffs)

* SPIFFS is a file system intended for SPI NOR flash devices on embedded targets.

* We use SPIFFS in order for the photos captured to be saved in it.

### Functions:

```CapturePhoto():```

* The capturePhoto() function is a JavaScript function that is triggered upon clicking the “CAPTURE PHOTO” button on the web page. The function sends a request on the /capture URL to the ESP32 so that it captures a new photo.

```Setup():```

In the setup() function, the following is done:

* Serial communication is initialized.

```Serial.begin(115200); ```
*  The ESP32-CAM is connected to the machine’s local network.

```WiFi.begin(ssid, password);```
* SPIFFS is initialized.

``` SPIFFS.begin();```
* Configurations and initializations for the OV2640 camera module are done.
* Cases for when the ESP32-CAM receives a request on a URL are then handled.
 1.  If the ESP32-CAM receives a request on the root / URL, the HTML text to build the home web page with the “CAPTURE PHOTO” and “REFRESH PAGE” buttons is sent back to the client.

 2.  If the “CAPTURE PHOTO” button is pressed on the web server, a request is sent to the ESP32 /capture URL. Then we set a flag to capture a new picture.

 3. If there is a request on the /saved-photo URL, the photo saved in SPIFFS is sent to the connected client.

* The web server is started.

```Loop():```

* ```capturePhotoSaveSpiffs(void):```

1. This function is called when the flag to capture a new picture is set to true.
2. The function captures a new photo with the camera and saves it to SPIFFS.

* ``` checkPhoto(fs::FS &fs):```

1. This function is used in the capturePhotoSaveSpiffs() function to check whether the captured photo was successfully saved to SPIFFS or not. If not, another photo is captured again.

Not using FreeRTOS was a compromise between the system complexity versus its stability at code changes, WCRT, and priorities. We chose not to complicate the system by adding RTOS since we are not expected to add future changes to the code.










# System Implementation:

## Circuit:

<img src="https://github.com/Osos3006/Home-Security-System/blob/master/Images/STM%20system/Final%20system%20TOP.jpg" width="500" height="400">

<img src="https://github.com/Osos3006/Home-Security-System/blob/master/Images/STM%20system/Final%20system%20side.jpg" width="500" height="400">

* motion detected

<img src="https://i.ibb.co/tccy5Jv/motion-detected.png" width="500" height="400">

* No motion 

<img src="https://i.ibb.co/rG3Nm63/No-motion.png" width="500" height="400">

* ESP32 validation that it is connected to STM32. otherwise it loops waiting for the UART connection which is critical to the system. 


<img src="https://i.ibb.co/fSRKTny/STM-BROKEN.png" width="500" height="400">

* ESP32 connected to Wi-Fi + saving images 


<img src="https://i.ibb.co/rdrXRr6/connect-to-wifi-save-image.png" width="500" height="400">


* Light ON/OFF , Buzzer ON/OFF


<img src="https://github.com/Osos3006/Home-Security-System/blob/master/Images/webserver/ON_OFF.PNG" width="500" height="400">


* Web Server preview (phase1)


<img src="https://i.ibb.co/SQ0kfX7/Capture.png" width="500" height="400">




<img src="https://i.ibb.co/CJfqmnv/room.png" width="500" height="400">

* Web Server Preview (Final)

<img src="https://github.com/Osos3006/Home-Security-System/blob/master/Images/webserver/logout%20button.PNG" width="500" height="400">


* Web Server Secured 


<img src="https://github.com/Osos3006/Home-Security-System/blob/master/Images/webserver/secure%20server.PNG" width="500" height="400">


* Logging out page 

<img src="https://github.com/Osos3006/Home-Security-System/blob/master/Images/webserver/logging_out.PNG" width="500" height="400">


## Limitations:
- The Esp32 Cam should be placed into a specific angle in order to detect people's faces clearly.  
- Esp32 cam is limited to only one client.
- We host the images on SPIFFS meaning that it stores a limited number of images, so in our project we only save one image at a time.
- Images on the server are not automatically refreshed but we need to refresh it manually every time to get the latest captured photo.
- For the User to turn Off or ON either the light or the Buzzer : He has to press 'l' for light and 'b' for Buzzer in a terminal connected to STM MCU through UART. This is going to be addressed in the future works.

## Future work:
- To overcome the problem of SPIFFS, we could use the micro SD port in the ESP32 cam and store the images to it. This will give the room to store more images. 
- Instead of using LEDs, we could use a relay module and mounting some real lamps.  
- We could Implement more functionalities in the web server such as adding support to multiple clients using a network of esp32 cams. This will provide coverage to a higher range from different angles.  
- A mobile application that reads the input from the webserver that connects multiple units of out system can be developed as well. 

## Challenges 

- We had to use Arduino IDE for the ESP32 cam not the Keil uVision (our main MCU IDE) as it has ready to use libraries and we found it difficult and time consuming to implement the ESP32 webserver and Camera interfacing on the ESP using Pure C on keil without any support from the community. however, the open source community recommended the use of the available libraries on Arduino IDE and that it can be used for production code. 

- The ESP32-CAM have no USB interface which made it difficult to program it, debug it and test the code on its own since the process of programming it includes connecting GPIO-0 and GND. then uploading the code while pressing reset in a specific point of time, then you have to disconnect the GPIO-0 and GND. Finally, you have to reset the system again in order for it to function. Moreover, the ESP32-CAM reset button is on the bottom of the board which made it not friendly for being fixated on the breadboard. hence, we depended on a primitive setup in order to fix the components for testing.

- The ESP32-CAM has one UART for Debugging information and programming it using a USB-to-TTL module. It has another UART only which has only RX implemented which made it difficult to transmit information from the ESP32 to the STM32 using UART. hence, another solution was figured out which is bluetooth pairing using a BT module like : HC-05 as discussed in the future work.

- This project was our third attempt for the semester since we had difficulties in the first attempt because the components were not available in Egypt and the second project idea was not feasible. hence, we were stressed in a very short time span to implement our project, test it and present it. 




## References
- [PIR sensor Theory (1)](https://osoyoo.com/2017/07/27/arduino-lesson-pir-motion-sensor/)
- [PIR sensor Theory (2)](https://components101.com/sensors/hc-sr501-pir-sensor)
- [ESP-CAM Web Server Interface](https://randomnerdtutorials.com/esp32-cam-take-photo-display-web-server/)


## Phase 1 Presentation
[Presentation Video](https://drive.google.com/file/d/1TkcKbtvFDXxx2nX3FWF_YyAYJsaFXKdD/view?usp=sharing)


[Slides](https://docs.google.com/presentation/d/1SmR-bazTPTJB2a8tqhuuUM6-uZVxIajpBRcfrtBjAu8/edit?usp=sharing)

## Final Demo Presentation

[Final Demo Presentation Video](https://drive.google.com/file/d/1DP7ZXKHaYc8dApzmEjHeYnpTyuE7WUV4/view?usp=sharing)

[Slides](https://docs.google.com/presentation/d/1-mAV8R1xnsgdCC_5jdVkTzcj8KsvIuzWH_uqOgVHZxU/edit#slide=id.p)


---
author: SYBA Squad
geometry: "left=2cm, right=2cm, top=2cm, bottom=2cm"
---

# SYBA Squad | DECO3801

### Brief: 015 Exploring a Universal Gestural Language

The project consists of three main components:
-  The gesture recognition program and GUI
    - The gesture recognition program uses a machine learning model to recognise gestures and then publishes messages to the MQTT broker on the relevant topic to control smart devices.
-  Home Assistant with integrated smart devices
    - Home Assistant provides a platform for home control and home automation. It is used to host an MQTT broker and integrate smart devices so that they can be controlled through the MQTT protocol.
-  The gesture configuration web interface
    - The web interface allows the user to configure customised gestures to control their smart devices.

## Table of Contents

- [SYBA Squad | DECO3801](#syba-squad--deco3801)
    - [Brief: 015 Exploring a Universal Gestural Language](#brief-015-exploring-a-universal-gestural-language)
  - [Table of Contents](#table-of-contents)
- [At a Glance](#at-a-glance)
  - [Gesture Recognition Program and GUI](#gesture-recognition-program-and-gui)
      - [Screenshots](#screenshots)
  - [How we used MQTT](#how-we-used-mqtt)
  - [What is the Home Assistant Operating System?](#what-is-the-home-assistant-operating-system)
  - [Configuring Gestures](#configuring-gestures)
- [Running the Project](#running-the-project)
  - [1. Running Gesture Recognition Program and GUI](#1-running-gesture-recognition-program-and-gui)
    - [Prerequisites](#prerequisites)
    - [Using python virtual environment](#using-python-virtual-environment)
    - [Setup](#setup)
    - [Using Docker](#using-docker)
  - [2. Running Home Assistant and integrating smart devices](#2-running-home-assistant-and-integrating-smart-devices)
    - [Prerequisites](#prerequisites-1)
    - [Raspberry Pi Setup](#raspberry-pi-setup)
    - [Configure the Raspberry Pi](#configure-the-raspberry-pi)
    - [Start the Raspberry Pi](#start-the-raspberry-pi)
    - [Onboarding Home Assistant](#onboarding-home-assistant)
    - [Adding the MQTT Broker Add-on](#adding-the-mqtt-broker-add-on)
    - [Configure the MQTT Integration](#configure-the-mqtt-integration)
    - [Adding the TP-Link Tapo Mini Smart Plug](#adding-the-tp-link-tapo-mini-smart-plug)
    - [Configuring Windows 10/11 using Push2Run](#configuring-windows-1011-using-push2run)
  - [3. Running the Gesture Configuration Web Interface](#3-running-the-gesture-configuration-web-interface)
    - [Running the config server](#running-the-config-server)

# At a Glance

## Gesture Recognition Program and GUI

We have developed a graphical user interface that provides feedback on the gestures that are being performed. This program uses the webcam to capture gestures and then uses a machine learning model to classify the gesture. Upon recognising a "gesture sentence" so they have provided a room, device and controlling action, we then publish this action as a message to the MQTT broker on the relevant topic. The UI was built using the [CustomTkinter](https://pypi.org/project/CustomTkinter/) library in python which is a customised version of the Tkinter library (a standard python library for creating GUIs) that provides additional functionality such as the ability to display images and videos. The messages are published to the MQTT broker using the [Paho-MQTT](https://pypi.org/project/paho-mqtt/) library in python.

#### Screenshots
The bottom bar shows the progress of recognising a sentence. The right bar shows logs of commands being sent out to smart devices.
|Scenario|Screenshot|
|-|-|
|Recognised Room and Device|![Screenshot 2023-10-19 at 4 50 46 am](https://github.com/SYBA-Squad/.github/assets/110031011/aa4869f0-8a7c-4c59-871b-104a688f7b68)|
|Recognised Gesture Sentence (Room, Device, Control Action)|![Screenshot 2023-10-19 at 4 51 46 am](https://github.com/SYBA-Squad/.github/assets/110031011/9d42c4e1-5064-4793-b0fd-a9bbafda604d)|



The [gesture recognition library](https://github.com/SYBA-Squad/multiframe-tokenizer) was developed to provide several utilities, in addition to the main user facing interface. The available functionalities are:
- Utilities for splitting training data into csv samples (handy for training the model)
- Model architecture definitions and training script
- Gesture language definition and configuration utilities (e.g. all the gestures that can be recognised)
- Gesture recognition demo visualizer (for testing the model)
- Gesture recognition interface for foreign program use (API for using the model in other programs such as the GUI)
- GUI interface to provide user feedback in constructing commands using CustomTkinter. (deliverable product)
- MQTT interface to publish commands to a broker using paho-mqtt. (API for publishing commands to the broker so that smart devices can be controlled)

All scripts depend on python (3.9 to 3.11 are verified) with the following libraries:

**Machine Learning Gesture Recognition**
- [matplotlib](https://pypi.org/project/matplotlib/)
- [torch](https://pypi.org/project/torch/)
- [torch_geometric](https://pypi.org/project/torch-geometric/)
- [torchmetrics](https://pypi.org/project/torchmetrics/)
- [transitions](https://pypi.org/project/transitions/) (for state machine)
- [opencv-python](https://pypi.org/project/opencv-python/)
- [numpy](https://pypi.org/project/numpy/)
- [pandas](https://pypi.org/project/pandas/)
- [mediapipe](https://pypi.org/project/mediapipe/)

**Machine Learning Model Data Collection Command Line Tool:**
- [colorama](https://pypi.org/project/colorama/)

**Packaging and Installation:**
- [pyinstaller](https://pypi.org/project/pyinstaller/)

**GUI:**
- [customtkinter](https://pypi.org/project/CustomTkinter/)

**MQTT:**
- [paho-mqtt](https://pypi.org/project/paho-mqtt/)

## How we used MQTT

MQTT is a lightweight, publish-subscribe based messaging protocol that is used to send messages between devices. This means that devices can subscribe to a topic and receive messages that are published to that topic. For example, upon recognising a gesture, we can publish a message to the topic for the room and device "room/device" and home assistant will receive this message and perform an action such as turning on the smart plug depending on the payload of the message.

## What is the Home Assistant Operating System?

Home Assistant provides a platform for home control and home automation. It is an embedded system that provides an experience like other consumer off-the-shelf products for device onboarding, integration and configuration through an easy to use interface.

In our project, we made use of official and non-official community add-ons to extend the functionality around Home Assistant. For example, we configured the home assistant to host an MQTT broker and installed the Community Store so that we could install custom integrations such as the Tapo Controller for controlling the TP-Link Tapo Mini Smart Plug.

Integrations are pieces of software that allow Home Assistant to connect to other software and platforms. Basically, they are the glue between Home Assistant and the devices and services you want to connect to on your local network such as smart devices. Home Assistant provides a large number of integrations by default, but you can also install custom integrations. For example, our Tapo Mini Smart Plug was not supported by Home Assistant by default, so we installed the Tapo Controller integration to control the TP-Link Tapo Mini Smart Plug.

Without Home Assistant, we would have to manually create this bridge between the MQTT broker and every smart device we wanted to control. Home Assistant provides a platform for us to easily configure and control our smart devices through a single interface whilst providing a large number of official integrations for us to use plus even more custom integrations provided by the community.

We also made use of Home Assistant automations to create triggers that would perform an action when a specific event occurred. For example, we created an automation to turn on and off the smart plug when specific MQTT messages were received. This allowed us to control the smart plug through by sending messages from our gesture recognition program.

## Configuring Gestures

We have also developed a web interface that allows the user to configure the gestures that they wish to use to control their smart devices. We provide a preset set of commands the user can make so all the rooms, devices and controlling actions. We allow the user to select what hand gesture they wish to use to represent each command.

These are built on top of a config.json file (located in the python subdirectory of the gesture recognition repository). The configuration files follow a specification that allows for powerful customisation of gestures. A configuration file has the following structure:

```json
{
    "commands": [
        {
            /**
            *  command: String representing the command name sent to mqtt
            */
            "command": "CommandName",
            "hands": {
                "type": "all", /** any of [all, any] */
                "list": [
                    /** list my contain any amount of the following two structures: */
                    {
                        "type": "all" /** a simple nesting of the above structure */
                        ...
                    },
                    {
                        "handedness": "any", /** any of [left, right, any] */
                        "gesture": "gestureName", /** any gesture name provided by the model */
                        "local": [], /** list containing any of [cycle_x, cycle_y, up, down, left, right, circle, stationary_x, stationary_y] */
                        "relative": [], /** list containing any of [towards_x, towards_y, away_x, away_y, stationary_x, stationary_y] */
                        "global": [] /** list containing any of [head, chest, waist, left, middle, right] */
                    }
                    
                ]
            }
        }
    ]
} 
```

The local parameter of a hand defines local motion, that is, if the specified hand is performing the described motion. The relative parameter defines motion relative to the other hand, and global describes global position information of the hand.

The "any" and "all" lists can be used to compose single hands into requirements for both hands, or multiple optional configurations. An "any" list requires any of its elements to be satisfied to match the command. An "all" list requires all of its elements to match.

# Running the Project

The project consists of three main components:
1.  The gesture recognition program and GUI
2.  Home Assistant and integrating smart devices
3.  The gesture configuration web interface


## 1. Running Gesture Recognition Program and GUI

The program can either be run using a python virutal environment or containerised using docker.

### Prerequisites

**Hardware**
- Webcam

**Software**
- Python 3.9-11 or Docker

### Using python virtual environment

### Setup
1. Navigate into the directory of this project
   - `cd gesture-recognition`
2. Create a virtual environment
   - `python -m venv venv`
3. Activate the virtual environment
   - Windows: `venv\Scripts\activate`
   - Linux and macOS: `source venv/bin/activate`
4. Install the required packages
   - `pip install -r requirements.txt`
5. Run the program
   - `python python/gui.py`
6. The screen will be black until the wave command is recognised.  By default the wave command is a hand performing the five gesture whilst moving from left to right. The bottom bar shows the progress of recognising a sentence. The right bar shows logs of commands being sent out to smart devices.

- If tkinter is not installed, install it using your package manager
   - Linux: `sudo apt-get install python-tk`
   - macOS: `brew install python-tk`

### Using Docker

For convenience, a Dockerfile and docker-compose file are set up. These include all configuration necessary to support webcam and gpu passthrough. Simply running the docker configuration by running `./enter.sh` or by using docker compose as usual should construct a fully prepared workspace which will open the gesture recognition GUI on startup.
To run docker compose, run the following command in the root directory of the repository:

**Bash**
```bash
docker compose up --build -d && docker compose exec dev-container /bin/bash
```

**Powershell**
```powershell
docker compose up --build -d; docker compose exec dev-container /bin/bash
```

## 2. Running Home Assistant and integrating smart devices

### Prerequisites

-   The following setup will configure a network of devices that will be able to communicate with each other. This will require that all devices are connected to the same network.

**Hardware**

-   Raspberry Pi 3/4 with power supply (used for hosting the Home Assistant OS which in turn hosts the Home Assistant web interface and MQTT broker)
-   Micro SD Card 32 GB or bigger (for configuring the Raspberry Pi with the Home Assistant OS)
-   Laptop with SD Card reader (or adapter)
-   Ethernet Cable (for home assistant setup)
-   Smart devices of your choice. We have included instructions for integration with some advanced integrations such as the [TP-Link Tapo Mini Smart Plug](https://www.jbhifi.com.au/products/tp-link-tapo-mini-smart-plug) (smart phone required) and Windows 10/11 (windows computer required).

### Raspberry Pi Setup

This section has been adapted from the [Home Assistant documentation](https://www.home-assistant.io/installation/raspberrypi).

### Configure the Raspberry Pi

1. Download the [Raspberry Pi Imager](https://www.raspberrypi.org/software/) and install it on a computer with an SD Card reader (or adapter).
1. With the SD Card in the computer and the Raspberry Pi Imager open:
    1. Select CHOOSE OS
    1. Other specific-purpose OS > Home assistants and Home automation > Home Assistant > Home Assistant OS (choose version relevant to Raspberry Pi model)
    1. CHOOSE STORAGE and select the SD Card (note: this will wipe the SD Card)
    1. WRITE

### Start the Raspberry Pi

1. After the image has been written to the SD Card, eject the SD Card and insert it into the Raspberry Pi.
1. Plug in an Ethernet cable that is connected to the network you wish to use.
1. Plug in the power supply and wait for the Raspberry Pi to boot up.
1. Open a web browser on a computer connected to the same network as the Raspberry Pi and navigate to [`http://homeassistant.local:8123/`]() (if this does not work, try `http://<ip address of the Raspberry Pi>:8123/`).
1.

### Onboarding Home Assistant

1. It may take a few minutes for Home Assistant to start up.
1. Once connected to the web interface, follow the on-screen instructions to set up your Home Assistant account.
1. SELECT **Create my smart home**
1. Enter a name, username, and password. Select **Create account**. (all data is stored locally on the Raspberry Pi and connections can only be made from devices connected to the same network)
1. The entering your location step can be skipped as
   the location you choose will not impact the functionality of our project.
   More details on why this is collected can be found in the [Home Assistant documentation](https://www.home-assistant.io/installation/raspberrypi):
1. Select relevant privacy settings and **Next**.
1. Finish the onboarding process by selecting **Finish**.
1. Read more about the [Home Assistant core concepts](https://www.home-assistant.io/getting-started/concepts-terminology/).

### Adding the MQTT Broker Add-on

The home assistant can be configured to host an MQTT broker. This will allow the home assistant to communicate with other devices on the network using the MQTT protocol. This is required for the gesture recognition GUI to communicate with the home assistant and control the smart devices.

1. Navigate to Settings > Add-ons > Add-on Store
1. Install the [Mosquitto broker](https://www.home-assistant.io/addons/mosquitto/) (one of the official Home Assistant add-ons) and Start it.
    - If you are unable to see any add-ons, you may need to reboot the whole system. Navigate to Settings > System > Power Button (top right corner) > Advanced > Reboot system.

### Configure the MQTT Integration

1. Navigate to Settings > Devices & Services
1. Under Discovered Devices, select configure next to the MQTT integration > Submit
1. Note down the MQTT broker authentication details (username and password). These can be found by selecting the MQTT integration > Configure > Reconfigure > Show password
1. Edit the env variables in the gesture-recognition/mqtt/.env file to match the MQTT broker authentication details. The hostname and port should not need to be changed.

### Adding the TP-Link Tapo Mini Smart Plug

For this specific smart plug, we have to install the Home Assistant Community Store via the terminal.

Firstly configure the smart plug using the Tapo app on your smart phone.

1. Download the Tapo app on your smart phone and create an account.
1. Plug in the smart plug and follow the provided instructions to configure it.
1. Note down the ip address of the smart plug by navigating to Settings > Device Info > IP Address.

Once configured, you can add the smart plug to the home assistant.

1. Navigate to Settings > Add-ons > Add-on Store and search for a terminal add-on.
1. Once installed, start it and make sure to select show in sidebar
1. If the terminal has trouble starting, you may need to configure an ssh password for the add-on.
1. Now navigate to the sidebar and select the terminal add-on to open a terminal.
1. Enter the following command to install the Home Assistant Community Store:

    ```bash
    wget -O - https://get.hacs.xyz | bash -
    ```

1. Enter the following command to restart the home assistant:

    ```bash
    ha ha restart
    ```

1. Navigate to Settings > Devices & Services > Integrations > Add Integration > Select HACS
1. When prompted, connect your github to HACS so that you can install custom integrations.
1. Select HACS in the sidebar and search for the [Tapo Controller](https://github.com/petretiandrea/home-assistant-tapo-p100) and download it. (You may have to clear the filter of the search bar to see the integration)
1. A restart of the home assistant may be required. Navigate to Settings and select restart if prompted.
1. Navigate to Settings > Devices & Services > Integrations > Add Integration > Select TP-Link Tapo
1. You will now be prompted to enter the username and password of your Tapo account. (Note: this is the same as the username and password of the Tapo app) and the ip address of the smart plug that you noted down earlier.
1. Finally, add an automation to the home assistant to control the smart plug. Select Add Automation > When something is triggered... Turn on the smart plug > Add Trigger > MQTT > Enter "room-number/device-number" (lowercase) for the topic and "on" for the payload > Save

-   The valid rooms, devices and controlling actions can be found in gesture-recognition/python/datatypes.py

### Configuring Windows 10/11 using Push2Run

1. Download and install [Push2Run](https://www.push2run.com/) on your Windows computer.
1. Import the Push2Run configuration file found in gesture-recognition/mqtt/config.p2r
1. Turn on the actions you wish to use to control your Windows computer.
1. Configure the MQTT connection. Select File > Options > MQTT and enter the hostname of the home assistant `homeassistant.local`, the port `1883` and the username and password of the MQTT broker (these can be found in the MQTT integration configuration).

## 3. Running the Gesture Configuration Web Interface

### Running the config server

1. Naviagte to the server directory
    ```
    cd config-interface/server
    ```
2. Create a python virtual environment
    ```
    python -m venv venv
    ```
3. Activate the virtual environment
    - Windows: `venv\Scripts\activate`
    - Linux and macOS: `source venv/bin/activate`
4. Install flask and the required packages
    ```
    pip install -r requirements.txt
    ```
5. Edit the SAVE-FILE value in the .env file to match the path to the config.json file used in the gesture-recognition code
    - If both the gesture-recognition and config-server directories are contained within the same directory, the default value (../../gesture-recognition/python/config.json) should work
6. Start the server
    ```
    flask --app main run
    ```
7. Download NodeJs from https://nodejs.org/en/download and complete the setup
8. In a new terminal navigate to the frontend directory
    ```
    cd config-interface/frontend
    ```
9. Install the dependancies
    ```
    npm i
    ```
10. Run the react application
  
    For production build:
    ```
    npm install -g serve
    serve -s build
    ```
    or for development build
    ```
    npm start
    ```
   
11. If the page does not load you may need to edit the GestureConfig.js file in `frontend/src/pages` and change the SERVER_URL variable to match the address of the flask server and then restart the node server with `npm start`
12. Open the application in the browser (typically localhost:3000)


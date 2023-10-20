---
author: SYBA Squad
geometry: "left=2cm, right=2cm, top=2cm, bottom=2cm"
---

# SYBA Squad | DECO3801

### Brief: 015 Exploring a Universal Gestural Language

## Prerequisites

-   The following setup will configure a network of devices that will be able to communicate with each other. This will require that all devices are connected to the same network.

### Hardware

-   Raspberry Pi 3/4 with power supply (used for hosting the Home Assistant OS which in turn hosts the Home Assistant web interface and MQTT broker)
-   Micro SD Card 32 GB or bigger (for configuring the Raspberry Pi with the Home Assistant OS)
-   Laptop with SD Card reader (or adapter)
-   Webcam (used for capturing gestures)
-   Ethernet Cable (for home assistant setup)
-   Smart devices of your choice. We have included instructions for integration with some advanced integrations such as the [TP-Link Tapo Mini Smart Plug](https://www.jbhifi.com.au/products/tp-link-tapo-mini-smart-plug) (smart phone required) and Windows 10/11 (windows computer required).

## Raspberry Pi Setup

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
1. Note down the MQTT broker authentication details (username and password) as these will be required for the gesture recognition GUI setup. These can be found by selecting the MQTT integration > Configure > Reconfigure > Show password

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

-   The valid rooms, devices and controlling actions can be found in multiframe-tokenizer/python/datatypes.py

## How we used MQTT

MQTT is a lightweight messaging protocol that is used to send messages between devices similar to HTTP, however, it is more suited to IoT devices as it is lightweight and uses less bandwidth. It is a publish-subscribe based messaging protocol that is used to send messages between devices. This means that devices can subscribe to a topic and receive messages that are published to that topic.
For example, upon recognising a gesture, we can publish a message to the topic for the room and device "room/device" and the smart device can subscribe to that topic to receive the message. This allows us to control smart devices through the MQTT protocol.

## What is the Home Assistant Operating System?

Home Assistant provides a platform for home control and home automation. It is an embedded system that provides an experience like other consumer off-the-shelf products for device onboarding, integration and configuration through an easy to use interface.

In our project, we made use of official and non-official community add-ons to extend the functionality around Home Assistant. For example, we configured the home assistant to host an MQTT broker and installed the Community Store so that we could install custom integrations such as the Tapo Controller for controlling the TP-Link Tapo Mini Smart Plug.

Integrations are pieces of software that allow Home Assistant to connect to other software and platforms. Basically, they are the glue between Home Assistant and the devices and services you want to connect to on your local network such as smart devices. Home Assistant provides a large number of integrations by default, but you can also install custom integrations. For example, our Tapo Mini Smart Plug was not supported by Home Assistant by default, so we installed the Tapo Controller integration to control the TP-Link Tapo Mini Smart Plug.

Without Home Assistant, we would have to manually create this bridge between the MQTT broker and every smart device we wanted to control. Home Assistant provides a platform for us to easily configure and control our smart devices through a single interface whilst providing a large number of official integrations for us to use plus even more custom integrations provided by the community.

We also made use of Home Assistant automations to create triggers that would perform an action when a specific event occurred. For example, we created an automation to turn on and off the smart plug when specific MQTT messages were received. This allowed us to control the smart plug through by sending messages from our gesture recognition program.

## Gesture Recognition Program

The [gesture recognition library](https://github.com/SYBA-Squad/multiframe-tokenizer) was developed to provide several utilities, in addition to the main user facing interface. The available functionalities are:
- Utilities for splitting training data into csv samples
- Model architecture definitions and training script
- Gesture language definition and configuration utilities.
- Gesture recognition demo visualizer.
- Gesture recognition interface for foreign program use.
- GUI interface to provide user feedback in constructing commands.

All scripts depend on python (3.9 to 3.11 are verified) with the following libraries:
- matplotlib
- torch
- torch_geometric
- torchmetrics
- transitions
- customtkinter
- opencv-python
- numpy
- pandas
- mediapipe
- colorama
- pandas
- pyinstaller

For convenience, a Dockerfile and docker-compose file are set up. These include all configuration necessary to support webcam and gpu passthrough. Simply running the docker configuration with enter.sh or using docker compose as usual should construct a fully prepared workspace.

More complete documentation can be found in the README of this library.

## Gesture Recognition GUI

We have developed a graphical user interface that provides feedback on the gestures that are being performed. This program uses the webcam to capture gestures and then uses a machine learning model to classify the gesture. Upon recognising a "gesture sentence" so they have provided a room, device and controlling action, we then publish this action as a message to the MQTT broker on the relevant topic. The UI was built using the [CustomTkinter](https://pypi.org/project/CustomTkinter/) library in python which is a customised version of the Tkinter library that provides additional functionality such as the ability to display images and videos. Since our recognition program is written in python, it was made sense to integrate the gesture recognition program with a python UI. The messages are published to the MQTT broker using the [Paho-MQTT](https://pypi.org/project/paho-mqtt/) library in python.

More complete documentation can be found in the README of the gesture recognition library.

## Configuring Gestures

We have also developed a web interface that allows the user to configure the gestures that they wish to use to control their smart devices. We provide a preset set of commands the user can make so all the rooms, devices and controlling actions. We allow the user to select what hand gesture they wish to use to represent each command.

These are built on top of a config.json file (located in the python subdirectory of the multiframe-tokenizor repository). The configuration files follow a specification that allows for powerful customisation of gestures. A configuration file has the following structure:

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

More complete documentation can be found in the README of the gesture recognition library.

## Gesture Recognition GUI Setup

### Dependencies

-   [Python 3.9](https://www.python.org/downloads/)
-   Python libraries:
    -   [OpenCV](https://pypi.org/project/opencv-python/)
    -   [Numpy](https://pypi.org/project/numpy/)
    -   [Pandas](https://pypi.org/project/pandas/)
    -   [Mediapipe](https://pypi.org/project/mediapipe/)
    -   [Pytorch](https://pypi.org/project/torch/)
    -   [Matplotlib](https://pypi.org/project/matplotlib/)
    -   [Tensorflow](https://pypi.org/project/tensorflow/)
    -   [CustomTkinter](https://pypi.org/project/CustomTkinter/)
    -   [MQTT](https://pypi.org/project/paho-mqtt/)
-   [Docker](https://www.docker.com/products/docker-desktop) virtual environment for installing the python libraries that we have provided a Dockerfile for. Other options such as [Anaconda](https://www.anaconda.com/products/individual), [Virtualenv](https://virtualenv.pypa.io/en/latest/) or [Pipenv](https://pypi.org/project/pipenv/) are also viable to be used. Otherwise, you can install the libraries globally on your machine using [`pip`](https://pip.pypa.io/en/stable/) if you wish.
-

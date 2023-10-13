# SYBA Squad | DECO3801

### Brief: 015 Exploring a Universal Gestural Language

## Prerequisites

-   The following setup will configure a network of devices that will be able to communicate with each other. This will require that all devices are connected to the same network.

### Hardware

-   Raspberry Pi 3/4 with power supply (used for hosting the Home Assistant OS which in turn hosts the Home Assistant web interface and MQTT broker)
-   Micro SD Card (for configuring the Raspberry Pi with the Home Assistant OS)
-   Laptop with SD Card reader (or adapter)
-   Webcam (used for capturing gestures)
-   Ethernet Cable (for home assistant setup)
-   Smart devices of your choice. We have included instructions for integration with some advanced integrations such as the [TP-Link Tapo Mini Smart Plug](https://www.jbhifi.com.au/products/tp-link-tapo-mini-smart-plug) (smart phone required) and Windows 10/11 (windows computer required).

## Raspberry Pi Setup

This section has been adapted from the [Home Assistant documentation](https://www.home-assistant.io/installation/raspberrypi).

### Configure the Raspberry Pi

1. Download the [Raspberry Pi Imager](https://www.raspberrypi.org/software/) and install it on a computer with an SD Card reader (or adapter).
1. With the SD Card in the computer and the Raspberry Pi Imager open:
    1. Select **CHOOSE OS**
    1. **Other specific-purpose OS** > **Home assistants and Home automation** > **Home Assistant** > **Home Assistant OS** (choose version relevant to Raspberry Pi model)
    1. **CHOOSE STORAGE** and select the SD Card (note: this will wipe the SD Card)
    1. **WRITE**

### Start the Raspberry Pi

1. After the image has been written to the SD Card, eject the SD Card and insert it into the Raspberry Pi.
1. Plug in an Ethernet cable that is connected to the network you wish to use.
1. Plug in the power supply and wait for the Raspberry Pi to boot up.
1. Open a web browser on a computer connected to the same network as the Raspberry Pi and navigate to `http://homeassistant.local:8123/` (if this does not work, try `http://<ip address of the Raspberry Pi>:8123/`).
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

### Adding the MQTT Broker Integration

The home assistant can be configured to host an MQTT broker. This will allow the home assistant to communicate with other devices on the network using the MQTT protocol. This is required for the gesture recognition GUI to communicate with the home assistant and control the smart devices.
To see how to add other integrations, see the [Home Assistant documentation](https://www.home-assistant.io/getting-started/integration/) and to see the list of all available integrations, see the [Home Assistant integration list](https://www.home-assistant.io/integrations/#all).

This section has been adapted from the [Home Assistant MQTT documentation](https://www.home-assistant.io/integrations/mqtt/)

**Automatic Configuration**

1. To automatically configure the MQTT broker click on this link: https://my.home-assistant.io/redirect/config_flow_start?domain=mqtt

**Manual Configuration**

1. Navigate to Home Assistant web interface located at [`http://homeassistant.local:8123/`](http://homeassistant.local:8123/)
1. Go to [Settings > Devices & Services](https://my.home-assistant.io/redirect/integrations)
1. In the bottom right corner, select the [Add Integration](https://my.home-assistant.io/redirect/config_flow_start?domain=mqtt) button
1. From the list, select **MQTT**

### Broker configuration

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

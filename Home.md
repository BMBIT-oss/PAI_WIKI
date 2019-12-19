# Paradox Alarm Interface (PAI)
* [Installation](./Installation)
* [Connection methods](./Connection-methods)
* [Configuration](./Configuration)


## Structure

* __Panels__ : Represents a generic panel that is realized by classes adequate to the specific model (MG/SP/EVO)

* __Paradox__: Object that interfaces with the panel and keeps some internal state. Accepts commands to control partitions, zones and outputs. Exposes states, changes and events

* __Interfaces__: Expose interfaces to the outside world. Currently, MQTT (with Homekit support), Signal, Pushbullet and IP (IP150 emulation) are supported, but others are planned and almost any other is supported.

* __Connections__: Handle communications with the panel, at a lower level. Currently, both Serial and IP connections are supported.


## Interfaces
Interfaces provide the means to interact with PAI and the alarm panel.

### MQTT
This is main interface for communication with other systems.
Details: [MQTT Interface](./MQTT-Interface)

### Homebridge and Homekit through MQTT

This interface also provides an integration with Homebridge, when using the [homebridge-mqttthing](https://github.com/arachnetech/homebridge-mqttthing) plugin. See [Homebridge](../Homebridge)

### HomeAssistant
HomeAssistant with MQTT autodiscovery supported. See [HomeAssistant](../HomeAssistant)

### OpenHAB via MQTT
See [OpenHAB](../OpenHAB)

### MQTT Dash

This interface will publish a simple dashboard to ```metrics/exchange/pai``` in a format that is readily parsed by [MQTT Dash](https://play.google.com/store/apps/details?id=net.routix.mqttdash). This will happen after the interface knows at least two Partitions. If you have only one partition, nothing will be published. If you have more than 2, only the first two will be used.

To have this dashboard, install MQTT Dash, create a connection, select the connection and click the two arrows on the top right corner. Then select the topic ```metrics/exchange/pai``` and subscribe for changes. The screen should update as shown:

![mqtt_dash](https://user-images.githubusercontent.com/497717/52603920-d4984d80-2e60-11e9-9772-578b10576b3c.jpg)


### Signal Interface

The Signal Interface allows accessing major state changes and arming/disarming partitions through the [WhisperSystems](https://www.whispersystems.org/) Signal service. You will require the corresponding mobile application in your smartphone. As this interface will produce notifications to other devices, and are destined to users, only a subset of the events are sent.

To send a command to the alarm use the format: ```type object command```. One example would be: ```partition outside arm```. If the ```object``` is all, the command will be sent to all objects of the same type (e.g., all partitions, all zones).

Interface with Signal is made through [Signal-CLI](https://github.com/AsamK/signal-cli) running in system dbus mode. Follow the instruction to enable ```Signal-CLI``` and it should work automatically. You will require a valid phone number to be allocated to this service.

The configuration setting ```SIGNAL_CONTACTS``` should contain a list with the contacts used for signal notifications. If the list is empty, the Signal module is disabled.

#### Pushbullet Interface
See [Pushbullet](./Pushbullet)

#### Pushover Interface
See [Pushover](./Pushover)

#### GSM SIMXXX Interface

The GSM Interface will notify users of major events through SMS and will accept commands through the same method.

In order to use this interface, please et the relevant configuration settings. The ```GSM_CONTACTS```setting should contain a list of contacts used for notifications and commands. Only these contacts will be allowed to control the alarm.

To send a command to the alarm use the format: ```type object command```. One example would be: ```partition outside arm```. If the ```object``` is all, the command will be sent to all objects of the same type (e.g., all partitions, all zones).

#### IP Interface

The IP Interface mimics an IP150 module, allowing the use of standard alarm management tools to interact with the panel. It supports plain sessions or encrypted session as found in later versions of the IP150 module.

__When a client is connected to this interface, PAI will operate as a proxy. Most features and interfaces will be disabled until the client disconnects__

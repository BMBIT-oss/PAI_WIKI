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


### MQTT Interface

The MQTT Interface allows accessing all relevant states and setting some. The list of states will increase as the knowledge of the alarm internal memory and its states is improved.

All interactions are made through a ```MQTT_BASE_TOPIC```, which defaults to ```paradox```. States are exposed by name with a boolean payload (True or False) and are mainly update by the alarm status messages, updated every ```KEEP_ALIVE_INTERFACE``` seconds.

#### States
* ```paradox/states/partitions/name/property```: Exposes Partition properties where ```name``` identifies the Partition name (e.g. Interior) and ```property``` identifies the property (e.g. ```arm```).
* ```paradox/states/zones/name/property```: Exposes Partition properties where ```name``` identifies the Zone name (e.g. Kitchen) and ```property``` identifies the property (e.g. ```open```).
* ```paradox/states/outputs/name/property```: Exposes Partition properties where ```name``` identifies the PGM name (e.g. Gate) and ```property``` identifies the property.

The following states are supported:

* __buses__ : ```supervision_trouble, tamper, trouble```
* __keypads__: ```ac_loss_trouble, battery_failure_trouble, signal_strength, supervision_failure_trouble, trouble```
* __outputs__: ```signal_strength, supervision_trouble, tamper, trouble```
* __partitions__: ```alarm, alarms_in_memory, arm, arm_with_remote, audible_alarm, auto_arming_engaged, bell_activated, bell_delay_finished, entry_delay,entry_delay_finished, exit_delay, exit_delay_finished, force_arm, in_remote_delay, intellizone_delay, intellizone_delay_finished, paramedic_alarm, pulse_fire_alarm, ready_status, recent_closing_delay, silent_alarm, sleep_arm, stay_arm, stayd_mode_active, strobe_alarm, transmission_delay_finished, wait_window, zone_bypassed```
* __repeaters__: ```ac_loss_trouble, battery_failure_trouble, signal_strength, supervision_trouble, trouble```
* __system__: ```battery, dc, rf_noise_floor, vdc```
* __zones__: ```alarm, bypassed, entry_delay, exit_delay, fire, fire_delay, intellizone_delay, in_tx_delay, no_delay, open, rf_low_battery_trouble, rf_supervision_trouble, shutdown, signal_strength, tamper, trouble, was_bypassed, was_in_alarm```

#### Events
* ```paradox/states/raw```: Exposes raw event information, composed by a minor and major codes, as well as descriptive text. The payload is a JSON object with the following structure:
```
    {
        'major': (major_code, 'major_text'),
        'type': 'event_type',
        'minor': (minor_code, 'minor_text')
    }

```
* ```major_code```  and ```major_text``` represent the major code and corresponding text description as provided by the alarm. This will mostly identify the event.
* ```minor_code```  and ```minor_text``` represent the minor code and corresponding text description as provided by the alarm. This will mostly identify a detail of the event, such as the zone number/name, the user name, partition name, and so on.
* ```type``` identifies the event category, and can take the following values: ```Partition```, ```Bell```, ```NonReportable```, ```User```, ```Remote```, ```Special```, ```Trouble```, ```Software```, ```Output```, ```Wireless```, ```Bus Module```, ```Zone```, ```System```.


#### Control

The MQTT Interface allows setting some properties for individual objects by specifying the correct name. In alternative, the ```all``` keyword can be used to apply the same setting to all objects. This is useful to activate all PGMs or to Arm/Disarm all partitions.

* ```paradox/control/partitions/name``` allow setting some partition properties where ```name``` identifies the partition. If the ```name``` is ```all```, all partitions will be updated. The payload can have the values ```arm```, ```arm_stay```, ```arm_sleep```, ```arm_stay_stayd```,  ```arm_sleep_stay``` or ```disarm``` and ```disarm_all```.
* ```paradox/control/zones/name``` allow setting some zone properties where ```name``` identifies the zone. If the ```name``` is ```all```, all zones will be updated. The payload can have the values ```bypass``` and ```clear_bypass```.
* ```paradox/control/outputs/name``` allow setting some PGM properties where ```name``` identifies the PGM. If the ```name``` is ```all```, all PGMs will be updated. The payload can have the values ```pulse```, ```on```, ```on_override```, ```off``` or ```off_override```.


#### Code Toggle

Sometimes it is useful to toggle the ARM state through a remote device, such as a NFC reader. Therefore, Partitions arm state can be toggled by issuing a special command with the format ```code_toggle-code_number``` (e.g., code_toggle-123456755). The ```code_toggle-``` keyword is constant, while the ```code_number``` is provided by the card (e.g., Card ID). If the code is present in the ```MQTT_TOGGLE_CODES```, the partition state will be toggled.

The ```MQTT_TOGGLE_CODES``` should be composed by a dictionary where the key contains the code, and the value contains a description. This allows for easily sending notifications in the form: "Alarm unlocked by USERNAME".

This was throughly tested with [ESPEasy](https://www.letscontrolit.com/) running on a ESP8266 board (e.g. NodeMCU or Wemos D1 Mini) connected to a PN532 NFC reader.
Besides the typical ESPEasy configuration, the only "code" required is a simple rule. The following example will publish any ID to the correct topic and will flash a LED for 2 seconds.

```
on reader#tag do
   Publish paradox/control/partitions/all,code_toggle-[reader#tag]
   Pulse,2,0,2000
endon
```

```reader#tag``` identifies the ESPEasy PN532 device name (```reader```) and the property holding the RFID ID (```tag```).

#### Homebridge and Homekit through MQTT

This interface also provides an integration with Homebridge, when using the [homebridge-mqttthing](https://github.com/arachnetech/homebridge-mqttthing) plugin. See [Homebridge](../Homebridge)

#### HomeAssistant
HomeAssistant with MQTT autodiscovery supported. See [HomeAssistant](../HomeAssistant)

#### OpenHAB
OpenHAB via MQTT. See [OpenHAB](../OpenHAB)

#### MQTT Dash

This interface will publish a simple dashboard to ```metrics/exchange/pai``` in a format that is readily parsed by [MQTT Dash](https://play.google.com/store/apps/details?id=net.routix.mqttdash). This will happen after the interface knows at least two Partitions. If you have only one partition, nothing will be published. If you have more than 2, only the first two will be used.

To have this dashboard, install MQTT Dash, create a connection, select the connection and click the two arrows on the top right corner. Then select the topic ```metrics/exchange/pai``` and subscribe for changes. The screen should update as shown:

![mqtt_dash](https://user-images.githubusercontent.com/497717/52603920-d4984d80-2e60-11e9-9772-578b10576b3c.jpg)


#### Signal Interface

The Signal Interface allows accessing major state changes and arming/disarming partitions through the [WhisperSystems](https://www.whispersystems.org/) Signal service. You will require the corresponding mobile application in your smartphone. As this interface will produce notifications to other devices, and are destined to users, only a subset of the events are sent.

To send a command to the alarm use the format: ```type object command```. One example would be: ```partition outside arm```. If the ```object``` is all, the command will be sent to all objects of the same type (e.g., all partitions, all zones).

Interface with Signal is made through [Signal-CLI](https://github.com/AsamK/signal-cli) running in system dbus mode. Follow the instruction to enable ```Signal-CLI``` and it should work automatically. You will require a valid phone number to be allocated to this service.

The configuration setting ```SIGNAL_CONTACTS``` should contain a list with the contacts used for signal notifications. If the list is empty, the Signal module is disabled.


#### Pushbullet Interface

The Pushbullet Interface allows accessing major state changes and arming/disarming partitions. As this interface will produce notifications to other devices, and are destined to users, only a subset of the events are sent.

In order to use this interface, please set the relevant configuration settings. The ```PUSHBULLET_CONTACTS``` setting should contain a list of contacts used for pushbullet notifications.

To send a command to the alarm use the format: ```type object command```. One example would be: ```partition outside arm```. If the ```object``` is all, the command will be sent to all objects of the same type (e.g., all partitions, all zones).

#### GSM SIMXXX Interface

The GSM Interface will notify users of major events through SMS and will accept commands through the same method.

In order to use this interface, please et the relevant configuration settings. The ```GSM_CONTACTS```setting should contain a list of contacts used for notifications and commands. Only these contacts will be allowed to control the alarm.

To send a command to the alarm use the format: ```type object command```. One example would be: ```partition outside arm```. If the ```object``` is all, the command will be sent to all objects of the same type (e.g., all partitions, all zones).

#### IP Interface

The IP Interface mimics an IP150 module, allowing the use of standard alarm management tools to interact with the panel. It supports plain sessions or encrypted session as found in later versions of the IP150 module.

__When a client is connected to this interface, PAI will operate as a proxy. Most features and interfaces will be disabled until the client disconnects__
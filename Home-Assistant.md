We support Home Assistant with MQTT autodiscovery.

## Requirements
- Supported panel (SP/MG/EVO)
- MQTT Broker (Hass.io has an addon)

## Installation

In your Home Assistant installation add 3rd party add-on repository in the Add-on Store: `https://github.com/ParadoxAlarmInterface/hassio-repository`

Install `Paradox Alarm Interface` version `1.3.1+` from there. There is also a development version available in this repository.

Add new `pai` user in Mosquitto for PAI to use. Check your Mosquitto addon manual.

Configure the add-on in the configuration tab.

```
LOGGING_FILE: null
LOGGING_LEVEL_CONSOLE: 20
LOGGING_LEVEL_FILE: 40
LOGGING_DUMP_PACKETS: false
LOGGING_DUMP_MESSAGES: false
LOGGING_DUMP_STATUS: false
LOGGING_DUMP_EVENTS: false
CONNECTION_TYPE: IP #set this to "IP" if using IP
SERIAL_PORT: /dev/ttyUSB0
SERIAL_BAUD: 9600
IP_CONNECTION_HOST: 192.168.X.X #set this to your IP150 IP address if using IP
IP_CONNECTION_PORT: 10000
IP_CONNECTION_PASSWORD: paradox
IP_CONNECTION_SITEID: null
IP_CONNECTION_EMAIL: null
IP_CONNECTION_PANEL_SERIAL: null
IP_CONNECTION_BARE: false
LIMITS: {}
SYNC_TIME: true
PASSWORD: '0000'
MQTT_ENABLE: true
MQTT_HOST: core-mosquitto
MQTT_PORT: 1883
MQTT_USERNAME: pai
MQTT_PASSWORD: #password 
MQTT_RETAIN: true
MQTT_BIND_ADDRESS: 127.0.0.1
MQTT_BIND_PORT: 0
MQTT_HOMEASSISTANT_AUTODISCOVERY_ENABLE: true
```

All configuration parameters from [pai.conf.example](https://github.com/ParadoxAlarmInterface/pai/blob/master/config/pai.conf.example) should be supported even if they do not exist in the default configuration. Home Assistant web UI wants configuration in yaml. But our `pai.conf.example` is a python file. There may be differences in format. Make sure you understand what is YAML.

Make sure you specify `MQTT_HOST` properly. If you use Home Assistant own Mosquitto addon `core-mosquitto` should work. `127.0.0.1` or `localhost` will not work.

See [Configuration](./Configuration)

`LOGGING_LEVEL_*` parameters accept numeric values:
```
CRITICAL = 50
ERROR = 40
WARNING = 30
INFO = 20
DEBUG = 10
NOTSET = 0
```

### Serial connection

See [serial connection method](./Connection-methods#serial-connection). You need to specify right port and baud rate. Inspect your hass.io for available tty ports. We recommend using a cheap USB to Serial module for 5v instead of raspberry pi pins directly with a level shifter.
```
SERIAL_PORT = '/dev/ttyUSB0'
SERIAL_BAUD: 9600
```

#### Permission denied: '/dev/ttyUSB0'

Credits to @juanbrunette

##### Login to Host OS
1) Connect a screen and keyboard to your Raspberry PI running Home Assistant Core.
2) Press Enter. You will see homeassistant login
3) Login as `root`. You will see `ha >`
4) Type in: `login`. You will see `#` (this means you are now on the Home Assistant Core Terminal as root)

##### Temporary solution
Permissions to the serial device will restore if you replug usb or restart the system:

5) `chmod 0666 /dev/ttyUSB0`
6) Restart the addon and verify PAI can connect to the serial device.

##### Permanent solution
7) Find your USB2Serial device's `Vendor=xxxx ProdID=xxxx` in `cat /sys/kernel/debug/usb/devices | grep -E "^([PS]:|$)"` output.

Note: if you are unsure which device. Unplug it. Run command. Then plug it back in and run command again. A device will appear in the output.
Write down `Vendor` and `ProdID`. You will need them later. Let's assume our `Vendor` is `0403` and `ProdID` is `6001`.

8) Type in: `vi /etc/udev/rules.d/paradox.rules`
9) Insert the following into the file by pressing `i` on your keyboard:

Note: Replace `idVendor` and `idProduct` attribute values with the ones you wrote down before.

```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", SYMLINK+="paradox", MODE="0666"
```

10) Press Esc
11) Type in (note the : in front of wq): `:wq`
12) Type in: `cat /etc/udev/rules.d/paradox.rules`. You should see
```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", SYMLINK+="paradox", MODE="0666"
```
13) Reload udev rules: `udevadm control --reload-rules && udevadm trigger`
14) Reboot your HASSIO or unplug and plug back your usb to serial device and restart the addon.
15) The addon should now be able to access `/dev/ttyUSB0`

## Home Assistant configuration
Check our [Home Assistant Integration page](./Home-Assistant-integration#homeassistant-keypad)


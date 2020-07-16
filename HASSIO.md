We support Hass.IO with MQTT autodiscovery.

## Requirements
- Supported panel (SP/MG/EVO)
- MQTT Broker (Hass.io has an addon)

## Installation

In your Hass.IO installation add 3rd party repository: `https://github.com/ParadoxAlarmInterface/hassio-repository`

Install `Paradox Alarm Interface` version `1.3.1+` from there.

Add new `pai` user in mosquitto for PAI to use. Check your mosquitto addon manual.

Configure the addon config. All configuration parameters from [pai.conf.example](https://github.com/ParadoxAlarmInterface/pai/blob/master/config/pai.conf.example) should be supported even if they do not exist in the default configuration. Hass.io web UI wants configuration in yaml. But our `pai.conf.example` is a python file. There may be differences in format. Make sure you understand what is YAML.

Make sure you specify `MQTT_HOST` properly. If you use hass.io own mosquitto addon `core-mosquitto` should work. `127.0.0.1` or `localhost` will not work.

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
1) Connect a screen and keyboard to your Raspberry PI running HassOS.
2) Press Enter. You will see homeassistant login
3) Login as `root`. You will see `ha >`
4) Type in: `login`. You will see `#` (this means you are now on the HassOs Terminal as root)

##### Temporary solution
Permissions to the serial device will restore if you replug usb or restart the system:

5) `chmod 0666 /dev/ttyUSB0`
6) Restart the addon and verify PAI can connect to the serial device.

##### Permanent solution
7) Find your USB2Serial device's `Vendor=xxxx ProdID=xxxx` in `cat /sys/kernel/debug/usb/devices | grep Vendor -A 2` output.

Note: if you are unsure which device. Unplug it. Run command. Then plug it back in and run command again. A device will appear in the output.
Write down `Vendor` and `ProdID`. You will need them later.

8) Type in: `vi /etc/udev/rules.d/paradox.rules`
9) Insert the following into the file by pressing `i` on your keyboard:

Note: Replace `idVendor` and `idProduct` attribute values with the ones you wrote down before.

```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", SYMLINK+="paradox", MODE="0666"
```

10) Press Esc
11) Type in (note the : in front of wq): `:wq`
12) Type in: `cat paradox.rules`. You should see
```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", SYMLINK+="paradox", MODE="0666"
```
13) Reload udev rules: `udevadm control --reload-rules && udevadm trigger`
14) Reboot your HASSIO or unplug and plug back your usb to serial device and restart the addon.
15) The addon should now be able to access `/dev/ttyUSB0`

## Home Assistant configuration
Check our [Home Assistant Integration page](./HomeAssistant#homeassistant-keypad)


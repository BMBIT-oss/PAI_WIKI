We support Hass.IO with MQTT autodiscovery.

# Requirements
- Supported panel (SP/MG/EVO)
- MQTT Broker (Hass.io has an addon)

# Installation

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

## Serial connection

See [serial connection method](./Connection-methods#serial-connection). You need to specify right port and baud rate. Inspect your hass.io for available tty ports. We recommend using a cheap USB to Serial module for 5v instead of raspberry pi pins directly with a level shifter.
```
SERIAL_PORT = '/dev/ttyUSB0'
SERIAL_BAUD: 9600
```

If you get `permission denied` you need to use `chmod a+rw /dev/ttyUSB0`.

# Lovelace

If you want more arming states than default `arm_home` and `arm_away` take a look at the [manual](https://www.home-assistant.io/lovelace/alarm-panel/).
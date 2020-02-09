# Requirements
- Supported panel (SP/MG/EVO)
- MQTT Broker (Hass.io has an addon)

# Installation
We support Hass.IO with MQTT autodiscovery.

In your Hass.IO installation add 3rd party repository: `https://github.com/ParadoxAlarmInterface/hassio-repository`

Install `Paradox Alarm Interface` version `1.3.1+` from there.

Configure options json. All configuration parameters from [pai.conf.example](https://github.com/ParadoxAlarmInterface/pai/blob/master/config/pai.conf.example) should be supported.

Make sure you specify `MQTT_HOST` properly. Host's IP address or docker interface IP is required. `127.0.0.1` or `localhost` will not work.

See [Configuration](./Configuration)

## Serial connection

See [serial connection method](./Connection-methods#serial-connection). You need to specify right port. Inspect your hass.io for available tty ports.
```
SERIAL_PORT = '/dev/ttyS1'
```

# Homebridge
We fully support homebridge via `mqttthing`.
Currently requires [dev](https://github.com/ParadoxAlarmInterface/pai/tree/dev) branch

## PAI Configuration:
You need to have MQTT enabled.

## Homebridge configuration
In homebridge config.json add accessory:
```json
{
            "accessory": "mqttthing",
            "type": "securitySystem",
            "name": "First floor",
            "url": "mqtt://<ip_address>",
            "username": "<username>",
            "password": "<password>",
            "topics": {
                "setTargetState": "paradox/control/partitions/First_floor",
                "getTargetState": "paradox/states/partitions/First_floor/current_state",
                "getCurrentState": "paradox/states/partitions/First_floor/current_state"
            },
            "currentStateValues": [
                "armed_home",
                "armed_away",
                "armed_night",
                "disarmed",
                "triggered"
            ],
            "targetStateValues": [
                "arm_stay",
                "arm",
                "arm_stay",
                "disarm"
            ],
            "restrictTargetState": [ 1, 3 ]
```
Paradox does not support `arm_night`, so we map it to `arm_stay`

Restart homebridge.

Enjoy.
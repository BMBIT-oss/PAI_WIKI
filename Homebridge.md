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
                "getTargetState": "paradox/states/partitions/First_floor/target_state",
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
                "armed_home",
                "armed_away",
                "armed_night",
                "disarmed"
            ],
            "restrictTargetState": [ 1, 3 ]
}
```
If you need `arm_stay` or `arm_sleep`, change your `restrictTargetState`. It is an array of `targetStateValues` indexes to exclude.

EVO panels do not support `armed_night`.

Motion sensors:
```json
{
"accessory": "mqttthing",
"type": "motionSensor",
"name": "NAME-OF-ZONE",
"url": "mqtt://<ip_address>",
"username": "<username>",
"password": "<password>",
"topics": {
    "getMotionDetected": "paradox/states/zones/NAME-OF-ZONE/open"
},
    "onValue": "True",
    "offValue": "False"
}
```

Restart homebridge.

Enjoy.
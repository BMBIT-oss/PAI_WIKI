We fully support homebridge via `mqttthing`.
Support was added in 1.1.0

## PAI Configuration:
You need to have MQTT enabled.

Some alieases in `MQTT_COMMAND_ALIAS` are required for hombridge to function correcltly. Make sure you've kept them if you override `MQTT_COMMAND_ALIAS` default settings.

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
If you need `arm_stay` or `arm_sleep`, change your `restrictTargetState`. It is an array of `targetStateValues` indexes to include.

You can lookup escaped partition names like `../partitions/First_floor` in MQTTExplorer.

Unescaped `../partitions/First floor` will not work.

EVO panels do not support `armed_night`. You can use:```"restrictTargetState": [
                0,
                1,
                3
            ]```

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

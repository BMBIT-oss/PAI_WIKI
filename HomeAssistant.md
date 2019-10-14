# HomeAssistant
We support HomeAssistant with MQTT autodiscovery.
Currently requires [dev](https://github.com/ParadoxAlarmInterface/pai/tree/dev) branch

## PAI Configuration:
Enable required module
```python
MQTT_HOMEASSISTANT_AUTODISCOVERY_ENABLE = True
```

## HomeAssistant configuration
In HomeAssistant configuration.yaml:
```yaml
mqtt:
  broker: <ip>
  username: <username>
  password: <password>
  discovery: true
```

Restart HomeAssistant.

Goto Configuration -> Integrations -> MQTT: configuration.yaml see a thing with your panel model (EVO192, MG5050, ...).

## Remove unused zones and partitions
If you have a lot of "Zone ###", "Area ###", "User ###" garbage that is not used.

1. Open PAI Configuration and configure your zone limits:
```python
LIMITS = {
    'zone': list(range(1, 18)) + list(range(30,33)) + [40],     # Zones to monitor and control: 1-17,30-32,40
    'user': range(1, 7),     # Users to consider: 1-6
    'door': [],               # Doors to consider: ignore
    'pgm': range(1, 6),    # Outputs to monitor and control: 1-6
    'partition': [1,2,4,7,8], # Partitions to monitor and control: 1,2,4,7,8
    'bus-module': [],         # Buses to monitor: ignore
    'key-switch': []
}
```

2. Download [MQTT Explorer](http://mqtt-explorer.com/). It has a button for bulk remove topics.
3. Remove all topics under `homeassistant/alarm_control_panel/<panel_serial_number>`. 
4. Remove all topics under `homeassistant/binary_sensor/<panel_serial_number>`

5. Restart PAI to populate `homeassistant/alarm_control_panel` again.
6. Restart HomeAssistant to reset discovered results. Maybe you will need to remove MQTT Integration and add it again.
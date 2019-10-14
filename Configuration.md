## Configuration
See [config/pai.conf.example](config/pai.conf.example) for all configuration options.

Configuration file should be placed in one of these locations:
  - /etc/pai/pai.conf
  - /usr/local/etc/pai/pai.conf
  - ~/.local/etc/pai.conf

### EVO specifics
As project was initially designed for SP/MG panels. EVO panels require some configuration fine tuning.

Set these settings
``` python
STATUS_REQUESTS = list(range(1, 6))

PARTITIONS_CHANGE_NOTIFICATION_IGNORE = [
  'arm_full',
  'exit_delay',
  'all_zone_closed', 
  'ready',
  'stay_instant_ready',
  'force_ready',
  'entry_delay',
  'auto_arming_engaged'
]
```

If you use Serial connection you need to set *SERIAL_BAUD*:
``` python
SERIAL_BAUD = 38400 # or 57600 if you have changed default setting in Babyware
```
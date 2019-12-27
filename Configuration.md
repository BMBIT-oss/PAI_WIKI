## Connection
Start from [picking a connection method](./Connection-methods)

## Advanced configuration
See [config/pai.conf.example](https://raw.githubusercontent.com/ParadoxAlarmInterface/pai/master/config/pai.conf.example) for all configuration options.

Configuration file should be placed in one of these locations:
  - /etc/pai/pai.conf
  - /usr/local/etc/pai/pai.conf
  - ~/.local/etc/pai.conf

Alternatively path to configuration file can be passed via command line (`-c` parameter) or `PAI_CONFIG_FILE` environment variable.

Configuration can be `.json`, `.yaml` or a `.py|.conf` file. Make sure file extension is right.

### EVO specifics
As project was initially designed for SP/MG panels. EVO panels require some configuration fine tuning.

Set these settings
``` python
STATUS_REQUESTS = list(range(1, 6)) # [1,2,3,4,5]
```

If you use Serial connection you need to set *SERIAL_BAUD*:
``` python
SERIAL_BAUD = 38400 # or 57600 if you have changed default setting in Babyware
```

## Improving performance
By default PAI will download only enabled zones and partitions. But ALL users, doors and pgms. It will keep track and post updates of all these assets to your integration/interface. This is a large amount of extra data.

It is recommended to narrow this down and specify only assets you want to monitor.

All examples that are possible are listed here:
```python
LIMITS = {
    'zone': list(range(1, 18)) + list(range(30,33)) + [40],     # Zones to monitor and control (1-17,30-32,40)
    'user': range(1, 7),     # Users to consider (1-6)
    'door': [],               # Doors to consider (none)
    'pgm': range(1, 6),    # Outputs to monitor and control (1-5)
    'partition': [1,2,4,7,8], # Partitions to monitor and control (1,2,4,7,8)
    'bus-module': [],         # Buses to monitor (none)
    'key-switch': [] (none)
}
```

PAI will automatically pick enabled partitions and zones if `LIMITS['zone']` and `LIMITS['partition']`(only EVO) is not defined.

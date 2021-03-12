## Connection
Start from [picking a connection method](./Connection-methods)

## PC Password
PAI requires a **4 digit** PC Password. It is **not** a user password and **not** an installer password. That password is used to connect to your panel with Babyware. Default password is usually `0000`. You might need to contact your installer to get this password or if you have an installer password you can lookup it up in right programming section on Keypad. If you know the password is right and you still get `AuthenticationFailed` then check [FAQ](./FAQ#authentication-failed-wrong-password)

## Advanced configuration
See [config/pai.conf.example](https://raw.githubusercontent.com/ParadoxAlarmInterface/pai/master/config/pai.conf.example) for all configuration options.

Configuration file should be placed in one of these locations:
  - /etc/pai/pai.conf
  - /usr/local/etc/pai/pai.conf
  - ~/.local/etc/pai.conf

Alternatively path to configuration file can be passed via command line (`-c` parameter) or `PAI_CONFIG_FILE` environment variable.

Configuration can be `.json`, `.yaml` or a `.py|.conf` file. Make sure file extension is right.

### Label encodings
```yaml
LABEL_ENCODING: paradox-en
```

Additionally supported (2.5.2+):
* `paradox-ar`: Arabic
* `paradox-de`: German
* `paradox-el`: Greek
* `paradox-en`: English
* `paradox-et`: Estonian
* `paradox-he`: Hebrew
* `paradox-hu`: Hungarian
* `paradox-pl`: Polish
* `paradox-pt`: Portuguese
* `paradox-ro`: Romanian
* `paradox-ru`: Russian
* `paradox-tr`: Turkish
* `cp936`: Chinese
* or any from standard encodings https://docs.python.org/3/library/codecs.html#standard-encodings

Thanks @tekand

### EVO Serial baud rate
If you use Serial connection you need to set `SERIAL_BAUD`:
```yaml
SERIAL_BAUD: 38400 # or 57600 if you have changed default setting in Babyware
```

## Improving performance
### Panel side
In Babyware it is recommended to check `System Options -> Communication -> “Transmit zone status on serial port”`. This way events will be automatically sent by the panel and received without much delay.

Set the Baud Rate to the highest possible value. EVO supports 38400 (default) and 57600 which is a 50% boost. 
MG5050 and other MG panels only support 9600, but don't fret over it. In these panels events only have 37 bytes and take ~500 microseconds to be sent.

### PAI side
By default PAI will download only enabled zones and partitions. But ALL users, doors and pgms. It will keep track and post updates of all these assets to your integration/interface. This is a large amount of extra data.

It is recommended to narrow this down and specify only assets you want to monitor.

All examples that are possible are listed here:
```yaml
LIMITS:
    zone: '1-17,30-32,40'   # Zones to monitor and control (1-17,30-32,40)
    user: '1-6'             # Users to consider (1-6)
    door: ''                # Doors to consider (none)
    pgm: '1-5'              # Outputs to monitor and control (1-5)
    partition: 'auto'       # Partitions to monitor and control (autodetect)
    bus-module: ''          # Buses to monitor (none)
    key-switch: ''          # (none)
```

In EVO, PAI can automatically detect enabled partitions and zones if you set them to `auto`.

## Problems?
Check [FAQ](./FAQ)
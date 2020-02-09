# Requirements
- Supported panel (SP/MG/EVO)
- MQTT Broker (we tried only with `mosquitto`. Others may work) for communication with other systems
- Python 3.6+ with pip (3.7, 3.8 recommended).

## Installation

1.  Download the files in this repository and place it in some directory
```
git clone https://github.com/ParadoxAlarmInterface/pai.git
```

2.  Copy ```config/pai.conf.example``` to ```/etc/pai/pai.conf``` and edit it to match your setup. The file uses Python syntax.
```
mkdir -p /etc/pai
cp config/pai.conf.example /etc/pai/pai.conf
edit /etc/pai/pai.conf as needed
./run.sh
```

Alternatively see [Configuration](./Configuration) section for supported file locations.

3.  Install the python 3.6 and its requirements.
```
pip3 install -r requirements.txt
```

If some requirement fail to install, this may not be critical.
* ```gi```, ```pygobject``` and ```pydbus``` are only required when using Signal
* ```Pushbullet.py``` and ```ws4py``` are only required when using Pushbullet
* ```chump``` is only required when using Pushover
* ```paho_mqtt``` is only required for MQTT support
* ```pyserial``` is only required when connecting to the panel directly through the serial port or using a GSM modem.

4. Run
```
./run.sh
```

If something goes wrong, you can edit the configuration file to increase the debug level. You can also run PAI with an alternative config by executing:
```
./run.sh -c pai.conf
```

You can override configuration valiables like this:
```
VAR_NAME1=x VAR_NAME2=y ./run.sh
```

## Autostart on system boot using systemd

Download [pai.service](https://raw.githubusercontent.com/ParadoxAlarmInterface/pai/master/config/systemd/pai.service) and put it to `/etc/systemd/system/pai.service`

Update paths in `pai.service`

Enable service and start
```
systemctl daemon-reload
systemctl enable pai.service
systemctl start pai.service
```

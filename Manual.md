# Manual

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
and you can override a single variable using:
```
VAR_NAME=x ./run.sh
```
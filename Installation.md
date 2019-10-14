# Docker

If you have docker running, this will be the easiest way:
```
mkdir /opt/pai/etc
mkdir /opt/pai/log

wget https://raw.githubusercontent.com/jpbarraca/pai/master/config/pai.conf.example -O /opt/pai/etc

docker run -it -v /opt/pai/etc:/etc/pai:ro -v /opt/pai/log:/var/log/pai --device=/dev/tty.YOUR_SERIAL_PORT jpbarraca/pai
```
edit /opt/pai/etc/pai.conf as needed, see [Configuration](./Configuration).

You also try ```jpbarraca/pai:latest```

The docker images do not have support for Signal.

or simply:

```
docker run -it -v <confFolder>/pai.conf:/etc/pai/pai.conf jpbarraca/pai
```

# Manually

1.  Download the files in this repository and place it in some directory
```
git clone https://github.com/jpbarraca/pai.git
```

2.  Copy ```config/pai.conf.example``` to ```/etc/pai/pai.conf``` and edit it to match your setup. The file uses Python syntax.
```
mkdir -p /etc/pai
cp config/pai.conf.example /etc/pai/pai.conf
edit /etc/pai/pai.conf as needed
python3 run.py
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
python3 run.py
```

If something goes wrong, you can edit the configuration file to increase the debug level.
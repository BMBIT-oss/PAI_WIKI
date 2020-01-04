Currently we allow next connection methods:
* [Serial connection](#serial-connection)
* [IP module connection (IP100, IP150)](#ip-module-connection-IP100-IP150)
* [Serial Over IP](#serial-over-ip-esp32) (ESP32)

# Serial connection
All wire connections you do at your own risk. It is very easy to burn your board if you plug something wrong.

Panel's serial port should be vacant to use this method.

For serial connection you will need a USB-TTL module or a level shifter if you want to connect to RPI directly.
Do not power you RPI from the Serial's AUX+.

## Paradox serial connector on the board:
```
┌───────┐
│ Rx   ┌╵
│ Tx   │
│ GND  │
│ AUX+ └╷
└───────┘
```
If you want a pretty connection then you can buy some of [these](https://www.ebay.com/itm/40-Sets-2-3-4-5P-2-54mm-Pitch-Terminal-Housing-Pin-Header-Connector-Adaptor-Kit/263446969764?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2057872.m2749.l2649)
 (a clamper tool required):

**Do not connect AUX+. It is 12V.**

RX/TX are 5V.

For serial connection usually you will need to use TX/RX/GND. If you use optocouplers, probably TX/RX will be sufficient.

### Configuration
```python
# Connection Type
CONNECTION_TYPE = 'Serial'  		# Serial or IP

# Serial Connection Details
SERIAL_PORT = '/dev/ttyS1' 		# Pathname of the Serial Port
SERIAL_BAUD = 9600              # 9600 for SP/MG. For EVO: Use 38400(default setting) or 57600 for EVO
```

EVO192 supports 38400(default setting for panel) or 57600. See panel's manual how to select a faster speed. This is main performance bottleneck, we do not want to fly slow :).

---

# IP module connection (IP100, IP150)
PAI can communicate with the panel using IP100 or IP150 connection locally or via Paradox Cloud(SWAN).

Connection process is reverse engineered from Babyware connection process. Unfortunately no public documentation available about this process.

Firmwares supported:
* **version < 4.x** - Local connection
* **4.x <= version < 4.40.004** - SWAN (Paradox cloud) connection.
* **version >= 4.40.004** - Local connection, SWAN (Paradox cloud) connection

It is recommended to configure your panel to highest possible baud rate for better performance. See your panel's user manual for available settings.

## Local connection
For IP150 firmware versions: **< 4.x and >= 4.40.004**
### Configuration
```python
# Connection Type
CONNECTION_TYPE = 'IP'  		# Serial or IP

# IP Connection Details
IP_CONNECTION_HOST = '192.168.1.10' # IP Module address when using direct IP Connection
IP_CONNECTION_PORT = 10000          # IP Module port when using direct IP Connection
IP_CONNECTION_PASSWORD = b'paradox' # IP Module password. "paradox" is factory default.
```

## Cloud connection (SWAN) _(not recommended)_
For IP150 firmware versions: **> 4.x**

Reason for _(not recommended)_:
* Periodic dropouts/restarts
* Possible banning (PAI keeps connection 24/7/365)
* Hard to debug reconnection problems

### Configuration
```python
# Connection Type
CONNECTION_TYPE = 'IP'  		# Serial or IP

IP_CONNECTION_SITEID = '<siteid>'		# SITE ID. IF defined, connection will be made through this method.
IP_CONNECTION_EMAIL = 'myemail@email.my' 		# Email registered in the site
IP_CONNECTION_PANEL_SERIAL = None       # Use a specific panel. Set it to None to use the first
```

---

# Serial Over IP (ESP32)
This is for noncommercial use and purely for educational purposes.
You can damage your hardware if you are not careful, aka the usual disclaimer.
 
I have tested a few MCU's and have found the ESP32 DOit Wrooom32 DEVKITv1
Very Reliable due to the hardware Serial Port. 
I do not use protection on mine, but if you feel like you want to OPTO isolate 
or use logic level converters you should and it most likely recommended.
Mine works fine for the TX and RX with no issues.
My Wemos D1 Mini that i ran without protection only worked for a while, 
I think it's mainly due to the the 5v connecting directly to the header.
(if you "need" ethernet you can investigate a MCU with ethernet)
I personally took on this for my Alarm as Lightning damaged my IP150 several times, 
and with Wireless and a single source of power i sleep easier. 

* You will need basic electronic skills and an understanding of the Arduino Framework.

I will update the design in more detail if needed
```
 Equipment Used
 *ESP32
 *LM2956 Breakout board (set to 5.0V )
 *USB Cable for ESP32 (that can be cut) [to connect between Lm and ESP]
 *Pin connector XH2.54 (4-Pin PWM Fan Female Connector (White)) [to connect to panel]
 *Connection for pinout below (I modified my existing IP150 Cable) 
```
The Pinout is as per the serial pinout above


```
Serial on Panel         LM2956            ESP32
┌───────┐               
│ Rx   ┌╵   == > == > == > == > == > == > TX2 
│ Tx   │    == > == > == > == > == > == > RX2 
│ GND  │    == > IN(-)   ->   OUT(-) == > USB GND 
│ AUX+ └╷   == > IN(+)   ->   OUT(+) == > USB 5V
└───────┘
```
The LM needs to be set to 5v before connecting the Node
the USB cable needs to be connected to the 5v output and then to the ESP32 to power the ESP 
* It is possible to connect the 5v directly to pin GND and 5v but this bypasses the protection afforded by the USB

You can follow the instructions edit and load the following:
https://github.com/espressif/arduino-esp32/blob/master/libraries/WiFi/examples/WiFiTelnetToSerial/WiFiTelnetToSerial.ino
once done and available on your network you can connect it to your wifi and test it/locate the IP.

The following settings needs to be set in your config
```python
# Connection Type
CONNECTION_TYPE = 'IP'  		# Serial or IP

# IP Connection Details
IP_CONNECTION_HOST = '192.168.1.10' # Address of the ESP32
IP_CONNECTION_PORT = 23             # Port of the ESP32
IP_CONNECTION_PASSWORD = b'paradox' # IP Module password. "paradox" is factory default.
IP_CONNECTION_BARE = True           # Used this for base Serial over TCP tunnels
```

Credits to @CyberTza

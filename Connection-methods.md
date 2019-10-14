# Connection methods
Currently we allow next connection methods:
* [Serial connection](#serial-connection)
* [IP150 module](#ip150-connection)

## Serial connection
All wire connections you do at your own risk. It is very easy to burn your board if you plug something wrong.

Panel's serial port should be vacant to use this method.

For serial connection you will need a USB-TTL module or a level shifter if you want to connect to RPI directly.
Do not power you RPI from the Serial's AUX+.

### Paradox serial connector on the board:
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

#### Configuration
```python
# Connection Type
CONNECTION_TYPE = 'Serial'  		# Serial or IP

# Serial Connection Details
SERIAL_PORT = '/dev/ttyS1' 		# Pathname of the Serial Port
SERIAL_BAUD = 9600              # 9600 for SP/MG. For EVO: Use 38400(default setting) or 57600 for EVO
```

EVO192 supports 38400(default setting for panel) or 57600. See panel's manual how to select a faster speed. This is main performance bottleneck, we do not want to fly slow :).

## IP150 connection
PAI can communicate with the panel using IP150 connection locally or via Paradox Cloud(SWAN).

Connection process is reverse engineered from Babyware connection process. Unfortunately no public documentation available about this process.

Firmwares supported:
* **version < 4.x** - Local connection
* **4.x <= version < 4.40.004** - SWAN (Paradox cloud) connection.
* **version >= 4.40.004** - Local connection, SWAN (Paradox cloud) connection

It is recommended to configure your panel to highest possible baud rate for better IP150 performance. See your panel's user manual for available settings.

### Local connection
For IP150 firmware versions: **< 4.x and >= 4.40.004**
#### Configuration
```python
# Connection Type
CONNECTION_TYPE = 'IP'  		# Serial or IP

# IP Connection Details
IP_CONNECTION_HOST = '192.168.1.10' # IP Module address when using direct IP Connection
IP_CONNECTION_PORT = 10000          # IP Module port when using direct IP Connection
IP_CONNECTION_PASSWORD = b'paradox' # IP Module password. "paradox" is factory default.
```

### Cloud connection (SWAN) _(not recommended)_
For IP150 firmware versions: **> 4.x**

Reason for _(not recommended)_:
* Periodic dropouts/restarts
* Possible banning (PAI keeps connection 24/7/365)
* Hard to debug reconnection problems

#### Configuration
```python
# Connection Type
CONNECTION_TYPE = 'IP'  		# Serial or IP

IP_CONNECTION_SITEID = '<siteid>'		# SITE ID. IF defined, connection will be made through this method.
IP_CONNECTION_EMAIL = 'myemail@email.my' 		# Email registered in the site
IP_CONNECTION_PANEL_SERIAL = None       # Use a specific panel. Set it to None to use the first
```
## Errors
### Authentication Failed. Wrong Password
1. Make sure you know your PC password. In Babyware: `Right click on your panel -> Properties -> PC Communication (Babyware) -> PC Communication (Babyware) Tab.`
   1. EVO: Section 3012
2. Make sure you filled `PASSWORD` config parameter in the right config. See run log where config file is located.
3. If your password has `0` try to replace it with `a`. Some panels want `0` encoded this way. `0306` -> `a3a6`.
4. PC password is always a 4 digit number. Even if you have configured 6 digit passwords in your panel settings.

### No handler for message 5
Real reason is unknown.

This error means that a status request failed. Do not panic. It is normal if it is infrequent. 5-6 status requests are sent every 10 seconds. Probably when a panel sends a live event payload at the same time when PAI waits for a status request response, panel hangs and responds much later. We do not want to wait for more than 10 seconds for this reply. So we dispose handler and go further. Next batch will surely succeed.

### Failed to connect: Panel did not replied to InitiateCommunication
1. Is you panel listed in [Compatibility](../Compatibility) page?
2. Verify that you can connect to your panel with Babyware.
3. If you use serial connection
   1. Make sure you properly specified `SERIAL_BAUD` rate. In EVO you have two options.
   2. If you skipped step 2. try swapping TX<->RX

## Debug
How to enable debug?

Our debug is very verbose so we do not recommend constantly running PAI in this mode.

Configuration if you are using Pythonic config:
```
LOGGING_DUMP_PACKETS = True
LOGGING_DUMP_MESSAGES = True
#LOGGING_DUMP_STATUS = True  # in rare cases

# if you want to log to console
LOGGING_LEVEL_CONSOLE = logging.DEBUG

# if you want to log to a file
LOGGING_LEVEL_FILE = logging.DEBUG
LOGGING_FILE = '/tmp/paradox.log' 
```

Configuration if you are using HASS.io:
```

LOGGING_DUMP_PACKETS: true
LOGGING_DUMP_MESSAGES: true
#LOGGING_DUMP_STATUS: true  # you will need it in very rare cases

# if you want to log to console (10 = debug)
LOGGING_LEVEL_CONSOLE: 10

# if you want to log to a file (10 = debug)
LOGGING_LEVEL_FILE: 10
LOGGING_FILE: '/tmp/paradox.log' 
```

`LOGGING_DUMP_PACKETS` and `LOGGING_DUMP_MESSAGES` will only be logged is you set `LOGGING_LEVEL_<destination>` to debug

## Other Questions
### Which panel is best for using with PAI
We consider EVO192/HD are the best panels available to use with PAI. Why? Answer is simple. They have the fastest serial port with baud rate 57600(38400 default) vs 9600 on SP/MG panels. If you want IP150. Answer is the same because IP150 uses panel's serial port.
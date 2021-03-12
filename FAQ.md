## Errors
### Authentication Failed. Wrong Password
1. Make sure you know your PC password. In Babyware: `Right click on your panel -> Properties -> PC Communication (Babyware) -> PC Communication (Babyware) Tab.`
   1. EVO: Section 3012
2. Make sure you filled `PASSWORD` config parameter in the right config. See run log where config file is located.
3. If your password has `0` try to replace it with `a`. Some panels want `0` encoded this way. `0306` -> `a3a6`.
4. PC password is always a 4 digit number. Even if you have configured 6 digit passwords in your panel settings.
5. If PAI has made many connection attempts with wrong password Panel can lock all further connection attempts for some time period. Give it some rest and retry.

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

## Capture Babyware <-> IP150 communication:
Some times we ask owners of IP150 to capture Babyware <-> IP150 communication to solve a problem.

There are two options how you can do it:
- **Simple and less secure:** Do a Wireshark capture filtering by ip of your IP150 module and send it to us via a safe channel. This method has one big negative side: By doing this you agree that we will see all your configuration including user passwords, how many modules, zones and partitions you have, labels and so on. Everything that is saved on the panel and Babyware loads. We do not need all this info but this is the simplest method.
- **Advanced and more secure:** You still do a Wireshark capture like you do in simple method but you decrypt the communication yourself using `ip150-connection-decrypt` feeding in your IP150 module password and a YAML file of TCP Flow acquired from Wireshark capture. Then you manually strip all sensitive information and keep only relevant part that is required to solve the problem. This way you do not share sensitive information with us.

### How to make a capture with Wireshark
1. Install Wireshark on the machine where you have BabyWare installed.
2. Run Babyware and setup local IP connection if you have not done it yet.
3. Try connecting. If succeeded close Babyware and rerun again but do not connect this time.
3. Run Wireshark, pick your main interface and type capture filter `host 192.168.1.2`. Replacing IP address with your IP150 module IP.
4. Start the capture
5. Connect with Babyware
6. Do not perform any actions in Babyware until it fully loads the data. When all events will be loaded then you can proceed.
7. Do required actions to solve the main reason why we did this capture. Keep en eye on packet numbers in Wireshark, save them somewhere for later
8. Stop capture
9. Disconnect Babyware

Note: Make sure you saved only one Babayware connection in one capture. Redo the steps if you failed.

### If you went Advanced and you want to decrypt yourself

10. Save capture as YAML: Right click on the first packet in Wireshark. `Follow` -> `TCP Stream`. Pick `Entire conversation` and `Show and save data as YAML`. Push `Save as...` button in the bottom. Save the file.
11. We assume you have PAI installed already with all dependencies.
    1. If you git cloned PAI. Checkout `dev` branch and pull. Run `python -m paradox.console_scripts.ip150_connection_decrypt path_to_yaml_file.yaml [ip150_password] > decrypted.txt`. `ip150_password` default is `paradox`.
    2. If you have installed PAI via pip or `setup.py install` then you should have `ip150-connection-decrypt` command in your path. Run `ip150-connection-decrypt path_to_yaml_file.yaml [ip150_password] > decrypted.txt`. `ip150_password` default is `paradox`.
12. Open `decrypted.txt` and remove all that you think is irrelevant.
13. That is it! You did it. 👏 💪 

## Other Questions
### Which panel is best for using with PAI
We consider EVO192/HD are the best panels available to use with PAI. Why? Answer is simple. They have the fastest serial port with baud rate 57600(38400 default) vs 9600 on SP/MG panels. If you want IP150. Answer is the same because IP150 uses panel's serial port. EVO support in PAI went a bit further than SP/MG.
PAI requires an external MQTT broker to be configured in pai.conf 

At a minimum, you will need to configure the following MQTT settings (set MQTT_HOST to something applicable to your setup).


MQTT_ENABLE = True                      # Enable MQTT Interface
MQTT_HOST = localhost                   # Hostname or address for your MQTT server
MQTT_PORT = 1883                        # TCP Port


# 

The MQTT Interface allows accessing all relevant states and setting some. The list of states will increase as the knowledge of the alarm internal memory and its states is improved.

All interactions are made through a `MQTT_BASE_TOPIC`, which defaults to `paradox`. States are exposed by name with a boolean payload (True or False) and are mainly update by the alarm status messages, updated every `KEEP_ALIVE_INTERFACE` seconds.

## States
* `paradox/states/partitions/name/property`: Exposes Partition properties where `name` identifies the Partition name (e.g. Interior) and `property` identifies the property (e.g. `arm`).
* `paradox/states/zones/name/property`: Exposes Partition properties where `name` identifies the Zone name (e.g. Kitchen) and `property` identifies the property (e.g. `open`).
* `paradox/states/outputs/name/property`: Exposes Partition properties where `name` identifies the PGM name (e.g. Gate) and `property` identifies the property. (EVO does not have this yet)

The following states are supported:

* __buses__ : `supervision_trouble, tamper, trouble`
* __keypads__: `ac_loss_trouble, battery_failure_trouble, signal_strength, supervision_failure_trouble, trouble`
* __outputs__: `signal_strength, supervision_trouble, tamper, trouble`
* __partitions__: `alarm, alarms_in_memory, arm, arm_with_remote, audible_alarm, auto_arming_engaged, bell_activated, bell_delay_finished, entry_delay,entry_delay_finished, exit_delay, exit_delay_finished, force_arm, in_remote_delay, intellizone_delay, intellizone_delay_finished, paramedic_alarm, pulse_fire_alarm, ready_status, recent_closing_delay, silent_alarm, sleep_arm, stay_arm, stayd_mode_active, strobe_alarm, transmission_delay_finished, wait_window, zone_bypassed`
* __repeaters__: `ac_loss_trouble, battery_failure_trouble, signal_strength, supervision_trouble, trouble`
* __system__: `battery, dc, rf_noise_floor, vdc`
* __zones__: `alarm, bypassed, entry_delay, exit_delay, fire, fire_delay, intellizone_delay, in_tx_delay, no_delay, open, rf_low_battery_trouble, rf_supervision_trouble, shutdown, signal_strength, tamper, trouble, was_bypassed, was_in_alarm`

## Events
* `paradox/states/raw`: Exposes raw event information, composed by a minor and major codes, as well as descriptive text. The payload is a JSON object with the following structure:
```
    {
        'major': (major_code, 'major_text'),
        'type': 'event_type',
        'minor': (minor_code, 'minor_text')
    }

```
* `major_code`  and `major_text` represent the major code and corresponding text description as provided by the alarm. This will mostly identify the event.
* `minor_code`  and `minor_text` represent the minor code and corresponding text description as provided by the alarm. This will mostly identify a detail of the event, such as the zone number/name, the user name, partition name, and so on.
* `type` identifies the event category, and can take the following values: `Partition`, `Bell`, `NonReportable`, `User`, `Remote`, `Special`, `Trouble`, `Software`, `Output`, `Wireless`, `Bus Module`, `Zone`, `System`.


## Control

The MQTT Interface allows setting some properties for individual objects by specifying the correct name. In alternative, the `all` keyword can be used to apply the same setting to all objects. This is useful to activate all PGMs or to Arm/Disarm all partitions.
`name` in the topic needs to be replaced by a name with spaces replaced with underscores.

* `paradox/control/partitions/name` allow setting some partition properties where `name` identifies the partition. If the `name` is `all`, all partitions will be updated. The payload can have the values `arm`, `arm_stay`, `arm_sleep`, `arm_stay_stayd`,  `arm_sleep_stay` or `disarm` and `disarm_all`.
* `paradox/control/zones/name` allow setting some zone properties where `name` identifies the zone. If the `name` is `all`, all zones will be updated. The payload can have the values `bypass` and `clear_bypass`.
* `paradox/control/outputs/name` allow setting some PGM properties where `name` identifies the PGM. If the `name` is `all`, all PGMs will be updated. The payload can have the values `pulse` (Not on EVO), `on`, `on_override`, `off` or `off_override`, `release` (Only on EVO).

Check command execution result in logs.

`name` sanitising function to put in the topic:
```bash
python -c 'name="my partition name"; import re; print(re.sub(r"\W", "_", name).strip("_"))'
```

## Notifications

You can ask PAI to send a notification to other text interfaces (Pushover, Pushbullet, Signal, GSM)

`paradox/notifications/info`

* `info` is message level. For Pushover it will convert to priority. Supported are: `NOTSET`, `DEBUG`, `INFO`, `WARN`, `ERROR`, `CRITICAL`.

Message content should be put into MQTT payload

## Code Toggle

Sometimes it is useful to toggle the ARM state through a remote device, such as a NFC reader. Therefore, Partitions arm state can be toggled by issuing a special command with the format `code_toggle-code_number` (e.g., code_toggle-123456755). The `code_toggle-` keyword is constant, while the `code_number` is provided by the card (e.g., Card ID). If the code is present in the `MQTT_TOGGLE_CODES`, the partition state will be toggled.

The `MQTT_TOGGLE_CODES` should be composed by a dictionary where the key contains the code, and the value contains a description. This allows for easily sending notifications in the form: "Alarm unlocked by USERNAME".

This was throughly tested with [ESPEasy](https://www.letscontrolit.com/) running on a ESP8266 board (e.g. NodeMCU or Wemos D1 Mini) connected to a PN532 NFC reader.
Besides the typical ESPEasy configuration, the only "code" required is a simple rule. The following example will publish any ID to the correct topic and will flash a LED for 2 seconds.

```
on reader#tag do
   Publish paradox/control/partitions/all,code_toggle-[reader#tag]
   Pulse,2,0,2000
endon
```

`reader#tag` identifies the ESPEasy PN532 device name (`reader`) and the property holding the RFID ID (`tag`).
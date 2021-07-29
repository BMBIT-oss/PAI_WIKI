We support Home Assistant with MQTT autodiscovery.
Support was added in 1.1.0

## Requirements
### MQTT Broker
Options:
* [Mosquitto](https://mosquitto.org/)
* HomeAssistant OS [Mosquitto addon](https://github.com/home-assistant/addons/blob/master/mosquitto/DOCS.md)

You need to create a username+password pair for PAI that you will use in PAI configuration.

## Paradox Alarm Interface (PAI) add-on configuration:
Enable the required module in the add-on configuration tab and all the other parameters.
```yaml
MQTT_HOMEASSISTANT_AUTODISCOVERY_ENABLE: true
```

## Home Assistant configuration
If using Home Assistant's default mqtt broker there is no need to modify configuration.yaml

Note that after setting `MQTT_HOMEASSISTANT_AUTODISCOVERY_ENABLE: true`, Home Assistant will now auto-discover all zones, partitions and PGMs automatically. No additional settings are required for configruation.yaml. 

Restart Home Assistant.

Goto Configuration -> Integrations -> You should see a device with your panel model (EVO192, MG5050, ...).

### Make Home Assistant to ask for a code to arm/disarm (dev branch)
If you want to enter a code to arm or disarm the alarm in Home Assistant or HassKit.

1. Set `MQTT_HOMEASSISTANT_CODE` to the code you want
2. Goto `Configuration -> Customizations`.
3. Find your `alarm_control_panel` partition.
4. Check `code_arm_required` and `code_disarm_required`.
5. Now you need to enter the code to disarm and arm.

### Lovelace

If you want more or less arming states than default `arm_home` and `arm_away` take a look at the [manual](https://www.home-assistant.io/lovelace/alarm-panel/).

## Remove unused zones, partitions, users or PGMs
You can limit (filter) the zones, partitions, users or PGMs that are exposed to Home Assistant as it can become cluttered. To do so you hvave to use the `LIMITS` option in your configuration:

1. Stop the PAI add-on
2. Open PAI add-on configuration and configure your zone limits (only listed items will be exposed):
```yaml
LIMITS:
  user: '1-3'
  pgm: '3'
  door: ''
  module: ''
  repeater: ''
  partition: '1,2,3-4'
```

3. Download [MQTT Explorer](http://mqtt-explorer.com/) or similar and remove the following topics:
  - all topics under `homeassistant/alarm_control_panel/<panel_serial_number>` 
  - all topics under `homeassistant/binary_sensor/<panel_serial_number>`
  - any other unnecessary topics form `switch` or `sensor`

4. Restart PAI add-on to populate `homeassistant/alarm_control_panel` again.
5. Restart Home Assistant to reset discovered results (this might be unnecessary).

## Custom notifications and automations

PAI supports several methods to deliver instant messages. However, if you are interested in using Home Assistant to deliver custom messages, or to trigger other automations based on any event, you may use the raw event feed.

The following automation, provided by [@rjcds](https://github.com/rjcds), sends a custom message through pushover with the name of the user that armed/disarmed the panel. This is a great way of sending messages in your own language. Feel free to change the service to notify.notify instead of notify.pushover.


```yaml
- alias: ArmedBy
  trigger:
    platform: mqtt
    topic: 'paradox/events/raw'
  condition:
    condition: template
    value_template: >
      {{ trigger.payload_json['change']['arm'] == True or
         trigger.payload_json['change']['arm'] == False }}
  action:
    service: notify.pushover
    data:
      message: >
        {% if 'master' in trigger.payload_json['message'] and trigger.payload_json['change']['arm'] == True -%}
            House armed by {{ trigger.payload_json['message'][17:-12] }}
        {%- elif 'master' in trigger.payload_json['message'] and trigger.payload_json['change']['arm'] == False -%}
            House disarmed by {{ trigger.payload_json['message'][19:-12] }}
        {%- elif trigger.payload_json['change']['arm'] == True -%}
            House armed by {{ trigger.payload_json['message'][17:-5] }}
        {%- else -%}
            House disarmed by {{ trigger.payload_json['message'][19:-5] }}
        {%- endif %}

```

The raw messages being parsed are:
```
Arming XXXX with YYYY master code
Unarming XXXX with YYYY master code
Arming XXXX with ZZZZ code
Unarming XXXX with ZZZZ code
```

You will need to adjust the string slicing if your partition name is not 4 characters long (eg `Home`) 



Similarly, the following automation will notify critical events as both homeassistant notifications and persistent notifications, for instance zones being tampered or power trouble.

```yaml
- alias: AlarmCritical
  trigger:
    platform: mqtt
    topic: 'paradox/events/raw'
  condition:
    condition: template
    value_template: >
      {{ trigger.payload_json['level'] == 'CRITICAL' }}
  action:
    - service: persistent_notification.create
      data:
        title: >
          {{ trigger.payload_json['label'] }} {{ trigger.payload_json['message'] }}
        message: >
          {{ trigger.payload_json['label'] }} {{ trigger.payload_json['message'] }} {{ trigger.payload_json['key'] }}
    - service: notify.notify
      data:
        title: >
          {{ trigger.payload_json['label'] }} {{ trigger.payload_json['message'] }}
        message: >
          {{ trigger.payload_json['label'] }} {{ trigger.payload_json['message'] }} {{ trigger.payload_json['key'] }}  
```
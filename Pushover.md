1. On [Pushover](https://pushover.net/) website
    1. Register
    2. Create New Application/API Token
    3. Take a note of API Token/Key
    4. Register some devices.
    5. Change device names
    5. _(optional) Create a group_
    6. Take a note of group or user token/key
    7. Test via `Push a Notification` for a target group or all devices
2. If all is fine proceed to PAI [Configuration](#Configuration)

# Configuration
```yaml
PUSHOVER_ENABLE: true
PUSHOVER_KEY: 'xxxxx'

# Send to specific devices
PUSHOVER_BROADCAST_KEYS:
  - user_key: 'yyyyy'
    devices: 'My_iPhone,Wife_iPhone'
# - or -
# Send to whole group or all devices on account
# PUSHOVER_BROADCAST_KEYS:
#   - user_key: 'yyyyy'
#     devices: '*'
```
YAML:
```yaml
PUSHOVER_ENABLE: true
PUSHOVER_KEY: xxxxx
PUSHOVER_BROADCAST_KEYS:
 - user_key: yyyy
   devices: '*'
```

Where:

- `xxxxx` is Application/API token/key
- `yyyyy` is your User or Group Key on your pushover account.
- `My_iPhone,Wife_iPhone` should be a list of comma separated device names as on pushover website. `*` can be used to send to all devices.

## Events/Notifications to send
See [Event filtering](./Event-filtering)

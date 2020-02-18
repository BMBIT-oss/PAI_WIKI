# Event system

We have two types of events:
- `live` events - are events that your panel sends to the serial bus.
- `change` event - are changes to a parameter produced by a status fetching or a `live` event.

In most cases you want to get notification on a `live` event.

EVO and SP/MG events and their tags are very different.

As our target is to minimise configuration for the users we do our best to maintain default configuration to suit most user needs.

If you receive multiple notifications on the same event please create an issue so we could solve it. Me need your help here.

## Event filtering
Most of the notifications from text interfaces that PAI implements can be configured with `*_EVENT_FILTERS` configuration parameter and `*_MIN_EVENT_LEVEL`

Filtering is done by using keywords that should be included or excluded in a firing event and event level.

**Keywords/tags include:**
* event type: `live` or `change`
* event tags
    * For `live` events: [EVO](https://github.com/ParadoxAlarmInterface/pai/blob/master/paradox/hardware/evo/event.py) or [SP/MG](https://github.com/ParadoxAlarmInterface/pai/blob/master/paradox/hardware/spectra_magellan/event.py). Lookup for `tags`.
    * For `change` events: [EVO](https://github.com/ParadoxAlarmInterface/pai/blob/master/paradox/hardware/evo/property.py) or [SP/MG](https://github.com/ParadoxAlarmInterface/pai/blob/master/paradox/hardware/spectra_magellan/property.py). Lookup for `tags`.
* event object type: `zone`, `partition`, ...
* event object label for `live` event: Example: `Living room`
* event object name for `live` event: Example: `Living_room`, note `_`. It is sanitized label
* event property change names and values: if event carries some property changes they also can be matched. Example: `generated_alarm=True`. Properties can be looked up in same files as it was for tags

**Event levels:**

| PAI Event level | Pushover message priority | Usage                                 |
|-----------------|---------------------------|---------------------------------------|
| NOTSET          | lowest                    |                                       |
| DEBUG           | lowest                    | All kind of events                    |
| INFO            | low                       | User armed, disarmed, ...             |
| WARN            | normal                    | Alarm restore, system/module troubles |
| ERROR           | high                      | Do not know why we do not use it      |
| CRITICAL        | emergency                 | Alarm                                 |

See [Pushover API](https://pushover.net/api) Message Priority section.

## Explanation

For example default for PUSHOVER_EVENT_FILTERS:
```python
# PUSHOVER_EVENT_FILTERS = [ # list of tags to include or exclude see hardware event.py for tag list
#     'alarm,-restore', # or -> matches events with "alarm" tag but skips that have "restore" tag
#     'trouble,-clock', # or
#     'tamper'
# ]
```

In the example above `PUSHOVER_EVENT_FILTERS` has 3 lines. For event to match and issue a notification at least one line must match.

Each line consists of a **comma**/**space**/**+**/**-**/... separated from a list of keywords that need to be included or excluded in an fired event's keywords to be handled by the interface

* `alarm,-restore` means that match all events that have `alarm` tag but do not have `restore` tag.
* `live,zone,generated_alarm=True+presently_in_alarm=True` means it should be `live` event on `zone` object that carries changes to properties and their values: `generated_alarm` (`True`) and `presently_in_alarm` (`True`).
* `live,generated_alarm=` means it should be event on `zone` object and should have `generated_alarm` property change. Value of the `generated_alarm` is irrelevant.

What if you want to get notifications for `arm` and `disarm` in additional to default setting?
Solution will be:
```python
PUSHOVER_EVENT_FILTERS = [
   'alarm,-restore',
   'trouble,-clock',
   'tamper',
   'arm',
   'disarm'
]
```

You need to make sure `*_MIN_EVENT_LEVEL` allows your event to pass to the interface.
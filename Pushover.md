# Configuration
```python
PUSHOVER_ENABLE = True
PUSHOVER_KEY = 'xxxxxxxxxxxxxxxxxxx'
PUSHOVER_BROADCAST_KEYS = {
    'yyyyyyyyyyyyyyyyyyyyyy': 'My_iPhone'
}
```

## Events to send
Default:
```python
# PUSHOVER_EVENT_FILTERS = [ # list of tags to include or exclude see hardware event.py for tag list
#     'alarm,-restore', # or -> matches events with "alarm" tag but skips that have "restore" tag
#     'trouble,-clock', # or
#     'tamper'
# ]
```

In case you want to get notifications also for `arm` and `disarm`
```python
PUSHOVER_EVENT_FILTERS = [
   'alarm,-restore',
   'trouble,-clock',
   'tamper',
   'arm',
   'disarm'
]
```

All available *tags* can be looked up here ([EVO](https://github.com/ParadoxAlarmInterface/pai/blob/master/paradox/hardware/evo/event.py), [SP/MG](https://github.com/ParadoxAlarmInterface/pai/blob/master/paradox/hardware/spectra_magellan/event.py))


# OpenHAB
OpenHAB configuration is a bit more complicated than Homebridge and HomeAssistant.

## PAI Configuration:
MQTT needs to be enabled.

## OpenHAB configuration
_things/broker.things_:
```ini
Bridge mqtt:broker:main "MQTT Broker" [ host="<ip_address>", username="<username>", password="<password>" ] {}
```
_things/paradox.things_:
```ini
Thing mqtt:topic:paradox_pai "PAI" (mqtt:broker:main) {
    Type contact: online "PAI Online" [
        stateTopic="paradox/interface/MQTTInterface", on="online", off="offline"
    ]
}

Thing mqtt:topic:paradox_partitions_arm "Alarm partitions" (mqtt:broker:main) {
    Channels:
        Type switch: First_floor "First floor" [
            stateTopic="paradox/states/partitions/First_floor/arm", on="True", off="False"
        ]
        Type switch: Second_floor "Second floor" [
            stateTopic="paradox/states/partitions/Second_floor/arm", on="True", off="False"
        ]
}

Thing mqtt:topic:paradox_zones_open "Alarm zones" (mqtt:broker:main) {
    Channels:
        Type contact: Living_room "Living room" [
            stateTopic="paradox/states/zones/Living_room/open", on="True", off="False"
        ]

        Type contact: Entrance "Entrance" [
            stateTopic="paradox/states/zones/Entrance/open", on="True", off="False"
        ]
}
```

_items/paradox.items_:
```ini
Group gParadox "Alarm"
Group:Switch:OR(ON,OFF) gParadoxPartitions "Areas" <siren> (gParadox)
Group:Contact:OR(OPEN,CLOSED) gParadoxZones "Zones" <motion> (gParadox)

Contact Paradox_PAI_Online "PAI online [%s]" (gParadox) {
  channel="mqtt:topic:paradox_pai:online"
}

// PARTITIONS
Switch Paradox_Partition_First_floor_Armed "First floor" <siren> (gParadoxPartitions) {
  channel="mqtt:topic:paradox_partitions_arm:First_floor",
  autoupdate="false"
}

Switch Paradox_Partition_Second_floor_Armed "Second floor" <siren> (gParadoxPartitions) {
  channel="mqtt:topic:paradox_partitions_arm:Second_floor",
  autoupdate="false"
}

// ZONES
Contact Paradox_Zone_Living_room "Living room [MAP(open_closed.map):%s]" <door> (gParadoxZones) {
  channel="mqtt:topic:paradox_zones_open:Living_room"
}

Contact Paradox_Zone_Entrance "Entrance [MAP(open_closed.map):%s]" <door> (gParadoxZones) {
  channel="mqtt:topic:paradox_zones_open:Entrance"
}
```

_transform/open_closed.map_:
```ini
OPEN=open
CLOSED=closed
NULL=unknown
-=unknown
```
_transform/arm.map_:
```ini
ON=armed
OFF=disarmed
NULL=unknown
-=unknown
UNDEF=unknown
```

_rules/paradox.rules_:
```ini
rule "Arm/disarm partition"
when
  Member of gParadoxPartitions received command
then
  val value = if (receivedCommand == ON) "arm" else "disarm"
  val partition = transform("REGEX", "Paradox_Partition_(\\w+)_Armed", triggeringItem.name)
  val topic = "paradox/control/partitions/" + partition + "/arm"

  val actions = getActions("mqtt","mqtt:broker:main")
  actions.publishMQTT(topic, value)
  logInfo("alarm", value + " " + partition)
end
```

_sitemaps/default.sitemap_
```ini
    Frame label="Alarm" visibility=[Paradox_PAI_Online==OPEN] {
      Text item=gParadoxPartitions label="Arm state [MAP(arm.map):%s]" {
        Frame label="Areas" {
          Default item=Paradox_Partition_First_floor_Armed
          Default item=Paradox_Partition_Second_floor_Armed
        }
        Frame label="Zones" {
          Group item=gParadoxZones icon="motion" valuecolor=[OPEN="red"]
        }
      }
    }
```

Restart OpenHAB. It does not like when you touch MQTT files and stuck sometimes.
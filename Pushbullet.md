The Pushbullet Interface allows accessing major state changes and arming/disarming partitions. As this interface will produce notifications to other devices, and are destined to users, only a subset of the events are sent.

## Configuration

In order to use this interface, please set the relevant configuration settings. The ```PUSHBULLET_CONTACTS``` setting should contain a list of contacts used for pushbullet notifications.

## Send command to PAI

To send a command to the alarm use the format: ```type object command```. One example would be: ```partition outside arm```. If the ```object``` is all, the command will be sent to all objects of the same type (e.g., all partitions, all zones).

## Events/Notifications to send
See [Event filtering](./Event-filtering)

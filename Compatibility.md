## User reported hardware attempted with PAI
### Firmware Upgrade WARNING:
**Do not upgrade EVO firmware versions to 7.50.000+ if you use Serial connection. Process is irreversible! Paradox introduces serial communication encryption which most probably will break our PAI ability to talk to the panel.**

### Supported
Other versions may be also supported. If PAI works with your panel, and the model-version combination is not listed, please edit the next table.

| Panel  |             | IP module |          |        | Connection |      |        | Notes                                                                                    |
|--------|-------------|-----------|----------|--------|------------|------|--------|------------------------------------------------------------------------------------------|
| Model  | Version     | IP100     | IP150    | IP150+ | Local      | SWAN | Serial |                                                                                          |
| EVOHD  | 4.50        | -         | -        |        | -          | -    | no     | Did not work with ESP32. [#198](https://github.com/ParadoxAlarmInterface/pai/issues/198) |
| EVOHD  | 6.91        | -         | 4.42     |        | ✓          | -    | -      |                                                                                          |
| EVOHD  | 7.30        | 4.42.002  | IP150    |        | ✓          | -    | -      |                                                                                          |
| EVOHD  | 7.31        | 5.20.0E   | -        |        | ✓          | -    | ✓      | Works with Serial via ESP32                                                              |
| EVO48  | 2.20        | 5.10      | -        |        | ✓          | -    | -      |                                                                                          |
| EVO192 | 2.66        | 5.10.06   | -        |        | ✓          | -    | -      |                                                                                          |
| EVO192 | 3.20        | -         | 5.3      |        | ✓          | -    | -      |                                                                                          |
| EVO192 | 6.86        | 5.10      | -        |        | ✓          | -    | -      |                                                                                          |
| EVO192 | 6.90        | 5.1.0     | 1.32     |        | ✓          | -    | ✓      |                                                                                          |
| EVO192 | 7.30        | -         | 1.32     |        | ✓          | -    | ✓      |                                                                                          |
| EVO192 | 7.30        | -         | 4.42     |        | ✓          | -    | -      |                                                                                          |
| EVO192 | 7.31        | -         | -        | 5.05   | ✓          | -    | -      |                                                                                          |
| EVO192 | 7.52.001    | -         | 1.34.000 |        | ✓          | -    | -      | [#272](https://github.com/ParadoxAlarmInterface/pai/issues/272) This panel FW has encrypted serial but connects fine via IP150|
| EVO192 | 7.70.018    | -         |  | 5.05.003 | ✓          | -    | -      | [#272](https://github.com/ParadoxAlarmInterface/pai/issues/272) This panel FW has encrypted serial but connects fine via IP150|
| MG5050 | 4.96        | -         | 4.40     |        | ✓          | -    | ✓      |                                                                                          |
| MG5050 | 4.72        | -         | 1.26     |        | ✓          | -    | -      |                                                                                          |
| MG5050 | 6.80        | -         | 4.40     |        | ✓          | -    | ✓      |                                                                                          |
| MG5050 | 7.15.002    | -         | 4.40.04  |        | ✓          | -    | -      | PID83h_V7_15_002_RF_V2_02_018_SPECTRUM                                                   |
| MG5050 | 7.01.012    | -         | 5.02.019 |        | ✓          | -    | -      | IP150 firmware 5.02.019                                                                  |
| MG5075 | 1.3 build 8 | -         | -        |        | ✓          | -    | -      | 307USB                                                                                   |
| SP4000 | 5.20        | -         | 5.02.019 |        | ✓          | -    | ✓      |                                                                                          |
| SP5500 | 2.40.1      | -         | -        |        | -          | -    | ✓      |                                                                                          |
| SP5500 | 4.54        | -         | 4.42     |        | ✓          | -    | -      |                                                                                          |
| SP5500 | 4.94        | -         | -        |        | ✓          | -    | ✓      | Works with Serial via ESP32                                                              |
| SP5500 | 7.14.002    | -         | -        |        | ✓          | -    | ✓      | USB to Serial via FTDI                                                                   |
| SP6000 | 4.15        | -         | -        |        | -          | -    | ✓      |                                                                                          |
| SP6000 | 6.80        | -         | 5.02     |        | ✓          | ✓    | -      | More stable with SWAN disabled                                                           |
| SP6000 | 4.54        | -         | -        | ✓      | ✓          | -    | -      | IP150+ working.                                                                          |
| SP6000 | 7.14.002    | -         | 5.02.19  | -      | ✓          | -    | -      |                                                                           |
| SP7000 | 5.18        | 5.20      | -        |        | ✓          | -    | -      |                                                                                          |
| SP7000 | 5.20        | -         | 4.40     |        | ✓          | -    | -      |                                                                                          |
| SP7000 | 6.90        | -         | 4.42     |        | ✓          | -    | -      |                                                                                          |
| SP7000 | 7.14.2      | -         | 5.2.9    |        | ✓          | ✓    | -      |                                                                                          |

### Unsupported:
* MG6250 - unsupported protocol


***

Note: For simpler table editing you can use https://www.tablesgenerator.com/markdown_tables


## Features that depend on firmware versions:
### EVO192 and EVOHD:
* PGM control requires fw 4.75+
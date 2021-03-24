## User reported hardware attempted with PAI
### Supported
Other versions may be also supported. If PAI works with your panel, and the model-version combination is not listed, please edit the next table.

|  Panel |         | IP module |           | Connection |      |        |              Notes             |
|:------:|:-------:|:---------:|:---------:|:----------:|:----:|:------:|:------------------------------:|
|  Model | Version |   IP100   | IP150     |    Local   | SWAN | Serial |                                |
| EVOHD  | 4.50    | -         | -         | -          | -    | no     | Did not work with ESP32. [#198](https://github.com/ParadoxAlarmInterface/pai/issues/198)  |
| EVOHD  | 6.91    | -         | 4.42      | ✓          | -    | -      |                                |
| EVOHD  | 7.30    | 4.42.002  | IP150     | ✓          | -    | -      |                                |
| EVOHD  | 7.31    | 5.20.0E   | -         | ✓          | -    | ✓      | Works with Serial via ESP32    |
| EVO48  | 2.20    | 5.10      | -         | ✓          | -    | -      |                                |
| EVO192 | 2.66    | 5.10.06   | -         | ✓          | -    | -      |                                |
| EVO192 | 6.86    | 5.10      | -         | ✓          | -    | -      |                                |
| EVO192 | 6.90    | 5.1.0     | 1.32      | ✓          | -    | ✓      |                                |
| EVO192 | 7.30    | -         | 1.32      | ✓          | -    | ✓      |                                |
| EVO192 | 7.30    | -         | 4.42      | ✓          | -    | -      |                                |
| MG5050 | 4.96    | -         | 4.40      | ✓          | -    | ✓      |                                |
| MG5050 | 4.72    | -         | 1.26      | ✓          | -    | -      |                                |
| MG5050 | 6.80    | -         | 4.40      | ✓          | -    | ✓      |                                |
| MG5050 | 7.01.012| -         | 5.02.019  | ✓          | -    | -      | IP150 firmware 5.02.019        |
| SP4000 | 5.20    | -         | 5.02.019  | ✓          | -    | ✓      |                                |
| SP5500 | 2.40.1  | -         | -         | -          | -    | ✓      |                                |
| SP5500 | 4.54    | -         | 4.42      | ✓          | -    | -      |                                |
| SP5500 | 7.14.002| -         | -         | ✓          | -    | ✓      | USB to Serial via FTDI         |
| SP6000 | 4.15    | -         | -         | -          | -    | ✓      |                                |
| SP6000 | 6.80    | -         | 5.02      | ✓          | ✓    | -      | More stable with SWAN disabled |
| SP7000 | 5.18    | 5.20      | -         | ✓          | -    | -      |                                |
| SP7000 | 5.20    | -         | 4.40      | ✓          | -    | -      |                                |
| SP7000 | 6.90    | -         | 4.42      | ✓          | -    | -      |                                |
| SP7000 | 7.14.2  | -         | 5.2.9     | ✓          | ✓    | -      |                                |

### Unsupported:
* MG6250 - unsupported protocol


***

Note: For simpler table editing you can use https://www.tablesgenerator.com/markdown_tables
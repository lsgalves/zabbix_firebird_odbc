# Firebird by ODBC

## Overview

For Zabbix version: 5.4 and higher
The template is developed for monitoring Firebird via ODBC.

This template was tested on:

- Firebird, version 2.5, 3

## Setup

> See [Zabbix template operation](https://www.zabbix.com/documentation/6.0/manual/config/templates_out_of_the_box/odbc_checks) for basic instructions.

1. Set the dsn in host macros ({$Firebird.DSN}).
  Do not forget to install Firebird ODBC driver on Zabbix server or Zabbix proxy.
  See Firebird documentation for instructions: https://firebirdsql.org/file/documentation/html/en/refdocs/fbodbc20/firebird-odbc-driver-20-manual.html


## Zabbix configuration

No specific Zabbix configuration is required.

## Author

Leonardo Galves

### Macros used

|Name|Description|Default|
|----|-----------|-------|
|{$FIREBIRD.CONNECTION.MAX} |<p>The maximum connections - for the trigger expression.</p> |`60` |
|{$FIREBIRD.DSN} |<p>System data source name.</p> |`<Put your DSN here>` |
|{$FIREBIRD.IDLE_CONNECTION.WARN} |<p>The maximum idle connections stalled to warning - for the trigger expression.</p> |`30` |
|{$FIREBIRD.STALLED_STATEMENTS.MAX} |<p>The maximum stalled statements - for the trigger expression.</p> |`10` |

## Template links

There are no template links in this template.

## Discovery rules

There are no discovery rules in this template.

## Items collected

|Group|Name|Description|Type|Key and additional info|
|-----|----|-----------|----|---------------------|
|Zabbix raw items |Firebird: Get performance counters |<p>The item gets server global status information.</p> |ODBC |db.odbc.get[get_status_variables,"{$FIREBIRD.DSN}"]<p>**Expression**:</p>`The text is too long. Please see the template.` |
|Firebird |Firebird: Version |<p>Firebird Server version.</p> |DEPENDENT |firebird.version<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Version')].CNTR_VALUE.first()`</p><p>- DISCARD_UNCHANGED_HEARTBEAT: `1d`</p> |
|Firebird |Firebird: Total active transactions |<p>The number of currently active transactions of all types.</p> |DEPENDENT |firebird.transactions<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Active Transactions')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Stalled statements |<p>The number of stalled statements.</p> |DEPENDENT |firebird.stalled_statements<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Stalled Statements')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Running statements |<p>The number of running statements.</p> |DEPENDENT |firebird.running_statements<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Running Statements')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Page writes |<p>Number of page writes in the database.</p> |DEPENDENT |firebird.page_writes<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Page Writes')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Pages allocated externally |<p>The number of external pages allocated.</p> |DEPENDENT |firebird.external_pages<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Pages Allocated Externally')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Page fetches |<p>Number of page fetches in the database.</p> |DEPENDENT |firebird.page_fetches<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Page Fetches')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Open connections |<p>Number of currently open connections.</p> |DEPENDENT |firebird.open_connections<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Open Connections')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Idle connections |<p>Number of currently idle connections.</p> |DEPENDENT |firebird.idle_connections<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='IDLE Connections')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Database state |<p>Database state:</p><p>0 - Online;</p><p>1 - Multi-user shutdown;</p><p>2 - Single-user shutdown;</p><p>3 - Full-shutdown;</p> |DEPENDENT |firebird.database_state<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Database State')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Database page size |<p>The number of pages configured to use for the database.</p><p>Allowed page sizes: 4096, 8192, 16384 or 32768 (>= Firebird 4)</p> |DEPENDENT |firebird.page_size<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Database Page Size')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Database memory usage |<p>Database memory usage in bytes.</p> |DEPENDENT |firebird.memory_usage<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Database Memory Used')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Database creation date |<p>The date and time when the database was created or was last restored.</p> |DEPENDENT |firebird.database_date<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Database Creation Date')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Database allocated pages |<p>The number of allocated pages in the database.</p> |DEPENDENT |firebird.allocated_pages<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Database Allocated Pages')].CNTR_VALUE.first()`</p> |
|Firebird |Firebird: Backup state |<p>Backup state:</p><p>0 - Normal;</p><p>1 - Stalled;</p><p>2 - Merge;</p> |DEPENDENT |firebird.backup_state<p>**Preprocessing**:</p><p>- JSONPATH: `$[?(@.COUNTER_NAME=='Backup State')].CNTR_VALUE.first()`</p><p>- REPLACE: `0` `Normal`</p><p>- REPLACE: `1` `Stalled`</p><p>- REPLACE: `2` `Merge`</p> |

## Triggers

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----|----|----|
|Firebird: Database has restored |<p>Firebird database has restored.</p> |`last(/Firebird by ODBC/firebird.database_date,#1)<>last(/Firebird by ODBC/firebird.database_date,#2) and length(last(/Firebird by ODBC/firebird.database_date))>0` |INFO |<p>Manual close: YES</p> |
|Firebird: Database is down |<p>The Firebird Database is shutdown. (The current state is {ITEM.VALUE}):</p><p>0 - The database is online;</p><p>1 - Multi-user shutdown;</p><p>2 - Single-user shutdown;</p><p>3 - Full shutdown.</p> |`last(/Firebird by ODBC/firebird.database_state)<>0` |HIGH | |
|Firebird: Number of connections too high (over {$FIREBIRD.CONNECTION.MAX} for 5m) |<p>Too high number of connections.</p> |`min(/Firebird by ODBC/firebird.open_connections,5m)>{$FIREBIRD.CONNECTION.MAX}` |AVERAGE | |
|Firebird: Number of idle connections too high (over {$FIREBIRD.IDLE_CONNECTION.WARN} for 5m) |<p>Too high number of idle connections.</p> |`min(/Firebird by ODBC/firebird.idle_connections,5m)>{$FIREBIRD.IDLE_CONNECTION.WARN}` |WARNING | |
|Firebird: Number of stalled statements too high (over {$FIREBIRD.STALLED_STATEMENTS.MAX} for 5m) |<p>Too high number of stalled statements.</p> |`min(/Firebird by ODBC/firebird.stalled_statements,5m)>{$FIREBIRD.STALLED_STATEMENTS.MAX}` |AVERAGE | |
|Firebird: Physical Backup (nBackup) is running (the state is: {ITEM.VALUE}) |<p>Firebird database is running nBackup. Backup state:</p><p>0 - Normal;</p><p>1 - Stalled;</p><p>2 - Merge;</p> |`last(/Firebird by ODBC/firebird.backup_state,#1)<>last(/Firebird by ODBC/firebird.backup_state,#2)` |INFO |<p>Manual close: YES</p> |
|Firebird: Version has changed (new version value received: {ITEM.VALUE}) |<p>Firebird version has changed. Ack to close.</p> |`last(/Firebird by ODBC/firebird.version,#1)<>last(/Firebird by ODBC/firebird.version,#2) and length(last(/Firebird by ODBC/firebird.version))>0` |INFO |<p>Manual close: YES</p> |

## References

https://firebirdsql.org/file/documentation/chunk/en/refdocs/fblangref25/fblangref25-appx05-montables.html

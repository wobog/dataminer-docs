---
uid: GetMaskedAlarmsForViewSortedV2
---

# GetMaskedAlarmsForViewSortedV2

Use this method to retrieve a specific number of masked view alarms, as well as the alarm cache status.

<!-- Available from DataMiner 10.0.7 onwards. -->

> [!NOTE]
> Using this method, you can e.g. request masked view alarms in batches in order to minimize loading time.

## Input

| Item       | Format  | Description                                                                         |
|------------|---------|-------------------------------------------------------------------------------------|
| connection | String  | The connection ID. See [ConnectApp](xref:ConnectApp).                               |
| viewID     | Integer | The view ID.                                                                        |
| index      | Integer | The point from which to start returning alarms.                                     |
| count      | Integer | The number of alarms to be returned.                                                |
| orderBy    | String  | The Alarm Console column(s) by which to order the alarms (separated by semicolons). |

## Output

| Item | Format | Description |
|--|--|--|
| GetMaskedAlarmsForViewSortedV2Result | Array of [DMAAlarm](xref:DMAAlarm) | The masked alarms for the specified view, sorted as requested, as well as the alarm cache status. |

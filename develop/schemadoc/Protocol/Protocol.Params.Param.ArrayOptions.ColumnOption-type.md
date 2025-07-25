---
uid: Protocol.Params.Param.ArrayOptions.ColumnOption-type
---

# type attribute

Specifies the column type.

## Content Type

[EnumColumnOptionType](xref:Protocol-EnumColumnOptionType)

## Parent

[ColumnOption](xref:Protocol.Params.Param.ArrayOptions.ColumnOption)

## Remarks

In the type attribute, you can specify one of the following options:

### autoincrement

Used to automatically create a unique value. Only applicable for primary key columns.

> [!NOTE]
> The use of this type is **no longer supported** for logger tables for the following reasons:
>
> - MySQL supports auto-increment, but Cassandra does not have this functionality by default. DataMiner supports auto-increment on a Cassandra database by walking over the table to determine the maximum ID to determine the next value. However, it is not advised to use this for logger tables as a logger table can be very large.
> - It is not supported with an indexing database or [Storage as a Service (STaaS)](xref:STaaS).
> - In case multiple elements could write to the same logger table, it is difficult for DataMiner to determine which element is responsible for doing the housekeeping of maintaining the PK value to be used when inserting a new row.
>
> If auto-increment behavior is needed for logger tables, it is better to implement it in the protocol itself. If you still need a unique PK in a logger table, the recommendation is to use a GUID (Note: The indexing database does not need a PK).

### concatenation

Allows you to join multiple columns into one.

The expected format of the value attribute is a comma-separated list of the column indexes to be concatenated. Via the "separator" option, you can specify a separator to be used.

In the following example, columns 1, 2, 3 and 4 are joined using a dot ('.') as separator:

```xml
<ColumnOption idx="0" pid="100" type="concatenation" value="1,2,3,4" options=";separator=."/>
```

> [!NOTE]
> This column type is only supported for SNMP tables.

### custom

Indicates that the content of the column will be managed in the protocol.

> [!NOTE]
> The usage of retrieved is preferred over custom, since this allows, among other things, to set a column at once.

### displaykey

<!-- RN 6343 -->

If you specify this option, the SLElement process will automatically fill this column with the display keys of the rows.

The display key is composed either via NamingFormat or via the naming option (or displayColumn). If neither is defined, the index will be used. Note that trending/alarming on this column is not possible.

> [!NOTE]
>
> - The *displaykey* column is there for the end user only. Therefore, the column has no reason to live within SLProtocol, which will fully rely on the primary keys instead for its logic. It only lives in SLElement, which is responsible for building the value. This has the following implications:
>   - The *displaykey* column should alway be the last column defined in the table. Otherwise, as the *displaykey* column does not exist within SLProtocol, all subsequent columns would have a confusing shifted IDX.
>   - Trending/alarming on this column is not possible.
>   - Columns of type "displayKey" cannot be saved.
>   - It is not possible to obtain the values from this column using calls that obtain information from the SLProtocol process, e.g. a NotifyProtocol NT_GET_TABLE_COLUMNS (321) call.
>   - It is also not possible to perform sets on columns of type "displaykey".
> - When the display key is created with multiple table columns through foreign key relations, the full path from parent to child has to be defined in the relations.

### index

Used when retrieving SNMP or WMI tables. It contains the row number.

### retrieved

This type is used in the following situations:

- To populate a column with a call to DataMiner using:
  - NotifyProtocol NT_FILL_ARRAY (193) (see [NT_FILL_ARRAY (193)](xref:NT_FILL_ARRAY))
  - NotifyProtocol NT_FILL_ARRAY_NO_DELETE (194) (see [NT_FILL_ARRAY_NO_DELETE (194)](xref:NT_FILL_ARRAY_NO_DELETE))
  - NotifyProtocol NT_FILL_ARRAY_WITH_COLUMN (220) (see [NT_FILL_ARRAY_WITH_COLUMN (220)](xref:NT_FILL_ARRAY_WITH_COLUMN))
  - NotifyProtocol NT_FILL_ARRAY_WITH_COLUMN_ONLY_UPDATES (336) (see [NT_FILL_ARRAY_WITH_COLUMN_ONLY_UPDATES (336)](xref:NT_FILL_ARRAY_WITH_COLUMN_ONLY_UPDATES))
  - A wrapper method such as FillArray (SLProtocol, QActionTable), FillArrayNoDelete (SLProtocol, QActionTable).
- To perform a merge action
- To perform an aggregate action
- To perform a swap action

### snmp

Represents an SNMP column

> [!IMPORTANT]
> From DataMiner 10.3.8, 10.3.0 [CU5], and 10.2.0 [CU17] onwards (RN 36559), in an SNMP table, columns of type "retrieved" can be placed in between columns of type "snmp" (provided the primary key column is a column of type "snmp" and not a column of type "retrieved"). In earlier versions of DataMiner, all columns of type "snmp" have to be placed before any group of type "retrieved".

### state

The state column is a column that can be used when retrieving SNMP or WMI tables, or tables consisting of retrieved columns. When, for example, a row is removed between two get operations, this cell will be set to "deleted".

Possible values for this cell are:

- Updated (1)
- Equal (2)
- New (3)
- Deleted (4)
- Recreated (5)

With this column type, you can extend the options attribute with the value "delete". If you do so, deleted rows will be automatically removed from the dynamic table. When you do not specify the "delete" option, deleted rows will stay in the table.

Example:

```xml
<ColumnOption idx="0" pid="100" type="state" value="" options=";delete"/>
```

> [!NOTE]
> In case of WMI tables (updated via WMI actions), by default, data in "retrieved" columns that are present in the WMI table gets cleared when the table is updated after receiving the WMI data.
>
> To ensure this does not happen, you can add an additional state column. This will prevent the retrieved column data from being cleared on every WMI polling update.
>
>   ```xml
>   <Param id="1000" trending="false">
>     <Name>networkInterfaces</Name>
>     <Description>Network Interfaces</Description>
>     <Type id="56;57;58;59;60;61;62">array</Type>
>     <ArrayOptions index="0">
>        <ColumnOption idx="7" pid="63" type="retrieved" options="" />
>        <ColumnOption idx="8" pid="64" type="state" options=";delete" />
>     </ArrayOptions>
>     <Interprete>
>        <RawType>other</RawType>
>        <Type>double</Type>
>        <LengthType>next param</LengthType>
>     </Interprete>
>     ...
> </Param>
>   ```

### viewTableKey

<!-- RN 13582 -->

If a direct view column containing primary keys is of type viewTableKey, a DataMinerAgentID_ElementID_ prefix will be added to those primary keys, referring to the source element that supplied the data.

This allows the retrieval of the correct data from a direct view table if the tables in the source elements do not have unique primary keys.

Example:

```xml
<ArrayOptions index="1" displayColumn="1" partial="true" options=";volatile;view=1500,remoteId;
directView=5361;filterChange=19901-1501,19902-1502,19903-1503,19904-1504,19905-1505">
   <ColumnOption idx="0" pid="19901" type="retrieved" options=";disableHeaderSum;disableHistogram;disableHeatmap"/>
   <ColumnOption idx="1" pid="19906" type="viewTableKey" options=";disableHeaderSum;disableHistogram;disableHeatmap"/>
   <ColumnOption idx="2" pid="19902" type="retrieved" options=";disableHeaderSum;disableHistogram;disableHeatmap"/>
   <ColumnOption idx="3" pid="19903" type="retrieved" options=";disableHeaderSum;disableHistogram;disableHeatmap"/>
   <ColumnOption idx="4" pid="19904" type="retrieved" options=";disableHeaderSum;disableHistogram;disableHeatmap"/>
   <ColumnOption idx="5" pid="19905" type="retrieved" options=";disableHeaderSum;disableHistogram;disableHeatmap"/>
</ArrayOptions>
```

> [!NOTE]
>
> - This column type should only be used if there can be duplicate primary keys across all the source elements from which the data originates.
> - If trend data is requested using primary keys from a column of type "viewTableKey", no data will be returned.

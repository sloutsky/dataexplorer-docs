---
title: .create external table mapping - Azure Data Explorer
description: Learn how to create an external table mapping for Azure Blob Storage or Azure Data Lake external tables.
ms.topic: reference
ms.date: 04/09/2023
---

# .create external table mapping

Create a new external table data mapping. For more information, see [Data mappings](./mappings.md).

## Syntax

`.create` `external` `table` *ExternalTableName* `mapping` *MappingName* *MappingInJsonFormat*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*ExternalTableName*|The name of the external table to which to assign the mapping.|
|*MappingName*|The name of the mapping.|
|*MappingInJsonFormat*|The mapping definition in JSON format.|

## Returns

Returns a table containing the new mapping name, mapping kind, and mapping definition.

## Example

```kusto
.create external table MyExternalTable mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**Output**

| Name | Kind | Mapping |
|--|--|--|
| mapping1 | JSON | [{"ColumnName":"rownumber","Properties":{"Path":"$.rownumber"}},{"ColumnName":"rowguid","Properties":{"Path":"$.rowguid"}}] |

---
title: .alter column-docstrings - Azure Data Explorer
description: Learn how to use the .alter column-docstrings command to set the `DocString` property of one or more columns of the specified table.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 04/20/2023
---
# .alter column-docstrings

Sets the `DocString` property of one or more columns of the specified table. Columns not explicitly set will have this property removed.

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter` `table` *TableName* `column-docstrings` `(` *Col1* `:` *DocString1* [`,` *Col2* `:` *DocString2*]... `)`

## Parameters

| Name | Type | Required | Description |
|--|--|--|--|
| *DocString* | string | &check; | Free text that you can attach to a table/function/column to describe the entity. This string is presented in various UX settings next to the entity names.|
| *TableName* | string | &check; | The name of the table on which the operation is performed.|
| *Col* | string | &check; | The column on which the operation is performed.|

## Example

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

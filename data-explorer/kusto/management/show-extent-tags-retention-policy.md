---
title: Show extent tags retention policy management - Azure Data Explorer
description: This article describes the show extent tags retention policy command in Azure Data Explorer.
ms.reviewer: yonil
ms.topic: reference
ms.date: 05/08/2023
---
# .show extent tags retention policy

Shows a table-level or database-level extent tags retention policy. For more information, see [extent tags retention policy](extent-tags-retention-policy.md).

## Permissions

You must have at least Database User, Database Viewer, or Database Monitor permissions to run these commands. For more information, see [role-based access control](access-control/role-based-access-control.md).

## Syntax

`.show`  ( `table` | `database` ) *EntityName* `policy` `extent_tags_retention`

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*EntityName*|string|&check;|The table or database name for which to show the extent tags retention policy.|

## Returns

Returns a JSON representation of the policy.

## Example

Show the extent tags retention policy for the database named `MyDatabase`:

```kusto
.show database MyDatabase policy extent_tags_retention
```

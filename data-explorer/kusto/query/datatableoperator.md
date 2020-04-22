---
title: opérateur de datatable - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de datatable dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 182f8030e3263ee5bf6bee4ca7444b0d5596e7d6
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765386"
---
# <a name="datatable-operator"></a>opérateur DataTable

Retourne un tableau dont le schéma et les valeurs sont définis dans la requête elle-même.

Notez que cet opérateur ne dispose pas d’un pipeline d’entrée.

**Syntaxe**

`datatable``(` *ColumnName* `:` *ColumnType* [...]`,` `)` *ScalarValue* `,` *ScalarValue* ScalarValue [ ScalarValue ...] `[``]`

**Arguments**

::: zone pivot="azuredataexplorer"

* *ColumnName*, *ColumnType*: Ceux-ci définissent le schéma de la table. La Syntaxe utilisée est exactement la même que la syntaxe utilisée lors de la définition d’une table (voir [.créer la table).](../management/create-table-command.md)
* *ScalarValue*: Une valeur scalaire constante à insérer dans la table. Le nombre de valeurs doit être un multiple integer des colonnes dans le tableau, et la valeur *n*'th doit avoir un type qui correspond à colonne *n* % *NumColumns*.

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*, *ColumnType*: Ceux-ci définissent le schéma de la table.
* *ScalarValue*: Une valeur scalaire constante à insérer dans la table. Le nombre de valeurs doit être un multiple integer des colonnes dans le tableau, et la valeur *n*'th doit avoir un type qui correspond à colonne *n* % *NumColumns*.

::: zone-end

**Retourne**

Cet opérateur renvoie un tableau de données du schéma et des données donnés.

**Exemple**

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```

---
title: opérateur DataTable-Azure Explorateur de données
description: Cet article décrit l’opérateur DataTable dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: cc62fcd04ad6a528836cc60a5c336ed4e8d1aecf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348562"
---
# <a name="datatable-operator"></a>opérateur DataTable

Retourne une table dont le schéma et les valeurs sont définis dans la requête elle-même.

> [!NOTE]
> Cet opérateur n’a pas d’entrée de pipeline.

## <a name="syntax"></a>Syntaxe

`datatable``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *ScalarValue* [ `,` *ScalarValue* ...]`]`

## <a name="arguments"></a>Arguments

::: zone pivot="azuredataexplorer"

* *ColumnName*, *ColumnType*: ces arguments définissent le schéma de la table. Les arguments utilisent la même syntaxe que celle utilisée lors de la définition d’une table.
  Pour plus d’informations, consultez [. Create table](../management/create-table-command.md)).
* *ScalarValue*: valeur scalaire constante à insérer dans la table. Le nombre de valeurs doit être un entier multiple des colonnes de la table. La *énième*valeur doit avoir un type qui correspond à la colonne *n*  %  *NumColumns*.

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*, *ColumnType*: ces arguments définissent le schéma de la table.
* *ScalarValue*: valeur scalaire constante à insérer dans la table. Le nombre de valeurs doit être un entier multiple des colonnes de la table. La *énième*valeur doit avoir un type qui correspond à la colonne *n*  %  *NumColumns*.

::: zone-end

## <a name="returns"></a>Retourne

Cet opérateur retourne une table de données du schéma et des données donnés.

## <a name="example"></a>Exemple

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```

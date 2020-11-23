---
title: Valeurs NULL-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les valeurs NULL dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ac8852adb5138bffe10a4726470b1c53d74cec1b
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324378"
---
# <a name="null-values"></a>Valeurs Null

Tous les types de données scalaires dans Kusto ont une valeur spéciale qui représente une valeur manquante.
Cette valeur est appelée **valeur null** ou simplement **null**.

## <a name="null-literals"></a>Littéraux Null

La valeur null d’un type scalaire `T` est représentée dans le langage de requête par le littéral null `T(null)` .
Ainsi, le code suivant retourne une seule ligne de valeurs NULL :

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> Notez qu’actuellement, le `string` type ne prend pas en charge les valeurs NULL.

## <a name="comparing-null-to-something"></a>Comparaison de la valeur null à un événement

La valeur NULL n’est pas comparée à une autre valeur du type de données, y compris lui-même. (Autrement dit, `null == null` a la valeur false.) Pour déterminer si une valeur est la valeur null, utilisez la fonction [IsNull ()](../isnullfunction.md) et la fonction [IsNotNull ()](../isnotnullfunction.md) .

## <a name="binary-operations-on-null"></a>Opérations binaires sur null

En général, NULL se comporte de manière « rémanente » autour des opérateurs binaires ; une opération binaire entre une valeur null et toute autre valeur (y compris une autre valeur null) produit une valeur null.

## <a name="data-ingestion-and-null-values"></a>Ingestion de données et valeurs null

Pour la plupart des types de données, une valeur manquante dans la source de données génère une valeur null dans la cellule de table correspondante. Une exception à qui sont des colonnes de type et d’ingestion de type `string` CSV, où une valeur manquante produit une chaîne vide.
Par exemple, si nous avons : 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

Ensuite :

|a     |b     |IsNull (a)|IsEmpty (a)|strlen (a)|IsNull (b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* Si vous exécutez la requête ci-dessus dans Kusto. Explorer, toutes les `true` valeurs s’affichent sous la forme `1` , et toutes les `false` valeurs s’affichent sous la forme `0` .

* Kusto n’offre aucun moyen de contraindre la colonne d’une table à avoir des valeurs null (en d’autres termes, il n’y a pas d’équivalent à la contrainte de SQL `NOT NULL` ).

::: zone-end

::: zone pivot="azuremonitor"

* Kusto n’offre aucun moyen de contraindre la colonne d’une table à avoir des valeurs null (en d’autres termes, il n’y a pas d’équivalent à la contrainte de SQL `NOT NULL` ).

::: zone-end
---
title: Valeurs nulles - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Null Values in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c3a48fdfca855a1ff2f848d4ed97d8162e97b931
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765945"
---
# <a name="null-values"></a>Valeurs Null

Tous les types de données scalaires à Kusto ont une valeur particulière qui représente une valeur manquante.
Cette valeur est appelée la **valeur nulle**, ou tout simplement **nulle**.

## <a name="null-literals"></a>Null littéralements

La valeur nulle d’un `T` type scalaire est représentée dans `T(null)`la langue de requête par le littéral nul .
Ainsi, les retours suivants d’une seule rangée pleine de nuls:

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> Veuillez noter qu’actuellement le `string` type ne prend pas en charge les valeurs nulles.

## <a name="comparing-null-to-something"></a>Comparaison nulle à quelque chose

La valeur nulle ne se compare pas à toute autre valeur du type de données, y compris elle-même. (C’est-à-dire, `null == null` est faux.) Pour déterminer si une certaine valeur est la valeur nulle, utilisez la fonction [isnull()](../isnullfunction.md) et la fonction [isnotnull().](../isnotnullfunction.md)

## <a name="binary-operations-on-null"></a>Opérations binaires sur nul

En général, null se comporte d’une manière "collante" autour des opérateurs binaires; une opération binaire entre une valeur nulle et toute autre valeur (y compris une autre valeur nulle) produit une valeur nulle.

## <a name="data-ingestion-and-null-values"></a>Ingestion de données et valeurs nulles

Pour la plupart des types de données, une valeur manquante dans la source de données produit une valeur nulle dans la cellule de table correspondante. Une exception à cela `string` sont des colonnes de type et cSV-comme l’ingestion, où une valeur manquante produit une chaîne vide.
Donc, par exemple, si nous avons: 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

Ensuite :

|a     |b     |isnull(a)|isempty(a)|strlen(a)|isnull(b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* Si vous exécutez la requête ci-dessus `true` dans Kusto.Explorer, toutes les valeurs seront démantées comme `1`, et toutes les `false` valeurs seront affichées comme `0`.

::: zone-end

* Kusto n’offre pas un moyen de contraindre la colonne d’une table à avoir `NOT NULL` des valeurs nulles (en d’autres termes, il n’y a pas d’équivalent à la contrainte de SQL).

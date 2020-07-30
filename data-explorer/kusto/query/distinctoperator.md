---
title: distinct, opérateur-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur distinct dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 233d3fdb0e25720b860a0c11515daec7c597dadd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348341"
---
# <a name="distinct-operator"></a>opérateur distinct

Produit une table avec la combinaison distincte des colonnes fournies dans la table d’entrée. 

```kusto
T | distinct Column1, Column2
```

Produit une table avec la combinaison distincte de toutes les colonnes de la table d’entrée.

```kusto
T | distinct *
```

## <a name="example"></a>Exemple

Affiche la combinaison distincte des fruits et des prix.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Distinct":::

**Remarques**

* Contrairement `summarize by ...` à, l' `distinct` opérateur prend en charge l’utilisation d’un astérisque ( `*` ) comme clé de groupe, ce qui facilite son utilisation pour les tables larges.
* Si les clés Group by sont de hautes cardinales, l’utilisation `summarize by ...` de avec la [stratégie de lecture aléatoire](shufflequery.md) peut être utile.
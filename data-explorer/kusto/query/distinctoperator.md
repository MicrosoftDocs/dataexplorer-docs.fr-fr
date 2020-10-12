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
ms.openlocfilehash: ffdd20c6d0c078cd3a7ecaa0d0fb2dac131ddda5
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941772"
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

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Deux tables. L’un possède des fournisseurs, des types de fruit et des prix, avec des combinaisons de prix de fruit répétées. La deuxième table répertorie uniquement les combinaisons uniques.":::

**Remarques**

* Contrairement `summarize by ...` à, l' `distinct` opérateur prend en charge l’utilisation d’un astérisque ( `*` ) comme clé de groupe, ce qui facilite son utilisation pour les tables larges.
* Si les clés Group by sont de hautes cardinales, l’utilisation `summarize by ...` de avec la [stratégie de lecture aléatoire](shufflequery.md) peut être utile.
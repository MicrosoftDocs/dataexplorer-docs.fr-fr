---
title: distinct, opérateur-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur distinct dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513163"
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
---
title: Opérateur distinct – Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’opérateur distinct dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513163"
---
# <a name="distinct-operator"></a>opérateur distinct

Produit une table avec la combinaison distincte des colonnes fournies de la table d’entrée. 

```kusto
T | distinct Column1, Column2
```

Produit une table avec la combinaison distincte de toutes les colonnes de la table d’entrée.

```kusto
T | distinct *
```

## <a name="example"></a>Exemple

Affiche la combinaison distincte de fruit et de prix.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Deux tables. L’une contient des fournisseurs, types de fruit et prix, avec des combinaisons de fruit-prix répétées. La deuxième table répertorie uniquement les combinaisons uniques.":::

**Remarques**

* Contrairement à l’opérateur `summarize by ...`, l’opérateur `distinct` prend en charge la fourniture d’un astérisque (`*`) comme clé de groupe, ce qui facilite son utilisation pour des tables de grande taille.
* Si les clés Grouper par présentent des cardinalités élevées, l’utilisation de `summarize by ...` avec la [stratégie de lecture aléatoire](shufflequery.md) peut être utile.
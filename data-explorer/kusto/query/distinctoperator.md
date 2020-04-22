---
title: opérateur distinct - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur distinct dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4287ca48d3fb5006e67a9266ea05287a7d8a06f6
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030364"
---
# <a name="distinct-operator"></a>opérateur distinct

Produit une table avec la combinaison distincte des colonnes fournies de la table d’entrée. 

```kusto
T | distinct Column1, Column2
```

Produit une table avec la combinaison distincte de toutes les colonnes dans la table d’entrée.

```kusto
T | distinct *
```

**Exemple**

Montre la combinaison distincte de fruits et de prix.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Distinctes":::

**Remarques**

* Contrairement `summarize by ...`à `distinct` , l’opérateur prend`*`en charge la fourniture d’un astérisque ( ) comme la clé du groupe, ce qui le rend plus facile à utiliser pour les tables larges.
* Si le groupe par les clés `summarize by ...` sont de hautes cardinalités, l’utilisation avec la [stratégie de mélange](shufflequery.md) pourrait être utile.
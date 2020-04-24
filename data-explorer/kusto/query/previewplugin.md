---
title: plugin aperçu - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le plugin de prévisualisation dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18bda0a4348d0c0eb2776bf124c57397f318a989
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510980"
---
# <a name="preview-plugin"></a>plugin aperçu

Renvoie une table avec jusqu’au nombre spécifié de lignes à partir de l’ensemble d’enregistrements d’entrée, et le nombre total d’enregistrements dans le nombre d’entrées établi.

```kusto
T | evaluate preview(50)
```

**Syntaxe**

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

**Retourne**

Le `preview` plugin renvoie deux tableaux de résultats :
* Un tableau avec jusqu’au nombre spécifié de lignes.
  Par exemple, la requête de l’échantillon ci-dessus est équivalente à l’exécution `T | take 50`.
* Un tableau avec une seule rangée/colonne, tenant le nombre d’enregistrements dans l’ensemble d’enregistrements d’entrée.
  Par exemple, la requête de l’échantillon ci-dessus est équivalente à l’exécution `T | count`.

**Conseils**

Si `evaluate` elle est précédée d’une source tabulaire qui comprend un filtre complexe, ou [`materialize`](materializefunction.md) un filtre qui fait référence à la plupart des colonnes de table source, préférez utiliser la fonction. Par exemple :

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```
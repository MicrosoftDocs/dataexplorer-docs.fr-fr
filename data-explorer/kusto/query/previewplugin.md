---
title: plug-in aperçu-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le plug-in version préliminaire dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 455ff1d4d3a42c09a39673028405d51b7acd1f5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251644"
---
# <a name="preview-plugin"></a>preview, plug-in

Retourne une table avec le nombre spécifié de lignes dans le jeu d’enregistrements d’entrée et le nombre total d’enregistrements dans le jeu d’enregistrements d’entrée.

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>Syntaxe

`T``|` `evaluate` `preview(` *NumberOfRows*`)`

## <a name="returns"></a>Retours

Le `preview` plug-in retourne deux tables de résultats :
* Table avec le nombre spécifié de lignes.
  Par exemple, l’exemple de requête ci-dessus équivaut à exécuter `T | take 50` .
* Table avec une seule ligne ou colonne, contenant le nombre d’enregistrements dans le jeu d’enregistrements d’entrée.
  Par exemple, l’exemple de requête ci-dessus équivaut à exécuter `T | count` .

> [!TIP]
> Si `evaluate` est précédé d’une source tabulaire qui comprend un filtre complexe ou d’un filtre qui fait référence à la plupart des colonnes de la table source, préférez utiliser la [`materialize`](materializefunction.md) fonction. Par exemple :

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```
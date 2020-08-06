---
title: plug-in aperçu-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le plug-in version préliminaire dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7c4ee69c4f82c25c6f4cf7d4b63ad9a659892a28
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802993"
---
# <a name="preview-plugin"></a>preview, plug-in

Retourne une table avec le nombre spécifié de lignes dans le jeu d’enregistrements d’entrée et le nombre total d’enregistrements dans le jeu d’enregistrements d’entrée.

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>Syntaxe

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

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
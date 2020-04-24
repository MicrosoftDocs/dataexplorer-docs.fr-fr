---
title: Le type de données guid - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données guid dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 382b2da0ab791cf3e2fea0e1c785ee42634aab07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509858"
---
# <a name="the-guid-data-type"></a>Le type de données guid

Le `guid` `uuid`type `uniqueid`de données (, ) représente une valeur unique à l’échelle mondiale de 128 bits.

> [!WARNING]
> À la rédaction de cet article, la prise en charge du type `guid` est incomplète.
> Le principal écart est l’absence d’un indice sur les colonnes de ce type, affectant la performance des requêtes qui se situent sur ce type.
> Nous recommandons vivement aux équipes d’utiliser des valeurs de type `string` à la place.

## <a name="guid-literals"></a>guid littérals

Pour représenter un type `guid`littéral, utilisez le format suivant :

```kusto
guid(74be27de-1e4e-49d9-b579-fe0b331d3642)
```

Le formulaire `guid(null)` spécial représente la [valeur nulle](null-values.md).
---
title: IsNotNull ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IsNotNull () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c198bd34161c683c6e5c6f1bde2990c0605122c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253275"
---
# <a name="isnotnull"></a>isnotnull()

Retourne `true` si l’argument n’a pas la valeur null.

## <a name="syntax"></a>Syntaxe

`isnotnull(`[*valeur*]`)`

`notnull(`[*valeur*] `)` -alias pour `isnotnull`

## <a name="example"></a>Exemple

```kusto
T | where isnotnull(PossiblyNull) | count
```

Notez qu’il existe d’autres façons d’obtenir cet effet :

```kusto
T | summarize count(PossiblyNull)
```
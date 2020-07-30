---
title: IsNotNull ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IsNotNull () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ff472919ecda9550e7e0bcd6b403c605d029bfb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347185"
---
# <a name="isnotnull"></a>isnotnull()

Retourne `true` si l’argument n’a pas la valeur null.

## <a name="syntax"></a>Syntaxe

`isnotnull(`[*valeur*]`)`

`notnull(`[*valeur*] `)` -alias pour`isnotnull`

## <a name="example"></a>Exemple

```kusto
T | where isnotnull(PossiblyNull) | count
```

Notez qu’il existe d’autres façons d’obtenir cet effet :

```kusto
T | summarize count(PossiblyNull)
```
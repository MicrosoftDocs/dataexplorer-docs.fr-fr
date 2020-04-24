---
title: isnotnull () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit isnotnull() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8be57f2f7484081ac316a8af6fea252a60f895a4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513445"
---
# <a name="isnotnull"></a>isnotnull ()

Revient `true` si l’argument n’est pas nul.

**Syntaxe**

`isnotnull(`[*valeur*]`)`

`notnull(`[*valeur*] `)` - alias pour`isnotnull`

**Exemple**

```kusto
T | where isnotnull(PossiblyNull) | count
```

Notez qu’il existe d’autres façons d’obtenir cet effet :

```kusto
T | summarize count(PossiblyNull)
```
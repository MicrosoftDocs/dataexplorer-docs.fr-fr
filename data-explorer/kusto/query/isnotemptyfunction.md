---
title: isnotempty () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’isnotempty () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14111be0fc0247dd151ef7454121e6b90a32ff0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513530"
---
# <a name="isnotempty"></a>isnotempty()

Revient `true` si l’argument n’est pas une chaîne vide ni un nul.

```kusto
isnotempty("") == false
```

**Syntaxe**

`isnotempty(`[*valeur*]`)`

`notempty(`[*valeur*] `)` -- alias de`isnotempty`
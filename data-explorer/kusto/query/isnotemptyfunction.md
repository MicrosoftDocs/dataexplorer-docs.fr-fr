---
title: IsNotEmpty ()-Azure Explorateur de données
description: Cet article décrit IsNotEmpty () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a21031b07df3a4fa654fd13eb3761618308337b
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717340"
---
# <a name="isnotempty"></a>isnotempty()

Retourne `true` si l’argument n’est pas une chaîne vide et n’a pas la valeur null.

```kusto
isnotempty("") == false
```

**Syntaxe**

`isnotempty(`[*valeur*]`)`

`notempty(`[*valeur*] `)` --alias de`isnotempty`

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
ms.openlocfilehash: 6d325861c7264c77535caf6df6c363ec801dd697
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347202"
---
# <a name="isnotempty"></a>isnotempty()

Retourne `true` si l’argument n’est pas une chaîne vide et n’a pas la valeur null.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Syntaxe

`isnotempty(`[*valeur*]`)`

`notempty(`[*valeur*] `)` --alias de`isnotempty`

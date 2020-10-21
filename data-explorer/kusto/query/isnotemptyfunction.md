---
title: IsNotEmpty ()-Azure Explorateur de données
description: Cet article décrit IsNotEmpty () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 42699b1d4a6f225213b2a2d55520cb019a983121
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253216"
---
# <a name="isnotempty"></a>isnotempty()

Retourne `true` si l’argument n’est pas une chaîne vide et n’a pas la valeur null.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Syntaxe

`isnotempty(`[*valeur*]`)`

`notempty(`[*valeur*] `)` --alias de `isnotempty`

## <a name="example"></a>Exemple

```kusto
T
| where isnotempty(fieldName)
| count
```

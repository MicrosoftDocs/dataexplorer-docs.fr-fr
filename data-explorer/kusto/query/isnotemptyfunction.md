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
ms.openlocfilehash: 9f7811c2668bd0bb2d6e3711800ee5d9a450b9ec
ms.sourcegitcommit: 487485c87706183a9824f695e5b56b0bc5ade048
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89056232"
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

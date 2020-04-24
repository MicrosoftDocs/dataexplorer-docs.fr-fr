---
title: toint() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit toint() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570e13dc816c8a7e6d5baa488912fd8def5d2883
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506101"
---
# <a name="toint"></a>toint()

Convertit l’entrée en représentation des numéros d’intégrer (signé 32 bits).

```kusto
toint("123") == 123
```

**Syntaxe**

`toint(`*Expr*`)`

**Arguments**

* *Expr*: Expression qui sera convertie en intégrant. 

**Retourne**

Si la conversion est réussie, le résultat sera un nombre d’intégraires.
Si la conversion n’est `null`pas réussie, le résultat sera .
 
*Remarque*: Préférez utiliser [int()](./scalar-data-types/int.md) lorsque c’est possible.
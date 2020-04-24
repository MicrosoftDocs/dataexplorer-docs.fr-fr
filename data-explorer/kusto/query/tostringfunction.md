---
title: tostring() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit tostring () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51aaf90b60653a648457dc00200168aec7fbefd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505880"
---
# <a name="tostring"></a>tostring()

Convertit l’entrée en une représentation des cordes.

```kusto
tostring(123) == "123"
```

**Syntaxe**

`tostring(`*Expr*`)`

**Arguments**

* *Expr*: Expression qui sera convertie en ficelle. 

**Retourne**

Si la valeur *Expr* n’est pas nulle résultat sera une représentation de chaîne d’Expr . *Expr*
Si la valeur *Expr* est nulle, le résultat sera la chaîne vide.
 
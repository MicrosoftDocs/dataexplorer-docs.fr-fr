---
title: ToGuid ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ToGuid () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d122833f4797c8503dd41cc8ba861554d6924338
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250376"
---
# <a name="toguid"></a>toguid()

Convertit l’entrée en [`guid`](./scalar-data-types/guid.md) représentation.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

> [!NOTE]
> Préférez l’utilisation [de GUID ()](./scalar-data-types/guid.md) dans la mesure du possible.

## <a name="syntax"></a>Syntaxe

`toguid(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera convertie en [`guid`](./scalar-data-types/guid.md) scalaire. 

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera un [`guid`](./scalar-data-types/guid.md) scalaire.
Si la conversion échoue, le résultat sera `null` .

---
title: ToDateTime ()-Azure Explorateur de données
description: Cet article décrit ToDateTime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b9248ddc76a4893cf1edd353c0fbd036c9a1e7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250540"
---
# <a name="todatetime"></a>todatetime()

Convertit l’entrée en valeur scalaire [DateTime](./scalar-data-types/datetime.md) .

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

## <a name="syntax"></a>Syntaxe

`todatetime(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera convertie en [DateTime](./scalar-data-types/datetime.md).

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera une valeur [DateTime](./scalar-data-types/datetime.md) .
Sinon, le résultat sera null.
 
> [!NOTE]
> Préférez l’utilisation [de DateTime ()](./scalar-data-types/datetime.md) dans la mesure du possible.

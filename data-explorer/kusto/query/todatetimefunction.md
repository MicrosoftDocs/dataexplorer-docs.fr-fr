---
title: ToDateTime ()-Azure Explorateur de données
description: Cet article décrit ToDateTime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6a991f9f64decbb3985830cf2611361d6c66db31
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350840"
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

## <a name="returns"></a>Retourne

Si la conversion réussit, le résultat sera une valeur [DateTime](./scalar-data-types/datetime.md) .
Sinon, le résultat sera null.
 
> [!NOTE]
> Préférez l’utilisation [de DateTime ()](./scalar-data-types/datetime.md) dans la mesure du possible.

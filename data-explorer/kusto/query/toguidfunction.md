---
title: ToGuid ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ToGuid () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 689ee6bf7b3fcb27dced20b06a9002659622902e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804132"
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

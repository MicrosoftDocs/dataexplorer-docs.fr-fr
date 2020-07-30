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
ms.openlocfilehash: a2eec6a582c4c8fc6cda6b3cf9a304f41ab48143
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350726"
---
# <a name="toguid"></a>toguid()

Convertit l’entrée en [`guid`](./scalar-data-types/guid.md) représentation.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

## <a name="syntax"></a>Syntaxe

`toguid(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera convertie en [`guid`](./scalar-data-types/guid.md) scalaire. 

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera un [`guid`](./scalar-data-types/guid.md) scalaire.
Si la conversion échoue, le résultat sera `null` .

*Remarque*: préférez l’utilisation [de GUID ()](./scalar-data-types/guid.md) dans la mesure du possible.
---
title: Not ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit not () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3224c48b963c051d0d65d27a2e64ec8317be30c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252180"
---
# <a name="not"></a>not()

Inverse la valeur de son `bool` argument.

```kusto
not(false) == true
```

## <a name="syntax"></a>Syntaxe

`not(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *expr*: `bool` expression à inverser.

## <a name="returns"></a>Retours

Retourne la valeur logique inversée de son `bool` argument.
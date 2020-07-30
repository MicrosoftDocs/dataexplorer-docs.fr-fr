---
title: Not ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit not () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fed2a55c8fa1c7689c087ccdeaa64ff576bea401
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346590"
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

## <a name="returns"></a>Retourne

Retourne la valeur logique inversée de son `bool` argument.
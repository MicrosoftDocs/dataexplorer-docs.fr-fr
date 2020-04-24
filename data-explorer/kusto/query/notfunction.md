---
title: pas() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit non() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 539e409a9e922afc390b097c863146b7fc30d7b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512034"
---
# <a name="not"></a>not()

Renverse la `bool` valeur de son argument.

```kusto
not(false) == true
```

**Syntaxe**

`not(`*Expr*`)`

**Arguments**

* *expr*: `bool` Une expression à renverser.

**Retourne**

Retourne la valeur logique `bool` inversée de son argument.
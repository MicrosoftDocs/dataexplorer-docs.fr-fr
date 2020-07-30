---
title: COALESCE ()-Azure Explorateur de données
description: Cet article décrit COALESCE () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 410a0c84a1bafdfa1900ef8e21bc0a91327b64c3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348868"
---
# <a name="coalesce"></a>coalesce()

Évalue une liste d’expressions et retourne la première expression non null (ou non vide pour String).

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

## <a name="syntax"></a>Syntaxe

`coalesce(`*expr_1* `, ` *expr_2* `,` ...)

## <a name="arguments"></a>Arguments

* *expr_i*: expression scalaire, à évaluer.
- Tous les arguments doivent être du même type.
- Le nombre maximal d’arguments 64 est pris en charge.


## <a name="returns"></a>Retourne

Valeur du premier *expr_i* dont la valeur n’est pas null (ou non vide pour les expressions de chaîne).

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|

---
title: COALESCE ()-Azure Explorateur de données
description: Cet article décrit COALESCE () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3eb5e533c2b4430f54909507e521912711c35811
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252639"
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


## <a name="returns"></a>Retours

Valeur du premier *expr_i* dont la valeur n’est pas null (ou non vide pour les expressions de chaîne).

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|

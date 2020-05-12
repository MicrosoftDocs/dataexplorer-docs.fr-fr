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
ms.openlocfilehash: ea57efe36fb86189d798e5f18fa3fe9470bfd634
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227534"
---
# <a name="coalesce"></a>coalesce()

Évalue une liste d’expressions et retourne la première expression non null (ou non vide pour String).

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**Syntaxe**

`coalesce(`*expr_1* `, ` *expr_2* `,` ...)

**Arguments**

* *expr_i*: expression scalaire, à évaluer.
- Tous les arguments doivent être du même type.
- Le nombre maximal d’arguments 64 est pris en charge.


**Retourne**

Valeur du premier *expr_i* dont la valeur n’est pas null (ou non vide pour les expressions de chaîne).

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|

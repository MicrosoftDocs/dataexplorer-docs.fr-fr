---
title: fusion () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la fusion () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ee2cd24f36007914fdc326e2863da148aec4406
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517134"
---
# <a name="coalesce"></a>coalesce()

Évalue une liste d’expressions et renvoie la première expression non nulle (ou non vide pour la chaîne).

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**Syntaxe**

`coalesce(`*expr_1**expr_2* `,` `, `

**Arguments**

* *expr_i*: Une expression scalaire, à évaluer.
- Tous les arguments doivent être du même type.
- Un maximum de 64 arguments est étayé.


**Retourne**

La valeur de la première *expr_i* dont la valeur n’est pas nulle (ou non vide pour les expressions à cordes).

**Exemple**

```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
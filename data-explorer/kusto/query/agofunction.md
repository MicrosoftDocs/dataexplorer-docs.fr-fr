---
title: il () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit il ya () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d155f1a1cd113f73acc779e6c2e73d5f537e71c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519089"
---
# <a name="ago"></a>ago()

Soustrait l’intervalle de temps donné de l’heure UTC actuelle.

```kusto
ago(1h)
ago(1d)
```

Comme `now()`, cette fonction peut être utilisée plusieurs fois dans une instruction, et l’heure UTC référencée est la même pour toutes les instanciations.

**Syntaxe**

`ago(`*a_timespan*`)`

**Arguments**

* *a_timespan* : intervalle à soustraire de l’heure UTC actuelle (`now()`).

**Retourne**

`now() - a_timespan`

**Exemple**

Toutes les lignes de l’horodatage de la dernière heure :

```kusto
T | where Timestamp > ago(1h)
```
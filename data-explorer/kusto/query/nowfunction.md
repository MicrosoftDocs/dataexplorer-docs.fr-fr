---
title: maintenant () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit maintenant () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c1a130cfbd45c35ff1ba26ed6c47986fc186c89c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512051"
---
# <a name="now"></a>now()

Retourne l’heure d’horloge UTC actuelle, compensée par une durée donnée.
Cette fonction peut être utilisée plusieurs fois dans une instruction, et l’heure référencée est la même pour toutes les instances.

```kusto
now()
now(-2d)
```

**Syntaxe**

`now(`[*compensé*]`)`

**Arguments**

* *décalage*: `timespan`A , ajouté à l’heure d’horloge UTC actuelle. Valeur par défaut : 0.

**Retourne**

Heure UTC actuelle, en tant que `datetime`.

`now()` + *Compenser* 

**Exemple**

Détermine l’intervalle depuis l’événement identifié par le prédicat :

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```
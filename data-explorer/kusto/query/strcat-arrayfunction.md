---
title: strcat_array ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit strcat_array () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06bdf7eb55b61cb974e803c9cdeb7b37b34ad87e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243786"
---
# <a name="strcat_array"></a>strcat_array()

Crée une chaîne concaténée de valeurs de tableau à l’aide du délimiteur spécifié.
    
## <a name="syntax"></a>Syntaxe

`strcat_array(`*tableau*, *délimiteur*`)`

## <a name="arguments"></a>Arguments

* *tableau*: `dynamic` valeur représentant un tableau de valeurs à concaténer.
* *délimiteur*: `string` valeur qui sera utilisée pour concaténer les valeurs dans le *tableau*

## <a name="returns"></a>Retours

Valeurs de tableau, concaténées en une chaîne unique.

## <a name="examples"></a>Exemples
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|
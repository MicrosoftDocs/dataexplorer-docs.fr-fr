---
title: strcat_array ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit strcat_array () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b8369d2e994477c9d01880632fac5f8a3ebaf6a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342595"
---
# <a name="strcat_array"></a>strcat_array()

Crée une chaîne concaténée de valeurs de tableau à l’aide du délimiteur spécifié.
    
## <a name="syntax"></a>Syntaxe

`strcat_array(`*tableau*, *délimiteur*`)`

## <a name="arguments"></a>Arguments

* *tableau*: `dynamic` valeur représentant un tableau de valeurs à concaténer.
* *délimiteur*: `string` valeur qui sera utilisée pour concaténer les valeurs dans le *tableau*

## <a name="returns"></a>Retourne

Valeurs de tableau, concaténées en une chaîne unique.

## <a name="examples"></a>Exemples
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|
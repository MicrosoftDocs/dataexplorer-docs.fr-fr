---
title: Il y a ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le précédent () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f3d58a7b29731125407eec7429967d1f90f5b7e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349769"
---
# <a name="ago"></a>ago()

Soustrait l’intervalle de temps donné de l’heure UTC actuelle.

```kusto
ago(1h)
ago(1d)
```

Comme `now()`, cette fonction peut être utilisée plusieurs fois dans une instruction, et l’heure UTC référencée est la même pour toutes les instanciations.

## <a name="syntax"></a>Syntaxe

`ago(`*a_timespan*`)`

## <a name="arguments"></a>Arguments

* *a_timespan* : intervalle à soustraire de l’heure UTC actuelle (`now()`).

## <a name="returns"></a>Retourne

`now() - a_timespan`

## <a name="example"></a>Exemple

Toutes les lignes de l’horodatage de la dernière heure :

```kusto
T | where Timestamp > ago(1h)
```
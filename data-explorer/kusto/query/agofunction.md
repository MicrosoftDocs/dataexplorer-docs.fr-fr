---
title: Il y a ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le précédent () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4457f7e400922221e139011508e13d4d7e6ee551
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247054"
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

## <a name="returns"></a>Retours

`now() - a_timespan`

## <a name="example"></a>Exemple

Toutes les lignes de l’horodatage de la dernière heure :

```kusto
T | where Timestamp > ago(1h)
```
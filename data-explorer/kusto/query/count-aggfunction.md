---
title: count() (fonction d’agrégation) – Azure Data Explorer | Microsoft Docs
description: Cet article décrit count() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513095"
---
# <a name="count-aggregation-function"></a>count() (fonction d’agrégation)

Retourne un nombre d’enregistrements par groupe de résumé (ou au total si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans un contexte d’agrégation à l’intérieur de l’opérateur [summarize](summarizeoperator.md).
* Utilisez la fonction d’agrégation [countif](countif-aggfunction.md) pour compter uniquement les enregistrements pour lesquels un prédicat retourne `true`.

## <a name="syntax"></a>Syntaxe

summarize `count()`

## <a name="returns"></a>Retours

Retourne un nombre d’enregistrements par groupe de résumé (ou au total si le résumé est effectué sans regroupement).

## <a name="example"></a>Exemple

Comptage d’événements dans les États commençant par la lettre `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|État|Nombre|
|---|---|
|WEST VIRGINIA|757|
|WYOMING|396|
|WASHINGTON|261|
|WISCONSIN|1850|

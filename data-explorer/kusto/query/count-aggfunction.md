---
title: Count () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Count () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513095"
---
# <a name="count-aggregation-function"></a>Count () (fonction d’agrégation)

Retourne le nombre d’enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)
* Utilisez la fonction d’agrégation [NB.si](countif-aggfunction.md) pour compter uniquement les enregistrements pour lesquels un prédicat est retourné `true` .

## <a name="syntax"></a>Syntaxe

résumer `count()`

## <a name="returns"></a>Retours

Retourne le nombre d’enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

## <a name="example"></a>Exemple

Comptage des événements dans les États commençant par letter `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|État|Count|
|---|---|
|WEST VIRGINIA|757|
|WYOMING|396|
|WASHINGTON|261|
|WISCONSIN|1850|

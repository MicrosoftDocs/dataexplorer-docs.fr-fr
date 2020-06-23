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
ms.openlocfilehash: 6a06be43773a356e903b25b2697e75b8342ed7f8
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133470"
---
# <a name="count-aggregation-function"></a>Count () (fonction d’agrégation)

Retourne le nombre d’enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)
* Utilisez la fonction d’agrégation [NB.si](countif-aggfunction.md) pour compter uniquement les enregistrements pour lesquels un prédicat est retourné `true` .

**Syntaxe**

résumer`count()`

**Retourne**

Retourne le nombre d’enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

**Exemple**

Comptage des événements dans les États commençant par letter `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|State|Count|
|---|---|
|WEST VIRGINIA|757|
|WYOMING|396|
|WASHINGTON|261|
|WISCONSIN|1850|

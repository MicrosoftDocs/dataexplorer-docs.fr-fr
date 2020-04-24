---
title: welch_test() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit welch_test() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9f4b3d86bf3d679fd4fdd320b394956c71d3e97
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504486"
---
# <a name="welch_test"></a>welch_test()

Calcule le p_value de la [fonction Welch-test](https://en.wikipedia.org/wiki/Welch%27s_t-test)

```kusto
// s1, s2 values are from https://en.wikipedia.org/wiki/Welch%27s_t-test
print
    s1 = dynamic([27.5, 21.0, 19.0, 23.6, 17.0, 17.9, 16.9, 20.1, 21.9, 22.6, 23.1, 19.6, 19.0, 21.7, 21.4]),
    s2 = dynamic([27.1, 22.0, 20.8, 23.4, 23.4, 23.5, 25.8, 22.0, 24.8, 20.2, 21.9, 22.1, 22.9, 20.5, 24.4])
| mv-expand s1 to typeof(double), s2 to typeof(double)
| summarize m1=avg(s1), v1=variance(s1), c1=count(), m2=avg(s2), v2=variance(s2), c2=count()
| extend pValue=welch_test(m1,v1,c1,m2,v2,c2)

// pValue = 0.021
```

**Syntaxe**

`welch_test(`*mean1*`, `*variance1*`, `*count1*`, `*mean2*`, `*variance2*`, `*count2*`)`

**Arguments**

* *mean1*: Expression qui représente la valeur moyenne (moyenne) de la 1ère série
* *variance1*: Expression qui représente la valeur de variance de la 1ère série
* *count1*: Expression qui représente le nombre de valeurs de la 1ère série
* *mean2*: Expression qui représente la valeur moyenne (moyenne) de la 2ème série
* *variance2*: Expression qui représente la valeur de variance de la 2ème série
* *count2*: Expression qui représente le nombre de valeurs de la 2ème série

**Retourne**

De [Wikipédia](https://en.wikipedia.org/wiki/Welch%27s_t-test):

Dans les statistiques, le t-test de Welch, ou test t d’écarts inégaux, est un test de localisation à deux échantillons qui est utilisé pour tester l’hypothèse selon laquelle deux populations ont des moyens égaux. Le t-test de Welch est une adaptation du t-test de l’étudiant, c’est-à-dire qu’il a été dérivé à l’aide du t-test de l’étudiant et qu’il est plus fiable lorsque les deux échantillons ont des écarts inégaux et des tailles d’échantillons inégales. Ces tests sont souvent appelés t-tests « non apprérés » ou « échantillons indépendants », car ils sont généralement appliqués lorsque les unités statistiques sous-jacentes aux deux échantillons comparés ne se chevauchent pas. Étant donné que le t-test de Welch a été moins populaire que le t-test de l’étudiant et peut être moins familier aux lecteurs, un nom plus informatif est «Welch’s inégalité variances t-test» ou «d’inégalités de variances t-test» pour la brièveté.
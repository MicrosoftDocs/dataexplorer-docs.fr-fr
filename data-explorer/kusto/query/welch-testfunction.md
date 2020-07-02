---
title: welch_test ()-Azure Explorateur de données
description: Cet article décrit welch_test () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1bb995874bf6ac552350c602c6d3742a08b1273b
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763682"
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

`welch_test(`*mean1* `, ` *variance1* `, ` *count1* `, ` *mean2* `, ` *variance2* `, ` *count2*`)`

**Arguments**

* *mean1*: expression qui représente la valeur moyenne (moyenne) de la première série
* *variance1*: expression qui représente la valeur de variance de la première série
* *count1*: expression qui représente le nombre de valeurs de la première série
* *mean2*: expression qui représente la moyenne de la valeur de la deuxième série
* *variance2*: expression qui représente la valeur de variance de la deuxième série
* *count2*: expression qui représente le nombre de valeurs dans la deuxième série

**Retourne**

À partir de [Wikipédia](https://en.wikipedia.org/wiki/Welch%27s_t-test):

Dans Statistics, t-test de Welch est un test d’emplacement à deux exemples qui est utilisé pour tester l’hypothèse selon laquelle deux populations ont des moyens équivalents. Le test t de Welch est une adaptation du test t de Student et est plus fiable lorsque les deux échantillons ont des variances inégales et des tailles d’échantillons inégales. Ces tests sont souvent appelés tests t « non couplés » ou « exemples indépendants ». Les tests sont généralement appliqués lorsque les unités statistiques sous-jacentes des deux échantillons comparés ne se chevauchent pas. Le test t de Welch est moins populaire que le test t de Student et peut être moins familier aux lecteurs. Le test est également appelé « Welch des variances inégales », ou « variances inégales t-test ».

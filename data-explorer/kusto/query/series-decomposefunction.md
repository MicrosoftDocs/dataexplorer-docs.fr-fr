---
title: series_decompose() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_decompose() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 375d63dd050840cd884fca0198511e71ac46f170
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663605"
---
# <a name="series_decompose"></a>series_decompose()

Applique une transformation de décomposition sur une série.  

Prend une expression contenant une série (tableau numérique dynamique) comme entrée et la décomposer en composants saisonniers, tendance et résiduels.
 
**Syntaxe**

`series_decompose(`*Série* `[,` *Seasonalité* `,` *Tendance* `,` *Test_points* `,` *Seasonality_threshold*`])`

**Arguments**

* *Série*: Cellule de tableau dynamique qui est un éventail de valeurs numériques, généralement la sortie résultante des opérateurs [de série](make-seriesoperator.md) ou [de make_list](makelist-aggfunction.md)
* *Saisonnalité*: Un integer contrôlant l’analyse saisonnière, contenant soit
    * -1 : auto-détection de la saisonnalité à [l’aide de series_periods_detect](series-periods-detectfunction.md) (par défaut).
    * période : integer positif précisant la période prévue en nombre de bacs. Par exemple, si la série est dans 1h poubelles, une période hebdomadaire est de 168 bacs.
    * 0: pas de saisonnalité (sauter l’extraction de ce composant).    
* *Tendance*: Une chaîne contrôlant l’analyse de tendance, contenant l’une des suivantes :
    * "avg": définir le composant tendance comme moyen(x) (par défaut)
    * "linefit": extraire le composant tendance à l’aide de la régression linéaire.
    * "aucun": pas de tendance, sauter l’extraction de ce composant.    
* *Test_points*: 0 (par défaut) ou integer positif, précisant le nombre de points à la fin de la série à exclure du processus d’apprentissage (régression). Ce paramètre doit être défini aux fins de prévision.
* *Seasonality_threshold*: Le seuil de la saisonnalité lorsque *la saisonnalité* est réglée `0.6`pour détecter automatiquement, le seuil de score par défaut est . Pour plus d’informations voir [series_periods_detect](series-periods-detectfunction.md).

**Retour**

 La fonction renvoie la série respective suivante :

* `baseline`: la valeur prévue de la série (somme des composantes saisonnières et tendance, voir ci-dessous)
* `seasonal`: la série de la composante saisonnière :
    * si la période n’est pas détectée ou explicitement réglée à 0 : constante 0
    * s’il est détecté ou mis à l’intégrateur positif: médiane des points de la série dans la même phase
* `trend`: la série de la composante tendance
* `residual`: la série du composant résiduel (c.-à-d. x - base de base)
  

**Remarques**

* Ordre d’exécution des composants :
    1. Extraire la série saisonnière
    2. Soustrayez-le de x, générant la série désaisonnale
    3. Extraire le composant tendance de la série désaisonnale
    4. Créer la ligne de base , la tendance saisonnière et saisonnière
    5. Créer le résidu x - base de base
    
* Soit la saisonnalité et/ou la tendance doivent être activées, sinon la fonction est redondante et retourne juste la ligne de base 0 et résiduelle x

**En savoir plus sur la décomposition des séries**

Cette méthode est habituellement appliquée à des séries chronoloques de mesures censées manifester un comportement périodique et/ou tendance (p. ex. le trafic de service et d’autres mesures d’utilisation), afin de prévoir les valeurs métriques futures et/ou de détecter les valeurs anormales. L’hypothèse implicite de ce processus de régression est qu’en dehors du comportement saisonnier et de tendance (a-priori connu), la série temporelle est stochastique et distribuée au hasard; par conséquent, nous pouvons prévoir les valeurs métriques futures des composantes saisonnières et tendance (ignorant la partie résiduelle), tandis que nous pouvons détecter des valeurs anormales basées sur une détection aberrante sur la partie résiduelle seulement. Plus de détails peuvent être trouvés dans le [chapitre de décomposition des séries chronos](https://www.otexts.org/fpp/6) dans ce grand livre.

**Exemples**

**1. Saisonnalité hebdomadaire**

Dans l’exemple suivant, nous générons une série avec une saisonnalité hebdomadaire et sans tendance, nous y ajoutons ensuite quelques valeurs aberrantes. `series_decompose`trouve l’auto-détection de la saisonnalité et génère une ligne de base qui est presque identique à la composante saisonnière. Les valeurs aberrantes que nous avons ajoutées peuvent être clairement visibles dans la composante résiduelles.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose1.png" alt-text="Série décomposition 1":::

**2. Saisonnalité hebdomadaire avec tendance**

Dans cet exemple, nous ajoutons une tendance à la série de l’exemple précédent. Tout d’abord, nous courons `series_decompose` avec `avg` les paramètres par défaut dans lesquels la valeur par défaut de tendance ne prend que la moyenne et ne calcule pas la tendance, nous pouvons voir que la ligne de base générée ne contient pas la tendance et est moins précis par rapport à l’exemple précédent, il est plus évident lors de l’observation de la tendance dans les résidus.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose2.png" alt-text="Série décomposer 2":::

Ensuite, nous jouons le même exemple, mais puisque `linefit` nous nous attendons à une tendance dans la série, nous spécifions dans le paramètre de tendance. Nous pouvons voir que la tendance positive est détectée et que la ligne de base est beaucoup plus proche de la série d’entrées. Les résidus sont proches de zéro avec seulement les valeurs aberrantes se démarquer. Nous pouvons voir tous les composants de la série dans le graphique.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y, -1, 'linefit')
| render timechart  
```

:::image type="content" source="images/samples/series-decompose3.png" alt-text="Série décomposer 3":::

---
title: series_decompose ()-Azure Explorateur de données
description: Cet article décrit series_decompose () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 9ff0df578f174bc6964e39e799b91068f89a28e4
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793936"
---
# <a name="series_decompose"></a>series_decompose()

Applique une transformation de décomposition sur une série.  

Prend une expression contenant une série (tableau numérique dynamique) comme entrée et la décompose en composants saisonniers, de tendance et résiduels.
 
## <a name="syntax"></a>Syntaxe

`series_decompose(`*Série* `[,` Caractère *saisonnier* `,` *Tendance* `,` *Test_points* `,` *Seasonality_threshold*`])`

## <a name="arguments"></a>Arguments

* *Série*: cellule de tableau dynamique, qui est un tableau de valeurs numériques, généralement le résultat des opérateurs de [série make](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md)
* Caractère *saisonnier*: un entier contrôlant l’analyse saisonnière, contenant soit
    * -1 : détection automatique des valeurs saisonnieres à l’aide de [series_periods_detect](series-periods-detectfunction.md) (par défaut).
    * period : entier positif spécifiant la période attendue en nombre d’emplacements. Par exemple, si la série est dans des emplacements 1 h, une période hebdomadaire est de 168 emplacements.
    * 0 : pas de caractère saisonnier (ignorer l’extraction de ce composant).    
* *Tendance*: chaîne contrôlant l’analyse des tendances, contenant l’une des valeurs suivantes :
    * « AVG » : définir le composant de tendance comme moyenne (x) (valeur par défaut)
    * « linefit » : extraction du composant de tendance à l’aide de la régression linéaire.
    * « None » : aucune tendance. ignorez l’extraction de ce composant.    
* *Test_points*: 0 (valeur par défaut) ou un entier positif, qui spécifie le nombre de points à la fin de la série à exclure du processus d’apprentissage (régression). Ce paramètre doit être défini à des fins de prévision.
* *Seasonality_threshold*: le seuil du score saisonnier lorsque le caractère *saisonnier* est défini sur détection automatique, le seuil de score par défaut est `0.6` . Pour plus d’informations, consultez [series_periods_detect](series-periods-detectfunction.md).

**Renvoi**

 La fonction retourne les séries correspondantes suivantes :

* `baseline`: la valeur prédite de la série (somme des composants saisonniers et Trend, voir ci-dessous).
* `seasonal`: la série du composant saisonnier :
    * Si la période n’est pas détectée ou est définie explicitement sur 0 : constante 0.
    * s’il est détecté ou défini sur un entier positif : médiane des points de série dans la même phase
* `trend`: la série du composant de tendance.
* `residual`: la série du composant résiduel (autrement dit, x-Baseline).
  

**Notes**

* Ordre d’exécution des composants :
    1. Extraire la série saisonnière
    2. Le soustraire de x, ce qui génère la série désaisonnièree
    3. Extraire le composant Trend de la série désaisonnelle
    4. Créer la ligne de base = saisonnier + tendance
    5. Créer le résiduel = x-ligne de base
    
* Vous devez activer les deux saisonniers et, ou la tendance. Dans le cas contraire, la fonction est redondante et retourne simplement la ligne de base = 0 et la valeur résiduelle = x.

**En savoir plus sur la décomposition des séries**

Cette méthode est généralement appliquée à la série chronologique de mesures attendues pour manifester le comportement périodique et/ou de tendance. Vous pouvez utiliser la méthode pour prévoir les valeurs métriques futures et/ou détecter les valeurs anormales. L’hypothèse implicite de ce processus de régression est que, en dehors du comportement saisonnier et de tendance, la série chronologique est stochastique et distribuée de manière aléatoire. Prévoyez les futures valeurs de métriques à partir des composants saisonniers et de tendance tout en ignorant la partie résiduelle. Détecte les valeurs anormales en fonction de la détection des valeurs hors norme uniquement sur la partie résiduelle. Vous trouverez plus d’informations dans le [chapitre décomposition des séries chronologiques](https://otexts.com/fpp2/decomposition.html).

## <a name="examples"></a>Exemples

**Caractère saisonnier hebdomadaire**

Dans l’exemple suivant, nous générons une série avec un caractère saisonnier hebdomadaire et sans tendance, nous y ajoutons quelques valeurs hors norme. `series_decompose` recherche et détecte automatiquement le caractère saisonnier et génère une ligne de base quasiment identique au composant saisonnier. Les valeurs hors norme que nous avons ajoutées peuvent être clairement visibles dans le composant residus.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/samples/series-decompose1.png" alt-text="Série décomposer 1":::

**Caractère saisonnier hebdomadaire avec tendance**

Dans cet exemple, nous ajoutons une tendance à la série de l’exemple précédent. Tout d’abord, nous exécutons `series_decompose` avec les paramètres par défaut. La `avg` valeur par défaut de la tendance prend uniquement la moyenne et ne calcule pas la tendance. La ligne de base générée ne contient pas la tendance. Lorsque l’on observe la tendance dans les résidus, il devient évident que cet exemple est moins précis que l’exemple précédent.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/samples/series-decompose2.png" alt-text="Série, décomposer 2":::

Ensuite, nous réexécuterons le même exemple. Étant donné que nous attendions une tendance dans la série, nous spécifions `linefit` dans le paramètre Trend. Nous pouvons voir que la tendance positive est détectée et que la base de référence est plus proche de la série d’entrée. Les résidus sont proches de zéro et seuls les valeurs hors norme sont représentées. Nous pouvons voir tous les composants de la série dans le graphique.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/samples/series-decompose3.png" alt-text="Série, décomposer 3":::

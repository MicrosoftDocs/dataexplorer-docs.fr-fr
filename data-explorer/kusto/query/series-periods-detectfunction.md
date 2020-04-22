---
title: series_periods_detect() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_periods_detect() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 0bc78f93270809774504c1eccbc9fa2bbce6c964
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663439"
---
# <a name="series_periods_detect"></a>series_periods_detect()

Trouve les périodes les plus importantes qui existent dans une série de temps.  

Très souvent, une mesure métrique du trafic d’une application se caractérise par deux périodes importantes : une période hebdomadaire et une quotidienne. Compte tenu d’une telle série de temps, `series_periods_detect()` doit détecter ces 2 périodes dominantes.  
La fonction prend comme entrée une colonne contenant un tableau dynamique de séries chronos (généralement la sortie résultante de [l’opérateur](make-seriesoperator.md) de la série de produits), deux `real` nombres définissant la taille minimale et `long` maximale de la période (c.-à-d. le nombre de bacs, par exemple pour 1h bin la taille d’une période quotidienne serait de 24) à rechercher, et un nombre définissant le nombre total de périodes pour la fonction de recherche. La fonction produit 2 colonnes :
* *périodes*: un tableau dynamique contenant les périodes qui ont été trouvées (dans les unités de la taille du bac), commandée par leurs scores
* *scores*: un tableau dynamique contenant des valeurs comprises entre 0 et 1, chacun mesure l’importance d’une période dans sa position respective dans le tableau *des périodes*
 
**Syntaxe**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**Arguments**

* *x*: Expression scalaire de tableau dynamique qui est un éventail de valeurs numériques, typiquement la sortie résultante des opérateurs [de make-series](make-seriesoperator.md) ou [de make_list.](makelist-aggfunction.md)
* *min_period*: Un `real` nombre précisant la période minimale à rechercher.
* *max_period*: Un `real` nombre précisant la période maximale à rechercher.
* *num_periods*: Un `long` nombre précisant le nombre maximum de périodes requise. Ce sera la longueur des tableaux dynamiques de sortie.

> [!IMPORTANT]
> * L’algorithme peut détecter les périodes contenant au moins 4 points et tout au plus la moitié de la longueur de la série. 
>
> * Vous devez définir le *min_period* un peu en dessous et *max_period* un peu au-dessus des périodes que vous vous attendez à trouver dans la série de temps. Par exemple, si vous avez un signal agrégé horaire, et que vous recherchez des périodes quotidiennes > et hebdomadaires (ce qui serait 24 &\*168 respectivement), vous pouvez définir\* *min_period*0,8 24 euros, *max_period*1,2 168 euros, laissant 20% de marges autour de ces périodes.
>
> * La série de temps d’entrée doit être régulière, c’est-à-dire agrégée dans des bacs constants (ce qui est toujours le cas si elle a été créée à l’aide [de make-series](make-seriesoperator.md)). Dans le cas contraire, le résultat n’est pas significatif.


**Exemple**

La requête suivante intègre un instantané d’un mois du trafic d’une demande, agrégé deux fois par jour (c’est-à-dire que la taille du bac est de 12 heures).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="Périodes de série":::

Courir `series_periods_detect()` sur cette série se traduit par une période hebdomadaire (14 points de long):

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| périodes\_\_de\_série\_détecter y périodes  | les\_\_périodes de série détectent les\_scores des périodes\_\_y |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


Notez que la période quotidienne qui peut également être vu dans le graphique n’a pas été trouvé puisque l’échantillonnage est trop grossier (12h taille du bac) de sorte qu’une période quotidienne de 2 bacs est beugler la taille de la période minimale de 4 points requis par l’algorithme.
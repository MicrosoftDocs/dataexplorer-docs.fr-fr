---
title: series_periods_detect ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit series_periods_detect () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 940419fea831f382a62359de28d37bc0b207676b
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618630"
---
# <a name="series_periods_detect"></a>series_periods_detect()

Recherche les périodes les plus significatives qui existent dans une série chronologique.  

Très souvent, une mesure mesurant le trafic d’une application est caractérisée par deux périodes importantes : une fois par semaine et par jour. En raison de ces séries chronologiques, `series_periods_detect()` doit détecter ces 2 périodes dominantes.  
La fonction prend comme entrée une colonne contenant un tableau dynamique de séries chronologiques (en général, le résultat de l’opérateur [Make-Series](make-seriesoperator.md) ), deux `real` nombres définissant la taille minimale et maximale de la période (par exemple, le nombre d’emplacements, par exemple, la taille d’une période quotidienne) à rechercher et `long` un nombre qui définit le nombre total de périodes pour la fonction à rechercher. La fonction génère 2 colonnes :
* *périodes*: tableau dynamique contenant les périodes trouvées (en unités de la taille de l’emplacement), classées par score
* *scores*: tableau dynamique contenant des valeurs comprises entre 0 et 1, chacun mesurant l’importance d’un point à sa position respective dans le tableau des *périodes*
 
**Syntaxe**

`series_periods_detect(`*x* `,` *min_period* min_period`,` *max_period* max_period`,` *num_periods*`)`

**Arguments**

* *x*: expression scalaire de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md) .
* *min_period*: `real` nombre spécifiant la période minimale à rechercher.
* *max_period*: `real` nombre spécifiant la période maximale à rechercher.
* *num_periods*: `long` nombre spécifiant le nombre maximal de périodes requis. Il s’agit de la longueur des tableaux dynamiques de sortie.

> [!IMPORTANT]
> * L’algorithme peut détecter les périodes qui contiennent au moins 4 points et au plus la moitié de la longueur de la série. 
>
> * Vous devez définir le *min_period* un peu plus bas et *max_period* un peu au-dessus des périodes que vous vous attendez à trouver dans la série chronologique. Par exemple, si vous avez un signal regroupé toutes les heures et que vous recherchez à la fois des > quotidiennes et des périodes hebdomadaires (24 & 168 respectivement), *min_period*vous pouvez définir\*min_period = 0,8 24 *max_period*= 1,2\*168, en laissant 20% de marges autour de ces périodes.
>
> * La série chronologique d’entrée doit être régulière, c’est-à-dire agrégée dans des emplacements constants (ce qui est toujours le cas si elle a été créée à l’aide de la [série make](make-seriesoperator.md)). Dans le cas contraire, le résultat n’est pas significatif.


**Exemple**

La requête suivante incorpore une capture instantanée d’un mois du trafic d’une application, agrégée deux fois par jour (par exemple, la taille de l’emplacement est de 12 heures).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="Périodes de série":::

S' `series_periods_detect()` exécuter sur cette série entraîne la période hebdomadaire (14 points longs) :

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| les\_périodes\_de\_série\_détectent les périodes y  | périodes\_\_de la\_série\_détecter\_les scores y périodes |
|-------------|-------------------|
| [14.0, 0.0] | [0,84, 0,0]  |


Notez que la période quotidienne qui peut également être observée dans le graphique est introuvable, car l’échantillonnage est trop grossiste (12 h bin Size). par conséquent, une période quotidienne de 2 emplacements fait souffler la taille de la période minimale de 4 points requis par l’algorithme.
---
title: series_periods_detect ()-Azure Explorateur de données
description: Cet article décrit series_periods_detect () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 6e362dd7d94c57cb79ee58c8ed7b36b719030987
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252925"
---
# <a name="series_periods_detect"></a>series_periods_detect()

Recherche les périodes les plus significatives qui existent dans une série chronologique.  

Souvent, une mesure mesurant le trafic d’une application est caractérisée par deux périodes importantes : une fois par semaine et par jour. La fonction `series_periods_detect()` détecte ces deux périodes dominantes dans une série chronologique.  
La fonction prend comme entrée :
* Colonne contenant un tableau dynamique de séries chronologiques. En règle générale, la colonne est le résultat de l’opérateur [Make-Series](make-seriesoperator.md) .
* Deux `real` nombres définissant la taille minimale et maximale de la période, le nombre d’emplacements à rechercher. Par exemple, pour un casier 1H, la taille d’une période quotidienne est de 24. 
* `long`Nombre qui définit le nombre total de périodes de recherche de la fonction. 

La fonction génère deux colonnes :
* *périodes*: tableau dynamique contenant les périodes qui ont été trouvées, en unités de taille de compartiment, classées par leurs scores.
* *scores*: tableau dynamique contenant des valeurs comprises entre 0 et 1. Chaque tableau mesure l’importance d’une période à sa position respective dans le tableau des *périodes* .
 
## <a name="syntax"></a>Syntaxe

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

## <a name="arguments"></a>Arguments

* *x*: expression scalaire de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md) .
* *min_period*: `real` nombre spécifiant la période minimale à rechercher.
* *max_period*: `real` nombre spécifiant la période maximale à rechercher.
* *num_periods*: `long` nombre spécifiant le nombre maximal de périodes requis. Ce nombre correspond à la longueur des tableaux dynamiques de sortie.

> [!IMPORTANT]
> * L’algorithme peut détecter les périodes qui contiennent au moins 4 points et au plus la moitié de la longueur de la série. 
>
> * Définissez le *min_period* un peu plus bas et *max_period* un peu plus haut, les périodes que vous vous attendez à trouver dans la série chronologique. Par exemple, si vous avez un signal agrégé toutes les heures et que vous recherchez des périodes quotidiennes et hebdomadaires (respectivement 24 et 168 heures), vous pouvez définir *min_period*= 0,8 \* 24, *max_period*= 1,2 \* 168, et conserver 20% de marges autour de ces périodes.
>
> * La série chronologique d’entrée doit être normale. Autrement dit, agrégé dans des emplacements constants, ce qui est toujours le cas s’il a été créé à l’aide de la [série make](make-seriesoperator.md). Dans le cas contraire, le résultat n’est pas significatif.

## <a name="example"></a>Exemple

La requête suivante incorpore une capture instantanée d’un mois du trafic d’une application, regroupée deux fois par jour. La taille de l’emplacement est de 12 heures.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="Périodes de série":::

En cours `series_periods_detect()` d’exécution sur cette série, produit une période hebdomadaire de 14 points.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| les \_ périodes de série \_ détectent les \_ \_ périodes y  | périodes de la série \_ \_ détecter les \_ \_ \_ scores y périodes |
|-------------|-------------------|
| [14.0, 0.0] | [0,84, 0,0]  |


> [!NOTE] 
> La période quotidienne qui peut également être observée dans le graphique est introuvable, car l’échantillonnage est trop grossiste (12 h bin Size). par conséquent, une période quotidienne de 2 emplacements est inférieure à la taille minimale de 4 points, requise par l’algorithme.

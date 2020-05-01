---
title: series_periods_validate ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit series_periods_validate () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 89ac06f2d2bbb376f08cf3fd88d316a7fcab0594
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618601"
---
# <a name="series_periods_validate"></a>series_periods_validate()

Vérifie si une série chronologique contient des modèles périodiques de longueurs données.  

Très souvent, une mesure mesurant le trafic d’une application est caractérisée par des périodes hebdomadaires et/ou quotidiennes. Vous pouvez le confirmer en exécutant `series_periods_validate()` une vérification pour une période hebdomadaire et quotidienne.

La fonction prend comme entrée une colonne contenant un tableau dynamique de séries chronologiques (en général, le résultat de l’opérateur [Make-Series](make-seriesoperator.md) ) et un `real` ou plusieurs nombres qui définissent les longueurs des périodes à valider. 

La fonction génère 2 colonnes :
* *periods*: tableau dynamique contenant les périodes à valider (fournies dans l’entrée)
* *scores*: tableau dynamique contenant un score compris entre 0 et 1 qui mesure l’importance d’un point à sa position respective dans le tableau des *périodes*

**Syntaxe**

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* PERIOD2 `,` . . . ] `)`

**Arguments**

* *x*: expression scalaire de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md) .
* *period1*, *PERIOD2*, etc. : `real` nombres spécifiant les périodes à valider, en unités de la taille de l’emplacement. Par exemple, si la série est dans des emplacements 1H, une période hebdomadaire est de 168 emplacements.

> [!IMPORTANT]
> * La valeur minimale pour chacun des arguments de *période* est **4** et la valeur maximale est la moitié de la longueur de la série d’entrée. pour un argument de *période* en dehors de ces limites, le score de sortie est **0**.
>
> * La série chronologique d’entrée doit être régulière, c’est-à-dire agrégée dans des emplacements constants (ce qui est toujours le cas si elle a été créée à l’aide de la [série make](make-seriesoperator.md)). Dans le cas contraire, le résultat n’est pas significatif.
> 
> * La fonction accepte jusqu’à 16 périodes à valider.


**Exemple**

La requête suivante incorpore une capture instantanée d’un mois du trafic d’une application, agrégée deux fois par jour (par exemple, la taille de l’emplacement est de 12 heures).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="Périodes de série":::

S' `series_periods_validate()` exécuter sur cette série pour valider une période hebdomadaire (14 points longs) génère un score élevé, avec un score de **0** pour valider une période de cinq jours (10 points de long).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| les\_périodes\_de\_série\_valident les périodes y  | les\_périodes\_de\_série\_valident les scores y |
|-------------|-------------------|
| [14,0, 10,0] | [0.84, 0.0]  |
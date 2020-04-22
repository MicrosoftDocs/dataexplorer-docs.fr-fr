---
title: series_periods_validate() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_periods_validate () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 8eba96e21513e776c984a356f88a705ca46485af
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663400"
---
# <a name="series_periods_validate"></a>series_periods_validate()

Vérifie si une série de temps contient des modèles périodiques de longueurs données.  

Très souvent, une mesure mesurant le trafic d’une application se caractérise par des périodes hebdomadaires et/ou quotidiennes. Cela peut être `series_periods_validate()` confirmé en exécutant la vérification pour une période hebdomadaire et quotidienne.

La fonction prend comme entrée une colonne contenant un tableau dynamique de séries chronos `real` (généralement la sortie résultante de [l’opérateur](make-seriesoperator.md) de la série de produits), et un ou plusieurs nombres qui définissent la longueur des périodes à valider. 

La fonction produit 2 colonnes :
* *périodes*: un tableau dynamique contenant les périodes à valider (fourni dans l’entrée)
* *scores*: un tableau dynamique contenant un score compris entre 0 et 1 qui mesure l’importance d’une période dans sa position respective dans le tableau *des périodes*

**Syntaxe**

`series_periods_validate(`*x*`,` *x période1* `,` [ *période2* `,` . . . ] `)`

**Arguments**

* *x*: Expression scalaire de tableau dynamique qui est un éventail de valeurs numériques, typiquement la sortie résultante des opérateurs [de make-series](make-seriesoperator.md) ou [de make_list.](makelist-aggfunction.md)
* *période1*, *période2,* `real` etc. : nombres précisant les périodes à valider, en unités de la taille du bac. Par exemple, si la série est dans 1h poubelles, une période hebdomadaire est de 168 bacs.

> [!IMPORTANT]
> * La valeur minimale pour chacun des *arguments* de période est **de 4** et le maximum est la moitié de la longueur de la série d’entrées; pour un argument *d’époque* en dehors de ces limites, le score de sortie sera **de 0**.
>
> * La série de temps d’entrée doit être régulière, c’est-à-dire agrégée dans des bacs constants (ce qui est toujours le cas si elle a été créée à l’aide [de make-series](make-seriesoperator.md)). Dans le cas contraire, le résultat n’est pas significatif.
> 
> * La fonction accepte jusqu’à 16 périodes à valider.


**Exemple**

La requête suivante intègre un instantané d’un mois du trafic d’une demande, agrégé deux fois par jour (c’est-à-dire que la taille du bac est de 12 heures).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="Périodes de série":::

Courir `series_periods_validate()` sur cette série pour valider une période hebdomadaire (14 points de long) se traduit par un score élevé, et avec un score **de 0** lors de la validation d’une période de cinq jours (10 points de long).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| périodes\_\_de\_série\_valider y périodes  | périodes\_\_de\_série\_valider y scores |
|-------------|-------------------|
| [14.0, 10.0] | [0.84,0.0]  |
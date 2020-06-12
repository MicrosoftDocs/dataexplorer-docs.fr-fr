---
title: series_periods_validate ()-Azure Explorateur de données
description: Cet article décrit series_periods_validate () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 0e93383cf1c9ff11fdf4a14ebad5d83c0dfa7a74
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717408"
---
# <a name="series_periods_validate"></a>series_periods_validate()

Vérifie si une série chronologique contient des modèles périodiques de longueurs données.  

Souvent, une mesure mesurant le trafic d’une application est caractérisée par une période hebdomadaire ou quotidienne. Cette période peut être confirmée en exécutant `series_periods_validate()` qui vérifie une période hebdomadaire et quotidienne.

La fonction prend comme entrée une colonne qui contient un tableau dynamique de séries chronologiques (en général, le résultat de l’opérateur [Make-Series](make-seriesoperator.md) ) et un ou plusieurs `real` nombres qui définissent les longueurs des périodes à valider.

La fonction génère deux colonnes :
* *periods*: tableau dynamique qui contient les périodes à valider (fournies dans l’entrée).
* *scores*: tableau dynamique qui contient un score compris entre 0 et 1. Le score indique l’importance d’une période à sa position respective dans le tableau des *périodes* .

**Syntaxe**

`series_periods_validate(`*x* `,` *period1* [ `,` *PERIOD2* `,` . . . ] `)`

**Arguments**

* *x*: expression scalaire de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md) .
* *period1*, *PERIOD2*, etc `real` . : nombres spécifiant les périodes à valider, en unités de la taille de l’emplacement. Par exemple, si la série est dans des emplacements 1H, une période hebdomadaire est de 168 emplacements.

> [!IMPORTANT]
> * La valeur minimale pour chacun des arguments de *période* est **4** et la valeur maximale est la moitié de la longueur de la série d’entrée. Pour un argument de *période* en dehors de ces limites, le score de sortie est **0**.
>
> * La série chronologique d’entrée doit être régulière, c’est-à-dire agrégée dans des emplacements constants, et est toujours le cas si elle a été créée à l’aide de la [série make](make-seriesoperator.md). Dans le cas contraire, le résultat n’est pas significatif.
> 
> * La fonction accepte jusqu’à 16 périodes à valider.

**Exemple**

La requête suivante incorpore une capture instantanée d’un mois du trafic d’une application, regroupée deux fois par jour (la taille de l’emplacement est de 12 heures).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="Périodes de série":::

Si vous exécutez `series_periods_validate()` sur cette série pour valider une période hebdomadaire (14 points longs), un score élevé est obtenu, avec un score de **0** lorsque vous validez une période de cinq jours (10 points de long).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| les \_ périodes de série \_ valident les \_ \_ périodes y  | les \_ périodes de série \_ valident les \_ \_ scores y |
|-------------|-------------------|
| [14,0, 10,0] | [0.84, 0.0]  |

---
title: plug-in panier-Azure Explorateur de données
description: Cet article décrit le plug-in du panier dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: cf83690d61bb84d1b6b877e76a77d5776be35ad4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349242"
---
# <a name="basket-plugin"></a>basket, plug-in

```kusto
T | evaluate basket()
```

Panier recherche tous les modèles fréquents d’attributs discrets (dimensions) dans les données. Elle retourne ensuite les modèles fréquents qui ont dépassé le seuil de fréquence dans la requête d’origine. Le panier est assuré de trouver tous les modèles fréquents dans les données, mais il n’est pas garanti d’avoir un Runtime polynomial. Le runtime de la requête est linéaire dans le nombre de lignes, mais il peut être exponentiel dans le nombre de colonnes (dimensions). Basket repose sur l’algorithme Apriori, développé à l’origine pour l’exploration de données d’analyse du panier.

## <a name="syntax"></a>Syntaxe

`T | evaluate basket(`*arguments*`)`

## <a name="returns"></a>Retourne

Panier retourne tous les modèles fréquents qui apparaissent au-dessus du seuil de ratio des lignes. Le seuil par défaut est 0,05. Chaque modèle est représenté par une ligne dans les résultats.

La première colonne est l’ID de segment. Les deux colonnes suivantes correspondent au *nombre* et au *pourcentage de lignes*de la requête d’origine, qui sont capturées par le modèle. Les colonnes restantes proviennent de la requête d’origine.
Leur valeur est soit une valeur spécifique de la colonne, soit une valeur générique, qui est NULL par défaut, ce qui signifie une valeur de variable.

**Arguments (tous facultatifs)**

`T | evaluate basket(`[*Seuil*, *WeightColumn*, *MaxDimensions*, *CustomWildcard*, *CustomWildcard*,...]`)`

Tous les arguments sont facultatifs, mais ils doivent alors être ordonnés comme ci-dessus. Pour indiquer que la valeur par défaut doit être utilisée, utilisez la valeur tilde de chaîne-' ~ '. Voir les exemples ci-dessous.

Arguments disponibles :

* Seuil-0,015 < *double* < 1 [par défaut : 0,05]

    Définit le ratio minimal des lignes à considérer comme fréquentes. Les modèles avec un rapport plus petit ne seront pas retournés.
    
    Exemple : `T | evaluate basket(0.02)`

* WeightColumn - *nom_colonne*

    Considère chaque ligne de l’entrée en fonction de la pondération spécifiée. Par défaut, chaque ligne a une pondération de « 1 ». L’argument doit être un nom de colonne numérique, tel que int, long, Real. Une colonne de pondération est couramment utilisée pour prendre en compte l’échantillonnage ou le compartimentage/l’agrégation des données déjà incorporées dans chaque ligne.

    Exemple : `T | evaluate basket('~', sample_Count)`

* MaxDimensions-1 < *int* [par défaut : 5]

    Définit le nombre maximal de dimensions non corrélées par panier, limité par défaut, pour réduire l’exécution de la requête.

    Exemple : `T | evaluate basket('~', '~', 3)`

* CustomWildcard- *« any_value_per_type »*

    Définit la valeur de caractère générique pour un type spécifique dans la table de résultats qui indique que le modèle actuel ne présente pas de restriction sur cette colonne.
    La valeur par défaut est Null. La valeur par défaut d’une chaîne est une chaîne vide. Si la valeur par défaut est une bonne valeur dans les données, vous devez utiliser une autre valeur de caractère générique, telle que `*` .

    Par exemple :

     `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2)
```

|ID de segment|Count|Pourcentage|State|Type d’événement|Dommage|Récoltes|
|---|---|---|---|---|---|---|---|---|
|0|4574|77,7|||Non|0
|1|2278|38,7||Grêle|Non|0
|2|5675|96,4||||0
|3|2371|40,3||Grêle||0
|4|1279|21,7||Vent d’orage||0
|5|2468|41,9||Grêle||
|6|1310|22,3|||YES|
|7|1291|21,9||Vent d’orage||

**Exemple avec des caractères génériques personnalisés**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2, '~', '~', '*', int(-1))
```

|ID de segment|Count|Pourcentage|State|Type d’événement|Dommage|Récoltes|
|---|---|---|---|---|---|---|---|---|
|0|4574|77,7|\*|\*|Non|0
|1|2278|38,7|\*|Grêle|Non|0
|2|5675|96,4|\*|\*|\*|0
|3|2371|40,3|\*|Grêle|\*|0
|4|1279|21,7|\*|Vent d’orage|\*|0
|5|2468|41,9|\*|Grêle|\*|-1
|6|1310|22,3|\*|\*|YES|-1
|7|1291|21,9|\*|Vent d’orage|\*|-1

---
title: plug-in autocluster-Azure Explorateur de données
description: Cet article décrit le plug-in autocluster dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 959b11eca2dc369a3f737e01175f77ff6626f773
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803843"
---
# <a name="autocluster-plugin"></a>autocluster, plug-in

```kusto
T | evaluate autocluster()
```

`autocluster`recherche des modèles communs d’attributs discrets (dimensions) dans les données. Il réduit ensuite les résultats de la requête d’origine, qu’il s’agisse de 100 ou 100 000 lignes, avec un petit nombre de modèles. Le plug-in a été développé pour faciliter l’analyse des défaillances (telles que les exceptions ou les incidents), mais peut éventuellement fonctionner sur n’importe quel jeu de données filtré.

> [!NOTE]
> `autocluster`repose en grande partie sur l’algorithme Seed-Expand du document suivant : [algorithmes pour l’exploration de données de télémétrie à l’aide d’attributs discrets](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1). 


## <a name="syntax"></a>Syntaxe

`T | evaluate autocluster(`*arguments*`)`

## <a name="returns"></a>Retours

Le `autocluster` plug-in retourne un ensemble de modèles (généralement petit). Les modèles capturent des portions des données avec des valeurs communes partagées sur plusieurs attributs discrets. Chaque modèle dans les résultats est représenté par une ligne.

La première colonne est l’ID de segment. Les deux colonnes suivantes contiennent le nombre et le pourcentage de lignes de la requête d’origine qui sont capturés par le modèle. Les colonnes restantes proviennent de la requête d’origine. Leur valeur est soit une valeur spécifique de la colonne, soit une valeur de caractère générique (null par défaut) qui signifie des valeurs de variables.

Les modèles ne sont pas distincts, peuvent se chevaucher et ne couvrent généralement pas toutes les lignes d’origine. Certaines lignes peuvent n’appartenir à aucun modèle.

> [!TIP]
> Utilisez [Where](./whereoperator.md) et [Project](./projectoperator.md) dans le canal d’entrée pour réduire les données uniquement à ce qui vous intéresse.
>
> Lorsque vous trouvez une ligne intéressante, vous pouvez l’explorer plus en détail en ajoutant ses valeurs spécifiques à votre filtre `where` .

## <a name="arguments"></a>Arguments 

> [!NOTE] 
> Tous les arguments sont facultatifs.

`T | evaluate autocluster(`[*SizeWeight*, *WeightColumn*, *NumSeeds*, *CustomWildcard*, *CustomWildcard*,...]`)`

Tous les arguments sont facultatifs, mais ils doivent alors être ordonnés comme ci-dessus. Pour indiquer que la valeur par défaut doit être utilisée, placez la valeur de tilde de chaîne « ~ » (consultez la colonne « example » dans la table).

|Argument        | Type, plage, valeur par défaut              |Description                | Exemple                                        |
|----------------|-----------------------------------|---------------------------|------------------------------------------------|
| SizeWeight     | 0 < *double* < 1 [par défaut : 0,5]   | Vous donne un contrôle sur l’équilibre entre le générique (couverture élevée) et les valeurs informatives (nombreuses). Si vous augmentez la valeur, cela réduit généralement le nombre de modèles, et chaque modèle a tendance à couvrir un pourcentage plus élevé. Si vous diminuez la valeur, cela produit généralement des modèles plus spécifiques avec davantage de valeurs partagées et une couverture de pourcentage plus faible. La formule sous-capot est une moyenne géométrique pondérée entre le score générique normalisé et le score informatif avec les pondérations `SizeWeight` et`1-SizeWeight`                   | `T | evaluate autocluster(0.8)`                |
|WeightColumn    | *column_name*                     | Considère chaque ligne de l’entrée en fonction de la pondération spécifiée (par défaut, chaque ligne a une pondération de « 1 »). L’argument doit être un nom de colonne numérique (tel que int, long, Real). Il est courant d’utiliser une colonne de pondération en prenant en compte l’échantillonnage ou la création de compartiments/l’agrégation des données déjà incorporées dans chaque ligne.                                                                                                       | `T | evaluate autocluster('~', sample_Count)` | 
| NumSeeds        | *int* [valeur par défaut : 25]              | Le nombre de valeurs initiales détermine le nombre de points de recherche locaux initiaux de l’algorithme. Dans certains cas, selon la structure des données et si vous augmentez le nombre de graines, le nombre (ou la qualité) des résultats augmente dans l’espace de recherche développé avec un compromis de requête plus lent. La valeur a réduit les résultats dans les deux sens. par conséquent, si vous la réduisez au-dessous de cinq, vous obtiendrez des améliorations de performances négligeables. Si vous augmentez à plus de 50, il générera rarement des modèles supplémentaires.                                         | `T | evaluate autocluster('~', '~', 15)`       |
| CustomWildcard  | *« any_value_per_type »*           | Définit la valeur de caractère générique pour un type spécifique dans la table de résultats. Elle indique que le modèle actuel n’a pas de restriction sur cette colonne. La valeur par défaut est null, car la chaîne par défaut est une chaîne vide. Si la valeur par défaut est une bonne valeur dans les données, une autre valeur de caractère générique doit être utilisée (par exemple, `*` ).                                                                                                                | `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))` |

## <a name="examples"></a>Exemples

### <a name="using-autocluster"></a>Utilisation d’autocluster

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|ID de segment|Count|Pourcentage|State|Type d’événement|Dommage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7||Grêle|Non
|1|512|8,7||Vent d’orage|YES
|2|898|15,3|TEXAS||

### <a name="using-custom-wildcards"></a>Utilisation de caractères génériques personnalisés

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|ID de segment|Count|Pourcentage|State|Type d’événement|Dommage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7|\*|Grêle|Non
|1|512|8,7|\*|Vent d’orage|YES
|2|898|15,3|TEXAS|\*|\*

## <a name="see-also"></a>Voir aussi

* [basket](./basketplugin.md)
* [abaisse](./reduceoperator.md)

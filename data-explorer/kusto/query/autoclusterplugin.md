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
ms.openlocfilehash: 0b2d76c0dd7a3ee0724db67aa8cb0cd9229748fa
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225528"
---
# <a name="autocluster-plugin"></a>plug-in de clusters

```kusto
T | evaluate autocluster()
```

AutoCluster recherche les modèles courants d’attributs discrets (dimensions) dans les données et réduit les résultats de la requête d’origine (qu’elle fasse 100 ou 100 000 lignes) à un petit nombre de modèles. AutoCluster a été développé pour analyser les échecs (par exemple, les exceptions, les incidents) mais peut éventuellement fonctionner sur n’importe quel jeu de données filtré. 

**Syntaxe**

`T | evaluate autocluster(`*arguments*`)`

**Retourne**

AutoCluster retourne un jeu de modèles (généralement petit) qui permettent de capturer des parties de données comportant des valeurs courantes partagées par plusieurs attributs discrets. Chaque modèle est représenté par une ligne dans les résultats. 

La première colonne est l’ID de segment. Les deux colonnes suivantes correspondent au nombre et au pourcentage de lignes de la requête d’origine capturées par le modèle. Les colonnes restantes sont issues de la requête d’origine et leur valeur est soit une valeur spécifique de la colonne soit une valeur générique (null par défaut), qui correspond à des valeurs de variables. 

Notez que les modèles ne sont pas distincts : ils peuvent se chevaucher et ne couvrent généralement pas toutes les lignes d’origine. Certaines lignes peuvent n’appartenir à aucun modèle.

> [!TIP]
> Utilisez [Where](./whereoperator.md) et [Project](./projectoperator.md) dans le canal d’entrée pour réduire les données uniquement à ce qui vous intéresse.
>
> Lorsque vous trouvez une ligne intéressante, vous pouvez l’explorer plus en détail en ajoutant ses valeurs spécifiques à votre filtre `where` .

**Arguments (tous facultatifs)**

`T | evaluate autocluster(`[*SizeWeight*, *WeightColumn*, *NumSeeds*, *CustomWildcard*, *CustomWildcard*,...]`)`

Tous les arguments sont facultatifs, mais ils doivent alors être ordonnés comme ci-dessus. Pour indiquer que la valeur par défaut doit être utilisée, insérez le signe tilde - ’ ~’ (voir les exemples ci-dessous).

Arguments disponibles :

* SizeWeight-0 < *double* < 1 [par défaut : 0,5]

    Vous permet de contrôler l’équilibre entre le générique (couverture élevée) et l’informatif (nombreuses valeurs partagées). L’augmentation de la valeur réduit généralement le nombre de modèles, et chaque modèle a tendance à couvrir un pourcentage plus élevé. La diminution de la valeur produit généralement des modèles plus spécifiques avec davantage de valeurs partagées et un pourcentage de couverture moins élevé. Le sous la formule de capot est une moyenne géométrique pondérée entre le score générique normalisé et le score informatif avec `SizeWeight` et `1-SizeWeight` comme poids. 

    Exemple : `T | evaluate autocluster(0.8)`

* WeightColumn - *nom_colonne*

    Considère chaque ligne de l’entrée en fonction de la pondération spécifiée (par défaut, chaque ligne a une pondération de « 1 »). L’argument doit être un nom de colonne numérique (par exemple, int, long, real). Il est courant d’utiliser une colonne de pondération en prenant en compte l’échantillonnage ou la création de compartiments/l’agrégation des données déjà incorporées dans chaque ligne.
    
    Exemple : `T | evaluate autocluster('~', sample_Count)` 

* NumSeeds- *int* [par défaut : 25] 

    Le nombre de valeurs initiales détermine le nombre de points de recherche locaux initiaux de l’algorithme. Dans certains cas, selon la structure des données, l’augmentation du nombre de valeurs initiales augmente le nombre (ou la qualité) des résultats par le biais d’un espace de recherche plus important avec un compromis de requête plus lent. La valeur a réduit les résultats dans les deux sens, si bien que sa diminution est inférieure à 5 pour obtenir des améliorations de performances négligeables et une augmentation de la valeur supérieure à 50 générera rarement des modèles supplémentaires.

    Exemple : `T | evaluate autocluster('~', '~', 15)`

* CustomWildcard- *« any_value_per_type »*

    Définit la valeur de caractère générique pour un type spécifique dans la table de résultats qui indique que le modèle actuel ne présente pas de restriction sur cette colonne.
    La valeur par défaut est null, la chaîne par défaut est une chaîne vide. Si la valeur par défaut est une valeur viable dans les données, une autre valeur de caractère générique doit être utilisée (par exemple, `*` ).

    Exemple : `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|ID de segment|Count|Pourcentage|État|Type d’événement|Dommage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7||Grêle|Non
|1|512|8,7||Vent d’orage|YES
|2|898|15,3|TEXAS||

**Exemple avec des caractères génériques personnalisés**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|ID de segment|Count|Pourcentage|État|Type d’événement|Dommage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7|\*|Grêle|Non
|1|512|8,7|\*|Vent d’orage|YES
|2|898|15,3|TEXAS|\*|\*

**Voir aussi**

* [basket](./basketplugin.md)
* [abaisse](./reduceoperator.md)

**Informations supplémentaires**

* AutoCluster repose en grande partie sur l’algorithme Seed-Expand décrit dans le document suivant : [Algorithms for Telemetry Data Mining using Discrete Attributes](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1). 


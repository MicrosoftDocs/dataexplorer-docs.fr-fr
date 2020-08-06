---
title: plug-in diffpatterns-Azure Explorateur de données
description: Cet article décrit le plug-in diffpatterns dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0be0dc12f48723bc83376a36db04f764991f7f0d
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803095"
---
# <a name="diff-patterns-plugin"></a>plug-in de modèles diff

Compare deux jeux de données de la même structure et recherche les modèles d’attributs discrets (dimensions) qui caractérisent les différences entre les deux jeux de données.
 `Diffpatterns`a été développé pour aider à analyser les défaillances (par exemple, en comparant les échecs aux échecs dans un laps de temps donné), mais peut éventuellement trouver des différences entre deux jeux de données de la même structure. 

```kusto
T | evaluate diffpatterns(splitColumn)
```
> [!NOTE]
> `diffpatterns`vise à trouver des modèles significatifs (qui capturent des parties de la différence de données entre les jeux) et n’est pas destiné aux différences ligne par ligne.

## <a name="syntax"></a>Syntaxe

`T | evaluate diffpatterns(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

## <a name="arguments"></a>Arguments 

### <a name="required-arguments"></a>Arguments obligatoires

* SplitColumn - *nom_colonne*

    Indique à l’algorithme comment fractionner la requête en jeux de données. Selon les valeurs spécifiées pour les arguments SplitValueA et SplitValueB (voir ci-dessous), l’algorithme fractionne la requête en deux jeux de données, « A » et « B », et analyse les différences entre ceux-ci. Par conséquent, la colonne fractionnée doit comprendre au moins deux valeurs distinctes.

* SplitValueA - *string*

    Représentation sous forme de chaîne de l’une des valeurs dans la SplitColumn spécifiée. Toutes les lignes qui ont cette valeur dans leur SplitColumn sont considérées comme étant le jeu de données « A ».

* SplitValueB - *string*

    Représentation sous forme de chaîne de l’une des valeurs dans la SplitColumn spécifiée. Toutes les lignes qui ont cette valeur dans leur SplitColumn considéré comme le jeu de données « B ».

    Exemple : `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

### <a name="optional-arguments"></a>Arguments facultatifs

Tous les autres arguments sont facultatifs, mais ils doivent alors être ordonnés comme ci-dessous. Pour indiquer que la valeur par défaut doit être utilisée, insérez le signe tilde - ’ ~’ (voir les exemples ci-dessous).

* WeightColumn - *nom_colonne*

    Considère chaque ligne de l’entrée en fonction de la pondération spécifiée (par défaut, chaque ligne a une pondération de « 1 »). L’argument doit être un nom de colonne numérique (par exemple,, `int` , `long` `real` ).
    Il est courant d’utiliser une colonne de pondération en prenant en compte l’échantillonnage ou la création de compartiments/l’agrégation des données déjà incorporées dans chaque ligne.
    
    Exemple : `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* Seuil-0,015 < *double* < 1 [par défaut : 0,05]

    Définit la différence minimale de modèle (taux) entre les deux jeux.

    Exemple : `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* MaxDimensions-0 < *int* [default : Unlimited]

    Définit le nombre maximal de dimensions qui ne sont pas corrélées par modèle de résultat. En spécifiant une limite, vous diminuez l’exécution de la requête.

    Exemple : `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* CustomWildcard- *« tout-valeur-par-type »*

    Définit la valeur de caractère générique pour un type spécifique dans la table de résultats qui indique que le modèle actuel ne présente pas de restriction sur cette colonne.
    La valeur par défaut est null, la chaîne par défaut est une chaîne vide. Si la valeur par défaut est une valeur viable dans les données, une autre valeur de caractère générique doit être utilisée (par exemple, `*` ).
    Reportez-vous à l’exemple ci-dessous.

    Exemple : `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="returns"></a>Retours

`Diffpatterns`retourne un petit ensemble de modèles qui capturent différentes parties des données dans les deux jeux (autrement dit, un modèle capturant un grand pourcentage de lignes dans le premier jeu de données et un pourcentage faible des lignes du deuxième jeu). Chaque modèle est représenté par une ligne dans les résultats.

Le résultat de `diffpatterns` retourne les colonnes suivantes :

* Segment : identité assignée au modèle dans la requête actuelle (Remarque : les ID ne sont pas forcément identiques dans les requêtes répétées).

* Counta : nombre de lignes capturées par le modèle dans le jeu A (le jeu a est l’équivalent de `where tostring(splitColumn) == SplitValueA` ).

* CountB : nombre de lignes capturées par le modèle dans le jeu B (le jeu B est l’équivalent de `where tostring(splitColumn) == SplitValueB` ).

* Pourcentagea : pourcentage de lignes d’un ensemble capturé par le modèle (100,0 * counta/Count (définis)).

* PercentB : pourcentage de lignes dans le jeu B capturé par le modèle (100,0 * CountB/Count (SetB)).

* PercentDiffAB : la différence absolue du point de pourcentage entre A et B (| Pourcentagea-PercentB |) est la mesure principale de l’importance des modèles dans la description de la différence entre les deux jeux.

* Autres colonnes : le schéma d’origine de l’entrée et la description du modèle, chaque ligne (modèle) renouvelit l’intersection des valeurs non génériques des colonnes (équivalent de `where col1==val1 and col2==val2 and ... colN=valN` pour chaque valeur non générique de la ligne).

Pour chaque modèle, les colonnes qui ne sont pas définies dans le modèle (autrement dit, sans restriction sur une valeur spécifique) contiennent une valeur générique qui est NULL par défaut. Consultez dans la section arguments ci-dessous comment les caractères génériques peuvent être modifiés manuellement.

* Remarque : les modèles ne sont pas souvent distincts. Ils peuvent se chevaucher et ne couvrent généralement pas toutes les lignes d’origine. Certaines lignes peuvent n’appartenir à aucun modèle.

> [!TIP]
> * Utilisez [Where](./whereoperator.md) et [Project](./projectoperator.md) dans le canal d’entrée pour réduire les données uniquement à ce qui vous intéresse.
> * Lorsque vous trouvez une ligne intéressante, vous pouvez l’explorer plus en détail en ajoutant ses valeurs spécifiques à votre filtre `where` .

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```

|ID de segment|CountA|CountB|PercentA|PercentB|PercentDiffAB|State|Type d’événement|Source|Récoltes|
|---|---|---|---|---|---|---|---|---|---|
|0|2278|93|49,8|7.1|42,7||Grêle||0|
|1|779|512|17,03|39,08|22,05||Vent d’orage|||
|2|1098|118|24,01|9,01|15|||Observateur chevronné|0|
|3|136|158|2,97|12,06|9,09|||Journal||
|4|359|214|7,85|16,34|8,49||Crue soudaine|||
|5|50|122|1,09|9,31|8,22|IOWA||||
|6|655|279|14,32|21,3|6,98|||Respect des lois||
|7|150|117|3,28|8,93|5,65||Crue|||
|8|362|176|7,91|13,44|5,52|||Gestionnaire des urgences||

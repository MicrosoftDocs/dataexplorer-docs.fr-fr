---
title: plugin diffpatterns - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le plugin diffpatterns dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fc4d1c7441e02eeeb1d90e1f8c0f2a521e3793da
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515995"
---
# <a name="diffpatterns-plugin"></a>plugin diffpatterns

```kusto
T | evaluate diffpatterns(splitColumn)
```
Compare deux ensembles de données d’une même structure et trouve des modèles d’attributs discrets (dimensions) qui caractérisent les différences entre les deux ensembles de données. Diffpatterns a été développé pour analyser les échecs (par exemple, en comparant les échecs et l’absence d’échecs sur une période donnée), mais peut éventuellement rechercher les différences entre deux jeux de données quelconques de la même structure. 

**Syntaxe**

`T | evaluate diffpatterns(`SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...]`)` 

**Arguments requis**

* SplitColumn - *nom_colonne*

    Indique à l’algorithme comment fractionner la requête en jeux de données. Selon les valeurs spécifiées pour les arguments SplitValueA et SplitValueB (voir ci-dessous), l’algorithme fractionne la requête en deux jeux de données, « A » et « B », et analyse les différences entre ceux-ci. Par conséquent, la colonne fractionnée doit comprendre au moins deux valeurs distinctes.

* SplitValueA - *string*

    Représentation sous forme de chaîne de l’une des valeurs dans la SplitColumn spécifiée. Toutes les lignes contenant cette valeur dans leur SplitColumn sont considérées comme constituant un jeu de données « A ».

* SplitValueB - *string*

    Représentation sous forme de chaîne de l’une des valeurs dans la SplitColumn spécifiée. Toutes les lignes qui ont cette valeur dans leur SplitColumn considéré comme l’ensemble de données "B".

    Exemple : `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

**Arguments facultatifs**

Tous les autres arguments sont facultatifs, mais ils doivent alors être ordonnés comme ci-dessous. Pour indiquer que la valeur par défaut doit être utilisée, insérez le signe tilde - ’ ~’ (voir les exemples ci-dessous).

* WeightColumn - *nom_colonne*

    Considère chaque ligne de l’entrée en fonction de la pondération spécifiée (par défaut, chaque ligne a une pondération de « 1 »). L’argument doit être un nom de colonne numérique (par exemple, int, long, real).
    Il est courant d’utiliser une colonne de pondération en prenant en compte l’échantillonnage ou la création de compartiments/l’agrégation des données déjà incorporées dans chaque ligne.
    
    Exemple : `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* Seuil - 0,015 < *double* < 1 [par défaut: 0,05]

    Définit la différence minimale de modèle (taux) entre les deux jeux.

    Exemple : `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* MaxDimensions - 0 < *int* [par défaut: illimité]

    Définit le nombre maximal de dimensions non corrélées par modèle de résultat. La spécification d’une limite réduit l’exécution de la requête.

    Exemple : `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* CustomWildcard - *"n’importe quelle valeur par type"*

    Définit la valeur de caractère générique pour un type spécifique dans la table de résultats qui indique que le modèle actuel ne présente pas de restriction sur cette colonne.
    La valeur par défaut est null, la chaîne par défaut est une chaîne vide. Si la valeur par défaut est une valeur viable dans les données, `*`une valeur wildcard différente doit être utilisée (p. ex. ).
    Reportez-vous à l’exemple ci-dessous.

    Exemple : `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

**Retourne**

Diffpatterns retourne un jeu de modèles (généralement petit) qui capturent différentes parties des données dans les deux jeux (par exemple, un modèle qui capture un fort pourcentage des lignes dans le premier jeu de données et un faible pourcentage des lignes dans le deuxième jeu). Chaque modèle est représenté par une ligne dans les résultats.

Le résultat des diffpatternes renvoie les colonnes suivantes :

* SegmentId: l’id attribué au modèle dans la requête actuelle (note: les ID ne sont pas garantis d’être les mêmes dans les requêtes répétées).

* CountA: le nombre de lignes capturées par le modèle dans `where tostring(splitColumn) == SplitValueA`le set A (Set A est l’équivalent de ).

* CountB: le nombre de lignes capturées par le modèle dans `where tostring(splitColumn) == SplitValueB`l’ensemble B (Set B est l’équivalent de ).

* PercentA: le pourcentage de lignes dans le set A capturé par le modèle ( 100,0 - CountA / compte (SetA) ).

* PercentB: le pourcentage de lignes dans l’ensemble B capturé par le modèle ( 100,0 - CountB / compte (SetB) ).

* PercentDiffAB: l’écart de point de pourcentage absolu entre A et B ( PercentA - PercentB ) est la principale mesure de signification des modèles dans la description de la différence entre les deux ensembles.

* Reste des colonnes : sont le schéma original de l’entrée et décrivent le motif, chaque rangée (modèle) envient `where col1==val1 and col2==val2 and ... colN=valN` l’intersection des valeurs non wildcard des colonnes (équivalent de chaque valeur non-wildcard dans la rangée).

Pour chaque modèle, les colonnes qui ne sont pas définies dans le modèle (c.-à-d. sans restriction sur une valeur spécifique) contiendront une valeur wildcard qui est nulle par défaut (voir dans la section Arguments ci-dessous comment les wildcards peuvent être modifiés manuellement).

* Remarque : les motifs ne sont généralement pas distincts, ils peuvent se chevaucher et ne couvrent généralement pas toutes les lignes d’origine. Certaines lignes peuvent n’appartenir à aucun modèle.


**Conseils**

Utilisez [où](./whereoperator.md) et [projetez](./projectoperator.md) dans le tuyau d’entrée pour réduire les données à ce qui vous intéresse.

Lorsque vous trouvez une ligne intéressante, vous pouvez l’explorer plus en détail en ajoutant ses valeurs spécifiques à votre filtre `where` .

* Remarque : les diffpatdes visent à trouver des modèles significatifs (qui capturent des parties de la différence de données entre les ensembles) et ne sont pas destinés aux différences rangée par rangée.

**Exemple**

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


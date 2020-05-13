---
title: opérateur supérieur imbriqué-Azure Explorateur de données
description: Cet article décrit l’opérateur Top imbriqué dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87606442dcc5d7c9e7e0fceec379c37169757c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370767"
---
# <a name="top-nested-operator"></a>Opérateur top-nested

produit les premiers résultats hiérarchiques, où chaque niveau est une vue détaillée des valeurs du niveau précédent. 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

Il est utile pour les scénarios de visualisation de tableau de bord, ou lorsqu’il est nécessaire de répondre à une question qui ressemble à : «Rechercher les valeurs Top-N de K1 (à l’aide d’une agrégation); pour chacune d’elles, recherchez les valeurs les plus importantes de la fonction M (à l’aide d’une autre agrégation). ..."

**Syntaxe**

*T* `|` `top-nested` [*N1*] `of` *expression1* [ `with others=` *ConstExpr1*] `by` [*AggName1* `=` ] *Aggregation1* [ `asc`  |  `desc` ] [ `,` ...]

**Arguments**

pour chaque règle imbriquée de niveau supérieur :
* *N1*: nombre de premières valeurs à retourner pour chaque niveau de la hiérarchie. Facultatif (en cas d’omission, toutes les valeurs distinctes seront retournées).
* *Expression1*: expression par laquelle sélectionner les valeurs supérieures. En général, il s’agit d’un nom de colonne dans *T*, ou d’une opération compartimentage (par exemple, `bin()` ) sur une telle colonne. 
* *ConstExpr1*: si elle est spécifiée, pour le niveau d’imbrication applicable, une ligne supplémentaire est ajoutée, qui contient le résultat agrégé pour les autres valeurs qui ne sont pas incluses dans les valeurs supérieures.
* *Aggregation1*: appel à une fonction d’agrégation qui peut être l’une des suivantes : [Sum ()](sum-aggfunction.md), [Count ()](count-aggfunction.md), [Max ()](max-aggfunction.md), [min ()](min-aggfunction.md), [DCount ()](dcountif-aggfunction.md), [AVG (](avg-aggfunction.md)), [centile ()](percentiles-aggfunction.md), [percentilew (](percentiles-aggfunction.md)) ou n’importe quelle combinaison de algebric de ces agrégations.
* `asc` ou `desc` (valeur par défaut) peut s’afficher pour indiquer si la sélection provient du bas ou du haut de la plage.

**Retourne**

Une table Hierarchial qui inclut des colonnes d’entrée et pour chacune une nouvelle colonne est produite pour inclure le résultat de l’agrégation pour le même niveau pour chaque élément.
Les colonnes sont organisées dans le même ordre que les colonnes d’entrée et la nouvelle colonne produite est proche de la colonne agrégée. Chaque enregistrement a une structure Hierarchial où chaque valeur est sélectionnée après l’application de toutes les règles imbriquées précédentes sur tous les niveaux précédents, puis l’application de la règle du niveau actuel sur cette sortie.
Cela signifie que les n premières valeurs du niveau i sont calculées pour chaque valeur du niveau i-1.
 
**Conseils**

* Utiliser le changement de nom des colonnes dans pour les résultats de l' *agrégation* : T | Top-imbriqué 3 de l’emplacement par MachinesNumberForLocation = Sum (MachinesNumber)...

* Le nombre d’enregistrements renvoyés peut être assez important. jusqu’à (*N1*+ 1) \* (*N2*+ 1) \* ... \* (*nm*+ 1) (où m est le nombre de niveaux et *ni* est le nombre supérieur pour le niveau i).

* L’agrégation doit recevoir une colonne numérique avec une fonction d’agrégation qui est l’une des mentionnées ci-dessus.

* Utilisez l' `with others=` option pour récupérer la valeur agrégée de toutes les autres valeurs qui ne sont pas des N premières valeurs dans un certain niveau.

* Si vous n’êtes pas intéressé par l’obtention `with others=` d’un certain niveau, les valeurs NULL sont ajoutées (pour la colonne aggreagated et la clé de niveau, consultez l’exemple ci-dessous).


* Il est possible de retourner des colonnes supplémentaires pour les candidats imbriqués sélectionnés en ajoutant des instructions imbriquées supplémentaires comme celles-ci (voir les exemples ci-dessous) :

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|État|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|KANSAS|87771.2355000001|Respect des lois|18744,823|FT SCOTT|264,858|
|KANSAS|87771.2355000001|Public|22855,6206|BUCKLIN|488,2457|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|
|TEXAS|123400,5101|Public|13650,9079|AMARILLO|246,2598|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|
|TEXAS|123400,5101|Observateur chevronné|13997,7124|CLAUDE|421,44|


* Avec d’autres exemples :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|État|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|KANSAS|87771.2355000001|Respect des lois|18744,823|FT SCOTT|264,858|
|KANSAS|87771.2355000001|Public|22855,6206|BUCKLIN|488,2457|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|
|TEXAS|123400,5101|Public|13650,9079|AMARILLO|246,2598|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|
|TEXAS|123400,5101|Observateur chevronné|13997,7124|CLAUDE|421,44|
|KANSAS|87771.2355000001|Respect des lois|18744,823|Tous les autres emplacements de fin|18479,965|
|KANSAS|87771.2355000001|Public|22855,6206|Tous les autres emplacements de fin|22367,3749|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|Tous les autres emplacements de fin|20890,9679|
|TEXAS|123400,5101|Public|13650,9079|Tous les autres emplacements de fin|13404,6481|
|TEXAS|123400,5101|Respect des lois|37228,5966|Tous les autres emplacements de fin|36939,2788|
|TEXAS|123400,5101|Observateur chevronné|13997,7124|Tous les autres emplacements de fin|13576,2724|
|KANSAS|87771.2355000001|||Tous les autres emplacements de fin|24891,0836|
|TEXAS|123400,5101|||Tous les autres emplacements de fin|58523.2932000001|
|Tous les autres États|1149279,5923|||Tous les autres emplacements de fin|1149279,5923|


La requête suivante affiche les mêmes résultats pour le premier niveau utilisé dans l’exemple ci-dessus :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279,5923|


Demande d’une autre colonne (EventType) au résultat supérieur imbriqué : 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|État|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|Type d’événement|
|---|---|---|---|---|---|---|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|Vent d’orage|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|Grêle|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|Tornade|
|KANSAS|87771.2355000001|Public|22855,6206|BUCKLIN|488,2457|Grêle|
|KANSAS|87771.2355000001|Public|22855,6206|BUCKLIN|488,2457|Vent d’orage|
|KANSAS|87771.2355000001|Public|22855,6206|BUCKLIN|488,2457|Crue|
|TEXAS|123400,5101|Observateur chevronné|13997,7124|CLAUDE|421,44|Grêle|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|Grêle|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|Crue|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|Crue soudaine|

Pour trier le résultat par le dernier niveau imbriqué (dans cet exemple par EndLocation) et donner un ordre de tri d’index pour chaque valeur dans ce niveau (par groupe) :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|État|Source|EndLocations|endLocationSums|indices|
|---|---|---|---|---|
|TEXAS|Observateur chevronné|CLAUDE|421,44|0|
|TEXAS|Observateur chevronné|AMARILLO|316,8892|1|
|TEXAS|Observateur chevronné|DALHART|252,6186|2|
|TEXAS|Observateur chevronné|PERRYTON|216,7826|3|
|TEXAS|Respect des lois|PERRYTON|289,3178|0|
|TEXAS|Respect des lois|LEAKEY|267,9825|1|
|TEXAS|Respect des lois|BRACKETTVILLE|264,3483|2|
|TEXAS|Respect des lois|GILMER|261,9068|3|
|KANSAS|Observateur chevronné|SHARON SPGS|388,7404|0|
|KANSAS|Observateur chevronné|ATWOOD|358,6136|1|
|KANSAS|Observateur chevronné|LENORA|317,0718|2|
|KANSAS|Observateur chevronné|VILLE DE SCOTT|307,84|3|
|KANSAS|Public|BUCKLIN|488,2457|0|
|KANSAS|Public|ASHLAND|446,4218|1|
|KANSAS|Public|PROTÉGER|446,11|2|
|KANSAS|Public|PARC D’ÉTATS MEADE|371,1|3|

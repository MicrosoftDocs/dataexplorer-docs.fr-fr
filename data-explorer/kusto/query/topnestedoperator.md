---
title: opérateur supérieur imbriqué-Azure Explorateur de données
description: Cet article décrit l’opérateur Top imbriqué dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ce56040e2135a455e29a8ff0ce83d832cbf5c5f7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243726"
---
# <a name="top-nested-operator"></a>Opérateur top-nested

Produit une agrégation hiérarchique et une sélection des valeurs principales, où chaque niveau est un raffinement du précédent.

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

L' `top-nested` opérateur accepte les données tabulaires comme entrée et une ou plusieurs clauses d’agrégation.
La première clause Aggregation (la plus à gauche) divise les enregistrements d’entrée en partitions, en fonction des valeurs uniques d’une expression sur ces enregistrements. La clause conserve ensuite un certain nombre d’enregistrements qui agrandissent ou réduisent cette expression sur les enregistrements. La clause Aggregation suivante applique ensuite une fonction similaire, de manière imbriquée. Chaque clause suivante est appliquée à la partition produite par la clause précédente. Ce processus se poursuit pour toutes les clauses d’agrégation.

Par exemple, l' `top-nested` opérateur peut être utilisé pour répondre à la question suivante : «pour une table contenant les chiffres des ventes, tels que le pays, le vendeur et le montant vendu : Quels sont les cinq premiers pays par vente ? Quels sont les trois principaux représentants de chacun de ces pays ?»

## <a name="syntax"></a>Syntaxe

*T* `|` `top-nested` *TopNestedClause2* [ `,` *TopNestedClause2*...]

Où *TopNestedClause* a la syntaxe suivante :

[*N*] `of` [ *`ExprName`* `=` ] *`Expr`* [ `with` `others` `=` *`ConstExpr`* ] `by` [ *`AggName`* `=` ] *`Aggregation`* `asc`  |  `desc` []

## <a name="arguments"></a>Arguments

Pour chaque *TopNestedClause*:

* *`N`*: Littéral de type `long` indiquant le nombre de premières valeurs à retourner pour ce niveau de hiérarchie.
  En cas d’omission, toutes les valeurs distinctes sont retournées.

* *`ExprName`*: Si ce paramètre est spécifié, définit le nom de la colonne de sortie correspondant aux valeurs de *`Expr`* .

* *`Expr`*: Expression sur l’enregistrement d’entrée indiquant la valeur à retourner pour ce niveau de hiérarchie.
  En général, il s’agit d’une référence de colonne pour l’entrée tabulaire (*T*), ou d’un calcul (tel que `bin()` ) sur une telle colonne.

* *`ConstExpr`*: Si elle est spécifiée, pour chaque niveau de hiérarchie, 1 enregistrement est ajouté avec la valeur qui est l’agrégation sur tous les enregistrements qui n’ont pas été « en fait le haut ».

* *`AggName`*: Si ce paramètre est spécifié, cet identificateur définit le nom de colonne dans la sortie pour la valeur d' *agrégation*.

* *`Aggregation`*: Expression numérique indiquant l’agrégation à appliquer à tous les enregistrements partageant la même valeur de *`Expr`* . La valeur de cette agrégation détermine quel enregistrement obtenu est « Top ».
  
  Les fonctions d’agrégation suivantes sont prises en charge :
   * [Sum ()](sum-aggfunction.md),
   * [Count ()](count-aggfunction.md),
   * [Max ()](max-aggfunction.md),
   * [min ()](min-aggfunction.md),
   * [DCount ()](dcountif-aggfunction.md),
   * [AVG ()](avg-aggfunction.md),
   * [centile ()](percentiles-aggfunction.md)et
   * [percentilew ()](percentiles-aggfunction.md). Toute combinaison algébrique des agrégations est également prise en charge.

* `asc` ou `desc` (la valeur par défaut) peut sembler pour contrôler si la sélection est en fait du « bas » ou « supérieur » de la plage de valeurs agrégées.

## <a name="returns"></a>Retours

Cet opérateur retourne une table qui comporte deux colonnes pour chaque clause Aggregation :

* Une colonne contient les valeurs distinctes du calcul de la clause *`Expr`* (avec le nom de colonne *ExprName* si elle est spécifiée)

* Une colonne contient le résultat du calcul de l' *agrégation* (avec le nom de colonne *AggregationName* si elle est spécifiée)

## <a name="notes"></a>Notes

Les colonnes d’entrée qui ne sont pas spécifiées comme *`Expr`* valeurs ne sont pas générées.
Pour obtenir toutes les valeurs à un certain niveau, ajoutez un nombre d’agrégations qui :

* Omet la valeur de *N*
* Utilise le nom de colonne comme valeur de *`Expr`*
* Utilise `Ignore=max(1)` comme agrégation, puis ignore (ou projeter) la colonne `Ignore` .

Le nombre d’enregistrements peut croître de façon exponentielle avec le nombre de clauses d’agrégation ((N1 + 1) \* (N2 + 1). \* ..). La croissance des enregistrements est encore plus rapide si aucune limite *n* n’est spécifiée. Prenez en compte que cet opérateur peut consommer une quantité considérable de ressources.

Si la distribution de l’agrégation est considérablement non uniforme, limitez le nombre de valeurs distinctes à retourner (à l’aide de *N*) et utilisez l' `with others=` option *ConstExpr* pour obtenir une indication du « poids » de tous les autres cas.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|KANSAS|87771.2355000001|Respect des lois|18744,823|FT SCOTT|264,858|
|KANSAS|87771.2355000001|Publiques|22855,6206|BUCKLIN|488,2457|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|
|TEXAS|123400,5101|Publiques|13650,9079|AMARILLO|246,2598|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|
|TEXAS|123400,5101|Observateur chevronné|13997,7124|CLAUDE|421,44|

Utilisez l’option « avec d’autres » :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|KANSAS|87771.2355000001|Respect des lois|18744,823|FT SCOTT|264,858|
|KANSAS|87771.2355000001|Publiques|22855,6206|BUCKLIN|488,2457|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|
|TEXAS|123400,5101|Publiques|13650,9079|AMARILLO|246,2598|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|
|TEXAS|123400,5101|Observateur chevronné|13997,7124|CLAUDE|421,44|
|KANSAS|87771.2355000001|Respect des lois|18744,823|Tous les autres emplacements de fin|18479,965|
|KANSAS|87771.2355000001|Publiques|22855,6206|Tous les autres emplacements de fin|22367,3749|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|Tous les autres emplacements de fin|20890,9679|
|TEXAS|123400,5101|Publiques|13650,9079|Tous les autres emplacements de fin|13404,6481|
|TEXAS|123400,5101|Respect des lois|37228,5966|Tous les autres emplacements de fin|36939,2788|
|TEXAS|123400,5101|Observateur chevronné|13997,7124|Tous les autres emplacements de fin|13576,2724|
|KANSAS|87771.2355000001|||Tous les autres emplacements de fin|24891,0836|
|TEXAS|123400,5101|||Tous les autres emplacements de fin|58523.2932000001|
|Tous les autres États|1149279,5923|||Tous les autres emplacements de fin|1149279,5923|

La requête suivante affiche les mêmes résultats pour le premier niveau utilisé dans l’exemple ci-dessus.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279,5923|


Demandez une autre colonne (EventType) au résultat supérieur imbriqué.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|State|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|Type d’événement|
|---|---|---|---|---|---|---|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|Vent d’orage|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|Grêle|
|KANSAS|87771.2355000001|Observateur chevronné|21279,7083|SHARON SPGS|388,7404|Tornade|
|KANSAS|87771.2355000001|Publiques|22855,6206|BUCKLIN|488,2457|Grêle|
|KANSAS|87771.2355000001|Publiques|22855,6206|BUCKLIN|488,2457|Vent d’orage|
|KANSAS|87771.2355000001|Publiques|22855,6206|BUCKLIN|488,2457|Crue|
|TEXAS|123400,5101|Observateur chevronné|13997,7124|CLAUDE|421,44|Grêle|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|Grêle|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|Crue|
|TEXAS|123400,5101|Respect des lois|37228,5966|PERRYTON|289,3178|Crue soudaine|

Donnez un ordre de tri d’index pour chaque valeur de ce niveau (par groupe) pour trier le résultat par le dernier niveau imbriqué (dans cet exemple, par EndLocation) :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|Source|EndLocations|endLocationSums|index|
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
|KANSAS|Publiques|BUCKLIN|488,2457|0|
|KANSAS|Publiques|ASHLAND|446,4218|1|
|KANSAS|Publiques|PROTÉGER|446,11|2|
|KANSAS|Publiques|PARC D’ÉTATS MEADE|371,1|3|

---
title: opérateur le mieux niché - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur le mieux imbriqué dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 296c36f4653bec971c71dc210008af7b0d98959a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505931"
---
# <a name="top-nested-operator"></a>Opérateur top-nested

produit les premiers résultats hiérarchiques, où chaque niveau est une vue détaillée des valeurs du niveau précédent. 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

Il est utile pour les scénarios de visualisation de tableau de bord, ou quand il est nécessaire de répondre à une question qui sonne comme: "trouver quelles sont les valeurs top-N de K1 (en utilisant une certaine agrégation); pour chacun d’eux, trouvez quelles sont les valeurs top-M de K2 (en utilisant une autre agrégation); ..."

**Syntaxe**

*T* `|` `desc``,``asc` |  `=` `by` `with others=` *N1* `of` *Expression1* *ConstExpr1* *Aggregation1* *AggName1* [ N1 ] Expression1 [ ConstExpr1 ] [ AggName1 ] Aggregation1 [ ] [ ...] `top-nested`

**Arguments**

pour chaque règle top-nested:
* *N1*: Le nombre de valeurs supérieures à revenir pour chaque niveau de hiérarchie. Facultative (si omis, toutes les valeurs distinctes seront retournées).
* *Expression1*: Une expression par laquelle choisir les valeurs supérieures. Typiquement, c’est soit un nom de colonne dans *T*, `bin()`ou une opération de binning (par exemple, ) sur une telle colonne. 
* *ConstExpr1*: Si spécifié, pour le niveau de nidification applicable, une rangée supplémentaire sera annexée qui détient le résultat agrégé pour les autres valeurs qui ne sont pas incluses dans les valeurs supérieures.
* *Agrégation1*: Appel à une fonction d’agrégation qui peut être l’un des: [sum()](sum-aggfunction.md), [compte()](count-aggfunction.md), [max ()](max-aggfunction.md), [min()](min-aggfunction.md), [dcount()](dcountif-aggfunction.md), [avg()](avg-aggfunction.md), [percentile()](percentiles-aggfunction.md), [percentilew()](percentiles-aggfunction.md), ou toute combinaison d’algues de ces agrégations.
* `asc` ou `desc` (valeur par défaut) peut s’afficher pour indiquer si la sélection provient du bas ou du haut de la plage.

**Retourne**

Tableau hiérarchial qui comprend des colonnes d’entrée et pour chacun une nouvelle colonne est produite pour inclure le résultat de l’agrégation pour le même niveau pour chaque élément.
Les colonnes sont disposées dans le même ordre des colonnes d’entrée et la nouvelle colonne produite sera proche de la colonne agrégée. Chaque enregistrement a une structure hiérarchielle où chaque valeur est sélectionnée après avoir appliqué toutes les règles supérieures précédentes sur tous les niveaux précédents, puis l’application de la règle du niveau actuel sur cette sortie.
Cela signifie que les valeurs n haut pour le niveau i sont calculés pour chaque valeur dans le niveau i - 1.
 
**Conseils**

* Utilisez des colonnes renommant pour les résultats *de l’agrégation* : T top-niché 3 de l’emplacement par MachinesNumberForLocation ' somme (MachinesNumber) ... .

* Le nombre de dossiers retournés pourrait être assez important; jusqu’à (*N1* \* 1) (*N2*1) \* ... \* (*Nm*1) (où m est le nombre des niveaux et *Ni* est le compte supérieur pour le niveau i).

* L’agrégation doit recevoir une colonne numérique avec fonction d’agrégation qui est l’une des mentionnées ci-dessus.

* Utilisez `with others=` l’option afin d’obtenir la valeur agrégée de toutes les autres valeurs qui n’était pas top valeurs N dans un certain niveau.

* Si vous n’êtes `with others=` pas intéressé à obtenir pour un certain niveau, les valeurs nulles seront annexées (pour la colonne aggreagated et la clé de niveau, voir l’exemple ci-dessous).


* Il est possible de retourner des colonnes supplémentaires pour les candidats sélectionnés les mieux-imbriqués en appending d’autres déclarations supérieures comme celles-ci (voir les exemples ci-dessous):

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**Exemple**

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|Kansas|87771.2355000001|Respect des lois|18744.823|FT SCOTT|264.858|
|Kansas|87771.2355000001|Public|22855.6206|BUCKLIN BUCKLIN|488.2457|
|Kansas|87771.2355000001|Observateur chevronné|21279.7083|SHARON SPGS|388.7404|
|TEXAS|123400.5101|Public|13650.9079|Amarillo|246.2598|
|TEXAS|123400.5101|Respect des lois|37228.5966|PERRYTON (EN)|289.3178|
|TEXAS|123400.5101|Observateur chevronné|13997.7124|Claude|421.44|


* Avec d’autres exemples :

```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|Kansas|87771.2355000001|Respect des lois|18744.823|FT SCOTT|264.858|
|Kansas|87771.2355000001|Public|22855.6206|BUCKLIN BUCKLIN|488.2457|
|Kansas|87771.2355000001|Observateur chevronné|21279.7083|SHARON SPGS|388.7404|
|TEXAS|123400.5101|Public|13650.9079|Amarillo|246.2598|
|TEXAS|123400.5101|Respect des lois|37228.5966|PERRYTON (EN)|289.3178|
|TEXAS|123400.5101|Observateur chevronné|13997.7124|Claude|421.44|
|Kansas|87771.2355000001|Respect des lois|18744.823|Tous les autres emplacements finaux|18479.965|
|Kansas|87771.2355000001|Public|22855.6206|Tous les autres emplacements finaux|22367.3749|
|Kansas|87771.2355000001|Observateur chevronné|21279.7083|Tous les autres emplacements finaux|20890.9679|
|TEXAS|123400.5101|Public|13650.9079|Tous les autres emplacements finaux|13404.6481|
|TEXAS|123400.5101|Respect des lois|37228.5966|Tous les autres emplacements finaux|36939.2788|
|TEXAS|123400.5101|Observateur chevronné|13997.7124|Tous les autres emplacements finaux|13576.2724|
|Kansas|87771.2355000001|||Tous les autres emplacements finaux|24891.0836|
|TEXAS|123400.5101|||Tous les autres emplacements finaux|58523.2932000001|
|Tous les autres États|1149279.5923|||Tous les autres emplacements finaux|1149279.5923|


La requête suivante affiche les mêmes résultats pour le premier niveau utilisé dans l’exemple ci-dessus :

```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


Demander une autre colonne (EventType) au résultat le mieux imbriqué : 

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|State|aggregated_State|Source|aggregated_Source|EndLocation|aggregated_EndLocation|Type d’événement|
|---|---|---|---|---|---|---|
|Kansas|87771.2355000001|Observateur chevronné|21279.7083|SHARON SPGS|388.7404|Vent d’orage|
|Kansas|87771.2355000001|Observateur chevronné|21279.7083|SHARON SPGS|388.7404|Grêle|
|Kansas|87771.2355000001|Observateur chevronné|21279.7083|SHARON SPGS|388.7404|Tornade|
|Kansas|87771.2355000001|Public|22855.6206|BUCKLIN BUCKLIN|488.2457|Grêle|
|Kansas|87771.2355000001|Public|22855.6206|BUCKLIN BUCKLIN|488.2457|Vent d’orage|
|Kansas|87771.2355000001|Public|22855.6206|BUCKLIN BUCKLIN|488.2457|Crue|
|TEXAS|123400.5101|Observateur chevronné|13997.7124|Claude|421.44|Grêle|
|TEXAS|123400.5101|Respect des lois|37228.5966|PERRYTON (EN)|289.3178|Grêle|
|TEXAS|123400.5101|Respect des lois|37228.5966|PERRYTON (EN)|289.3178|Crue|
|TEXAS|123400.5101|Respect des lois|37228.5966|PERRYTON (EN)|289.3178|Crue soudaine|

Afin de trier le résultat par le dernier niveau imbriqué (dans cet exemple par EndLocation) et de donner un ordre de tri d’index pour chaque valeur dans ce niveau (par groupe) :

```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|Source|EndLocations|endLocationSums|indicies|
|---|---|---|---|---|
|TEXAS|Observateur chevronné|Claude|421.44|0|
|TEXAS|Observateur chevronné|Amarillo|316.8892|1|
|TEXAS|Observateur chevronné|Dalhart|252.6186|2|
|TEXAS|Observateur chevronné|PERRYTON (EN)|216.7826|3|
|TEXAS|Respect des lois|PERRYTON (EN)|289.3178|0|
|TEXAS|Respect des lois|Leakey|267.9825|1|
|TEXAS|Respect des lois|BRACKETTVILLE BRACKETTVILLE|264.3483|2|
|TEXAS|Respect des lois|Gilmer|261.9068|3|
|Kansas|Observateur chevronné|SHARON SPGS|388.7404|0|
|Kansas|Observateur chevronné|Atwood|358.6136|1|
|Kansas|Observateur chevronné|Lenora|317.0718|2|
|Kansas|Observateur chevronné|VILLE DE SCOTT|307.84|3|
|Kansas|Public|BUCKLIN BUCKLIN|488.2457|0|
|Kansas|Public|Ashland|446.4218|1|
|Kansas|Public|Protection|446.11|2|
|Kansas|Public|PARC D’ÉTAT DE MEADE|371.1|3|
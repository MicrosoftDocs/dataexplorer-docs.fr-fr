---
title: rejoindre l’opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de jointure dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 86d883c8d0214d278099260fa2406b91b776b4d1
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765814"
---
# <a name="join-operator"></a>opérateur join

fusionne les lignes de deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes spécifiées de chaque table.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**Syntaxe**

*LeftTable* `|` `join` [*JoinParameters*] `(` *RightTable* `)` `on` *Attributes*

**Arguments**

* *LeftTable*: La table **gauche** ou l’expression tabulaire (parfois appelée table **extérieure)** dont les lignes doivent être fusionnées. Dénoté `$left`comme .

* *RightTable*: La **bonne** table ou expression tabulaire (parfois appelée table*intérieure)* dont les lignes doivent être fusionnées. Dénoté `$right`comme .

* *Attributs*: Une ou plusieurs règles (séparées par virgule) qui décrivent comment les lignes de *LeftTable* sont appariées aux rangées de *RightTable*. Plusieurs règles sont évaluées à l’aide de l’opérateur `and` logique.
  Une règle peut être l’une des règles :

  |Type de règle        |Syntaxe                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |Égalité par nom |*ColumnName*                                    |`where`*LeftTable*. *ColumnName* `==` *RightTable*. *Nom de colonne*|
  |Égalité par valeur|`$left.`*LeftColumn* `==` `$right.` *RightColumn*|`where``$left.` *LeftColumn* `==` `$right.` *RightColumn*       |

> [!NOTE]
> En cas d'«égalité par valeur», les noms de *colonnes* doivent `$left` `$right` être qualifiés avec le tableau du propriétaire applicable dénoté par et notations.

* *JoinParameters*: Paramètres zéro ou plus (séparés dans l’espace) sous la forme de La valeur *nomine* `=` *Value* qui contrôle le comportement de l’opération de match en rangée et du plan d’exécution. Les paramètres suivants sont pris en charge : 

::: zone pivot="azuredataexplorer"

  |Nom           |Valeurs                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |Variantes de jointure|Voir [Join Flavors](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |Voir [Cross-Cluster Join](joincrosscluster.md)|
  |`hint.strategy`|Conseils d’exécution                               |Voir [les conseils de join](#join-hints)                |

::: zone-end

::: zone pivot="azuremonitor"

  |Nom           |Valeurs                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |Variantes de jointure|Voir [Join Flavors](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
  |`hint.strategy`|Conseils d’exécution                               |Voir [les conseils de join](#join-hints)                |

::: zone-end


> [!WARNING]
> La saveur par `kind` défaut joindre, `innerunique`si elle n’est pas spécifiée, est . C’est différent de certains autres `inner` produits d’analyse, qui ont comme saveur par défaut. Veuillez lire attentivement [ci-dessous](#join-flavors) pour comprendre les différences entre les différents types et pour vous assurer que la requête donne les résultats escomptés.

**Retourne**

Schéma de sortie dépend de la saveur de jointure:

 * `kind=leftanti`, `kind=leftsemi`:

     Le tableau de résultat contient des colonnes du côté gauche seulement.

     
 * `kind=rightanti`, `kind=rightsemi`:

     Le tableau de résultat contient des colonnes du côté droit seulement.

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes. Les colonnes du côté droit seront automatiquement renommées en cas de conflit de nom.

     
Les enregistrements de sortie dépendent de la saveur de jointure :

 * `kind=leftanti`, `kind=leftantisemi`

     Retourne tous les records du côté gauche qui n’ont pas de correspondances de la droite.     
     
 * `kind=rightanti`, `kind=rightantisemi`

     Retourne tous les records du côté droit qui n’ont pas de correspondances de la gauche.  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    Une ligne pour chaque correspondance entre les tables d’entrée. Un match est une rangée sélectionnée à partir d’une table qui a la même valeur pour tous les `on` champs comme une rangée dans l’autre table avec ces contraintes:

   - `kind`Quelconque`kind=innerunique`

    Une seule ligne du côté gauche correspond à chaque valeur de la clé `on` . La sortie contient une ligne pour chaque correspondance de cette ligne avec des lignes du côté droit.
    
   - `kind=leftsemi`
   
    Retourne tous les records du côté gauche qui ont des allumettes de la droite.
    
   - `kind=rightsemi`
   
       Retourne tous les records du côté droit qui ont des allumettes de la gauche.

   - `kind=inner`
 
    La sortie contient une ligne pour chaque combinaison de lignes correspondantes des côtés gauche et droit.

   - `kind=leftouter` (ou `kind=rightouter` ou `kind=fullouter`)

    Outre les correspondances internes, on trouve une ligne pour chaque ligne du côté gauche (et/ou droit), même s’il n’y a aucune correspondance. Dans ce cas, les cellules de sortie sans correspondance contiennent des valeurs null.
    Si plusieurs lignes comportent les mêmes valeurs pour ces champs, des lignes s’affichent pour toutes les combinaisons.

 

**Conseils**

Pour un résultat optimal :

* Si une table est toujours plus petite que l’autre, utilisez-la pour le côté gauche de la jointure.

**Exemple**

Obtenez les activités étendues d’un journal dans lequel certaines entrées marquent le début et la fin d’une activité. 

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityId, StartTime=timestamp
| join (Events
    | where Name == "Stop"
        | project StopTime=timestamp, ActivityId)
    on ActivityId
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityIdLeft = ActivityId, StartTime=timestamp
| join (Events
        | where Name == "Stop"
        | project StopTime=timestamp, ActivityIdRight = ActivityId)
    on $left.ActivityIdLeft == $right.ActivityIdRight
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

[Plus sur cet exemple](./samples.md#activities).

## <a name="join-flavors"></a>Variantes de jointure

La variante exacte de l’opérateur de jointure est spécifiée avec le mot clé kind. A partir d’aujourd’hui, Kusto prend en charge les saveurs suivantes de l’opérateur de jointure: 

|Type de jointure|Description|
|--|--|
|[`innerunique`](#default-join-flavor)(ou vide par défaut)|Joint intérieur avec la déduplication du côté gauche|
|[`inner`](#inner-join)|Joint intérieur standard|
|[`leftouter`](#left-outer-join)|Jointure externe gauche|
|[`rightouter`](#right-outer-join)|Jointure externe droite|
|[`fullouter`](#full-outer-join)|Adhésion extérieure complète|
|[`leftanti`](#left-anti-join), [`anti`](#left-anti-join) ou[`leftantisemi`](#left-anti-join)|Anti de gauche rejoindre|
|[`rightanti`](#right-anti-join)Ou[`rightantisemi`](#right-anti-join)|Droit anti rejoindre|
|[`leftsemi`](#left-semi-join)|Semi gauche rejoindre|
|[`rightsemi`](#right-semi-join)|Demi-joint droit|

### <a name="inner-and-innerunique-join-operator-flavors"></a>intérieur et innerunique joindre les saveurs de l’opérateur

-    Lors de l’utilisation **de la saveur intérieure de jointure**, il y aura une rangée dans la sortie pour chaque combinaison de lignes assorties de gauche et droite sans des suppressions de touches gauches. La sortie sera un produit cartésien des touches gauche et droite.
    Exemple de **jointure intérieure**:

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = inner
    t2
on key
```

|key|value|key1|valeur1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.1|1|val1.3|
|1|val1.2|1|val1.4|
|1|val1.1|1|val1.4|

-    L’utilisation **de la saveur de jointure intérieure** sera deduplicate touches du côté gauche et il y aura une rangée dans la sortie de chaque combinaison de touches gauches deduplicated et les touches droites.
    Exemple de **jointure innerunique** pour les mêmes jeux de données utilisés ci-dessus, Veuillez noter que **la saveur intérieure pour** ce cas peut produire deux sorties possibles et les deux sont correctes.
    Dans la première sortie, l’opérateur de jointure a choisi au hasard la première clé qui apparaît en t1 avec la valeur "val1.1" et l’a assortie avec des touches t2 tandis que dans la seconde, l’opérateur de jointure aléatoirement choisi la deuxième clé apparaît en t1 qui a la valeur "val1.2" et l’a assortie avec des touches t2:

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|key|value|key1|valeur1|
|---|---|---|---|
|1|val1.1|1|val1.3|
|1|val1.1|1|val1.4|

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|key|value|key1|valeur1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|


-    Kusto est optimisé pour pousser les `join` filtres qui viennent après le côté de joint approprié à gauche ou à droite lorsque c’est possible.
    Parfois, lorsque la saveur utilisée est **intérieure** et le filtre peut être propagé sur le côté gauche de la jointure, alors il sera propagé automatiquement et les touches qui s’appliquent à ce filtre apparaîtra toujours dans la sortie.
    par exemple, l’utilisation de ` where value == "val1.2" ` l’exemple ci-dessus et l’ajout de filtre donnera toujours le deuxième résultat et ne donnera jamais le premier résultat pour les jeux de données utilisés :

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
| where value == "val1.2"
```

|key|value|key1|valeur1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|
 
### <a name="default-join-flavor"></a>Par défaut joindre la saveur
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
Utilisons deux tableaux d’échantillons pour expliquer le fonctionnement de la jointure : 
 
Table X 

|Clé |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

Table Y 

|Clé |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 
 
La jointure par défaut effectue une jointure interne après déduplication du côté gauche de la clé de la jointure (la déduplication conserve le premier enregistrement). Soit l’instruction suivante : 

    X | join Y on Key 

Le côté gauche de la jointure (table X après déduplication) serait : 

|Clé |Value1 
|---|---
|a |1 
|b |2 
|c |4 

Tandis que le résultat de la jointure serait : 

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join Y on Key
```

|Clé|Value1|Clé1|Value2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|


(Notez que les touches 'a' et 'd' n’apparaissent pas dans la sortie, puisqu’il n’y avait pas de touches correspondantes sur les côtés gauche et droit). 
 
(Historiquement, il s’agissait de la première implémentation de la jointure soutenue par la version initiale de Kusto; elle est utile dans les scénarios typiques d’analyse des journaux/traces où nous voulons corréler deux événements (chacun correspondant à un certain critère de filtrage) selon la même pièce d’identité de corrélation, et récupérer toutes les apparences du phénomène que nous recherchons, ignorant les apparitions multiples des traces contributives.)
 
### <a name="inner-join"></a>Jointure interne

Il s’agit de la jointure interne standard connue du monde SQL. Un enregistrement de sortie est généré chaque fois qu’un enregistrement sur le côté gauche a la même clé de jointure que l’enregistrement sur le côté droit. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=inner Y on Key
```

|Clé|Value1|Clé1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

A noter que (b,10) venant du côté droit a été rejoint deux fois: avec les deux (b,2) et (b,3) sur la gauche; également (c,4) sur la gauche a été rejoint deux fois: avec les deux (c,20) et (c,30) sur la droite. 

### <a name="left-outer-join"></a>Jointure externe gauche 

Le résultat d’une jointure externe gauche pour les tables X et Y contient toujours tous les enregistrements de la table de gauche (X), même si la condition de jointure ne trouve pas d’enregistrements correspondants dans la table de droite (Y). 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftouter Y on Key
```

|Clé|Value1|Clé1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

 
### <a name="right-outer-join"></a>Jointure externe droite 

Elle ressemble à la jointure externe gauche, mais le traitement des tables est inversé. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightouter Y on Key
```

|Clé|Value1|Clé1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

 
### <a name="full-outer-join"></a>Adhésion extérieure complète 

Conceptuellement, une jointure externe complète combine l’application d’une jointure externe gauche et d’une jointure externe droite. Lorsque les enregistrements dans les tables jointes ne correspondent pas, l’ensemble de résultats aura des valeurs NULL pour chaque colonne de la table qui manque d’une ligne correspondante. Pour les enregistrements sans correspondance, une seule ligne est générée dans le jeu de résultats (les champs étant renseignés à partir des deux tables). 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=fullouter Y on Key
```

|Clé|Value1|Clé1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|
|a|1|||

 
### <a name="left-anti-join"></a>Anti de gauche rejoindre

Gauche anti rejoindre retourne tous les dossiers du côté gauche qui ne correspondent à aucun enregistrement du côté droit. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftanti Y on Key
```

|Clé|Value1|
|---|---|
|a|1|

L’anti-jointure modélise la requête « NOT IN ». 

### <a name="right-anti-join"></a>Droit anti rejoindre

Droite anti rejoindre retourne tous les dossiers du côté droit qui ne correspondent à aucun enregistrement du côté gauche. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightanti Y on Key
```

|Clé|Value2|
|---|---|
|d|40|

L’anti-jointure modélise la requête « NOT IN ». 

### <a name="left-semi-join"></a>Semi gauche rejoindre

La demi-finale gauche renvoie tous les records du côté gauche qui correspondent à un record du côté droit. Seules les colonnes du côté gauche sont retournées. 

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftsemi Y on Key
```

|Clé|Value1|
|---|---|
|b|3|
|b|2|
|c|4|

### <a name="right-semi-join"></a>Demi-joint droit

Demi-joint droit retourne tous les records du côté droit qui correspondent à un record du côté gauche. Seules les colonnes du côté droit sont retournées. 

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightsemi Y on Key
```

|Clé|Value2|
|---|---|
|b|10|
|c|20|
|c|30|


### <a name="cross-join"></a>Joint join

Kusto ne fournit pas nativement une saveur de jointure (c.-à-d., vous ne pouvez pas marquer l’opérateur avec `kind=cross`).
Il n’est pas difficile de simuler cela, cependant, en venant avec une clé factice:

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>Joignez-vous à des conseils

L’opérateur `join` prend en charge un certain nombre d’indices qui contrôlent la façon dont une requête s’exécute.
Ceux-ci ne changent pas `join`la sémantique de , mais peuvent affecter sa performance.

Joignez-vous aux conseils expliqués dans les articles suivants : 
* `hint.shufflekey=<key>`et `hint.strategy=shuffle`  -  [mélanger les requêtes](shufflequery.md)
* `hint.strategy=broadcast` - [diffuser rejoindre](broadcastjoin.md)
* `hint.remote=<strategy>` - [joint-cluster](joincrosscluster.md)

---
title: opérateur de jointure-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur de jointure dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2961f27226175fe81b7d9c82a6366b36134d805f
ms.sourcegitcommit: 31af2dfa75b5a2f59113611cf6faba0b45d29eb5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454114"
---
# <a name="join-operator"></a>opérateur join

fusionne les lignes de deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes spécifiées de chaque table.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**Syntaxe**

*LeftTable* `|` `join`[*JoinParameters*] `(` *RightTable* `)` `on` *attributs*

**Arguments**

* *LeftTable*: table de **gauche** ou expression tabulaire (parfois appelée table **externe** ) dont les lignes doivent être fusionnées. Désigné comme `$left` .

* *RightTable*: table ou expression tabulaire **appropriée** (parfois appelée « table*interne* ») dont les lignes doivent être fusionnées. Désigné comme `$right` .

* *Attributs*: une ou plusieurs règles (séparées par des virgules) qui décrivent comment les lignes de *LeftTable* sont mises en correspondance avec les lignes de *RightTable*. Plusieurs règles sont évaluées à l’aide de l' `and` opérateur logique.
  Une règle peut être l’une des suivantes :

  |Type de règle        |Syntaxe                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |Égalité par nom |*ColumnName*                                    |`where`*LeftTable*. *ColumnName* `==` *RightTable*. *ColumnName*|
  |Égalité par valeur|`$left.`*LeftColumn* `==` `$right.` *Rightcolumn*|`where``$left.` *LeftColumn* `==` LeftColumn `$right.` *Rightcolumn*       |

> [!NOTE]
> En cas d’égalité par valeur, les noms de colonnes *doivent* être qualifiés par la table de propriétaire applicable dénotée par `$left` les `$right` notations et.

* *JoinParameters*: zéro ou plusieurs paramètres (séparés par des espaces) sous la forme d’une valeur de *nom* `=` *Value* qui contrôlent le comportement de l’opération de correspondance des lignes et du plan d’exécution. Les paramètres suivants sont pris en charge : 

::: zone pivot="azuredataexplorer"

  |Nom           |Valeurs                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |Variantes de jointure|Voir [jointure de versions](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |Voir [jointure entre clusters](joincrosscluster.md)|
  |`hint.strategy`|Indicateurs d’exécution                               |Voir les [indicateurs de jointure](#join-hints)                |

::: zone-end

::: zone pivot="azuremonitor"

  |Nom           |Valeurs                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |Variantes de jointure|Voir [jointure de versions](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
  |`hint.strategy`|Indicateurs d’exécution                               |Voir les [indicateurs de jointure](#join-hints)                |

::: zone-end


> [!WARNING]
> La version de jointure par défaut, si `kind` n’est pas spécifiée, est `innerunique` . Cela diffère des autres produits d’analyse, qui ont `inner` comme version par défaut. Veuillez lire attentivement [ci-dessous](#join-flavors) pour comprendre les différences entre les différents types et pour vous assurer que la requête génère les résultats souhaités.

**Retourne**

Le schéma de sortie dépend de la version de jointure :

 * `kind=leftanti`, `kind=leftsemi`:

     La table de résultats contient uniquement des colonnes du côté gauche.

     
 * `kind=rightanti`, `kind=rightsemi`:

     La table de résultats contient uniquement des colonnes du côté droit.

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes. Les colonnes du côté droit seront automatiquement renommées en cas de conflit de nom.

     
Les enregistrements de sortie dépendent de la version de jointure :

 * `kind=leftanti`, `kind=leftantisemi`

     Retourne tous les enregistrements du côté gauche qui n’ont pas de correspondances à partir de la droite.     
     
 * `kind=rightanti`, `kind=rightantisemi`

     Retourne tous les enregistrements du côté droit qui n’ont pas de correspondances à partir de la gauche.  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    Une ligne pour chaque correspondance entre les tables d’entrée. Une correspondance est une ligne sélectionnée dans une table qui a la même valeur pour tous les `on` champs qu’une ligne de l’autre table avec les contraintes suivantes :

   - `kind`non spécifié`kind=innerunique`

    Une seule ligne du côté gauche correspond à chaque valeur de la clé `on` . La sortie contient une ligne pour chaque correspondance de cette ligne avec des lignes du côté droit.
    
   - `kind=leftsemi`
   
    Retourne tous les enregistrements du côté gauche qui ont des correspondances à partir de la droite.
    
   - `kind=rightsemi`
   
       Retourne tous les enregistrements du côté droit qui ont des correspondances à partir de la gauche.

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

En [savoir plus sur cet exemple](./samples.md#get-sessions-from-start-and-stop-events).

## <a name="join-flavors"></a>Variantes de jointure

La variante exacte de l’opérateur de jointure est spécifiée avec le mot clé kind. À l’heure actuelle, Kusto prend en charge les versions suivantes de l’opérateur de jointure : 

|Type de jointure|Description|
|--|--|
|[`innerunique`](#default-join-flavor)(ou vide par défaut)|Jointure interne avec déduplication côté gauche|
|[`inner`](#inner-join)|Jointure interne standard|
|[`leftouter`](#left-outer-join)|Jointure externe gauche|
|[`rightouter`](#right-outer-join)|Jointure externe droite|
|[`fullouter`](#full-outer-join)|Jointure externe complète|
|[`leftanti`](#left-anti-join), [`anti`](#left-anti-join) ou[`leftantisemi`](#left-anti-join)|Jointure anti gauche|
|[`rightanti`](#right-anti-join)ni[`rightantisemi`](#right-anti-join)|Anti-jointure Right|
|[`leftsemi`](#left-semi-join)|Semi-jointure gauche|
|[`rightsemi`](#right-semi-join)|Semi-jointure droite|

### <a name="inner-and-innerunique-join-operator-flavors"></a>versions d’opérateur de jointure interne et innerunique

-    Lors de l’utilisation de la **version de jointure interne**, il y aura une ligne dans la sortie pour chaque combinaison de lignes correspondantes de gauche à droite sans déduplication des clés de gauche. La sortie est un produit cartésien de clés de gauche et de droite.
    Exemple de **jointure interne**:

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
|1|Val 1.2|1|Val 1.3|
|1|Val 1.1|1|Val 1.3|
|1|Val 1.2|1|Val 1.4|
|1|Val 1.1|1|Val 1.4|

-    L’utilisation de la **version de jointure innerunique** déduplique les clés de gauche et il y aura une ligne dans la sortie de chaque combinaison de clés de gauche et de clé de droite dédupliquées.
    Exemple de **jointure innerunique** pour les mêmes jeux de données que ceux utilisés ci-dessus. Notez que la **version innerunique** pour ce cas de figure peut générer deux sorties possibles et les deux sont correctes.
    Dans la première sortie, l’opérateur de jointure a choisi de façon aléatoire la première clé qui apparaît dans T1 avec la valeur « Val 1.1 » et l’a mise en correspondance avec les clés T2 alors que dans la deuxième, l’opérateur de jointure a choisi de façon aléatoire la deuxième clé apparaît dans T1, qui a la valeur « Val 1.2 » et la correspond aux clés T2 :

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
|1|Val 1.1|1|Val 1.3|
|1|Val 1.1|1|Val 1.4|

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
|1|Val 1.2|1|Val 1.3|
|1|Val 1.2|1|Val 1.4|


-    Kusto est optimisé pour pousser les filtres qui viennent après le `join` vers le côté de jointure approprié à gauche ou à droite lorsque cela est possible.
    Parfois, lorsque la version utilisée est **innerunique** et que le filtre peut être propagé vers le côté gauche de la jointure, elle est automatiquement propagée et les clés qui s’appliquent à ce filtre apparaissent toujours dans la sortie.
    par exemple, l’exemple ci-dessus et l’ajout d’un filtre ` where value == "val1.2" ` donneront toujours le deuxième résultat et ne donneront jamais le premier résultat pour les jeux de données utilisés :

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
|1|Val 1.2|1|Val 1.3|
|1|Val 1.2|1|Val 1.4|
 
### <a name="default-join-flavor"></a>Version de jointure par défaut
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
Nous allons utiliser deux exemples de tables pour expliquer le fonctionnement de la jointure : 
 
Table X 

|Clé : |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

Table Y 

|Clé : |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 
 
La jointure par défaut effectue une jointure interne après déduplication du côté gauche de la clé de la jointure (la déduplication conserve le premier enregistrement). Soit l’instruction suivante : 

    X | join Y on Key 

Le côté gauche de la jointure (table X après déduplication) serait : 

|Clé : |Value1 
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

|Clé :|Value1|Key1|Value2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|


(Notez que les clés « a » et « d » n’apparaissent pas dans la sortie, car il n’y avait aucune clé correspondante sur les côtés gauche et droit). 
 
(Historiquement, il s’agissait de la première implémentation de la jointure prise en charge par la version initiale de Kusto ; elle est utile dans les scénarios d’analyse de journal/suivi typiques où nous voulons mettre en corrélation deux événements (chacun correspondant à un critère de filtrage) sous le même ID de corrélation, et récupérer toutes les apparences du phénomène que nous recherchons, en ignorant les apparences
 
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

|Clé :|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

Notez que (b, 10) du côté droit ont été joints deux fois : avec (b, 2) et (b, 3) à gauche ; en outre, (c, 4) à gauche a été joint deux fois : avec (c, 20) et (c, 30) à droite. 

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

|Clé :|Value1|Key1|Value2|
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

|Clé :|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

 
### <a name="full-outer-join"></a>Jointure externe complète 

Conceptuellement, une jointure externe complète combine l’application d’une jointure externe gauche et d’une jointure externe droite. Lorsque les enregistrements dans les tables jointes ne correspondent pas, le jeu de résultats contient des valeurs NULL pour chaque colonne de la table qui n’a pas de ligne correspondante. Pour les enregistrements sans correspondance, une seule ligne est générée dans le jeu de résultats (les champs étant renseignés à partir des deux tables). 
 
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

|Clé :|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|
|a|1|||

 
### <a name="left-anti-join"></a>Jointure anti gauche

Left anti-jointure retourne tous les enregistrements du côté gauche qui ne correspondent à aucun enregistrement du côté droit. 
 
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

|Clé :|Value1|
|---|---|
|a|1|

L’anti-jointure modélise la requête « NOT IN ». 

### <a name="right-anti-join"></a>Anti-jointure Right

Right anti-jointure retourne tous les enregistrements du côté droit qui ne correspondent à aucun enregistrement du côté gauche. 
 
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

|Clé :|Value2|
|---|---|
|d|40|

L’anti-jointure modélise la requête « NOT IN ». 

### <a name="left-semi-join"></a>Semi-jointure gauche

La semi-jointure gauche retourne tous les enregistrements du côté gauche qui correspondent à un enregistrement du côté droit. Seules les colonnes du côté gauche sont retournées. 

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

|Clé :|Value1|
|---|---|
|b|3|
|b|2|
|c|4|

### <a name="right-semi-join"></a>Semi-jointure droite

Right semi Join retourne tous les enregistrements du côté droit qui correspondent à un enregistrement du côté gauche. Seules les colonnes du côté droit sont retournées. 

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

|Clé :|Value2|
|---|---|
|b|10|
|c|20|
|c|30|


### <a name="cross-join"></a>Jointure croisée

Kusto ne fournit pas de manière native une version de jointure croisée (c’est-à-dire que vous ne pouvez pas marquer l’opérateur avec `kind=cross` ).
Toutefois, il n’est pas difficile de simuler cette approche en utilisant une clé factice :

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>Indicateurs de jointure

L' `join` opérateur prend en charge un certain nombre d’indicateurs qui contrôlent la façon dont une requête s’exécute.
Elles ne modifient pas la sémantique de `join` , mais elles peuvent affecter ses performances.

Les indicateurs de jointure sont expliqués dans les articles suivants : 
* `hint.shufflekey=<key>`et `hint.strategy=shuffle`  -  [requête de lecture aléatoire](shufflequery.md)
* `hint.strategy=broadcast` - [jonction de diffusion](broadcastjoin.md)
* `hint.remote=<strategy>` - [jointure entre clusters](joincrosscluster.md)

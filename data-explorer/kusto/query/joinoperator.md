---
title: Opérateur de jointure - Azure Data Explorer
description: Cet article décrit l'opérateur de jointure dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b90e5f1c95ec75a946490cd75b5dd89ad2cb1aba
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513333"
---
# <a name="join-operator"></a>opérateur de jointure

Fusionnez les lignes de deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes spécifiées de chaque table.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>Syntaxe

*LeftTable* `|` `join` [*JoinParameters*] `(` *RightTable* `)` `on` *Attributes*

## <a name="arguments"></a>Arguments

* *LeftTable* : table ou expression tabulaire de **gauche**, parfois appelée table **externe**, dont les lignes doivent être fusionnées. Désignée sous la forme suivante : `$left`.

* *RightTable* : table ou expression tabulaire de **droite**, parfois appelée table **interne**, dont les lignes doivent être fusionnées. Désignée sous la forme suivante : `$right`.

* *Attributes* : une ou plusieurs **règles** séparées par des virgules qui décrivent comment les lignes de *LeftTable* sont mises en correspondance avec les lignes de *RightTable*. Les règles multiples sont évaluées à l'aide de l'opérateur logique `and`.

  Une **règle** peut être d'un des types suivants :

  |Type de règle        |Syntaxe          |Predicate    |
  |-----------------|--------------|-------------------------|
  |Égalité par nom |*ColumnName*    |`where` *LeftTable*.*ColumnName* `==` *RightTable*.*ColumnName*|
  |Égalité par valeur|`$left.`*LeftColumn* `==` `$right.`*RightColumn*|`where` `$left.`*LeftColumn* `==` `$right.`*RightColumn*       |

    > [!NOTE]
    > Pour l'égalité par valeur, les noms des colonnes *doivent* être qualifiés auprès de la table propriétaire applicable désignée par les notations `$left` et `$right`.

* *JoinParameters* : zéro ou plusieurs paramètres séparés par des espaces, qui se présentent sous la forme *Nom* `=` *Valeur* et qui contrôlent le comportement de l'opération de correspondance des lignes et du plan d'exécution. Les paramètres suivants sont pris en charge :

    ::: zone pivot="azuredataexplorer"

    |Nom des paramètres           |Valeurs                                        |Description                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |Saveurs de jointure|Voir [Saveurs de jointure](#join-flavors)|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |Voir [Jointure entre clusters](joincrosscluster.md)|
    |`hint.strategy`|Indicateurs d'exécution                               |Voir [Indicateurs de jointure](#join-hints)                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |Nom           |Valeurs                                        |Description                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |Saveurs de jointure|Voir [Saveurs de jointure](#join-flavors)|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|Indicateurs d'exécution                               |Voir [Indicateurs de jointure](#join-hints)                |

    ::: zone-end

> [!WARNING]
> Si `kind` n'est pas spécifié, la saveur de jointure par défaut est `innerunique`. Il s'agit d'une différence notable par rapport à certains autres produits d'analytique dont la saveur par défaut est `inner`.  Consultez [Saveurs de jointure](#join-flavors) pour identifier les différences et vérifier que la requête produit les résultats attendus.

## <a name="returns"></a>Retours

**Le schéma de sortie dépend de la saveur de jointure :**

| Saveur de jointure | Schéma de sortie |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| La table des résultats contient uniquement les colonnes du côté gauche.|
| `kind=rightanti`, `kind=rightsemi` | La table des résultats contient uniquement les colonnes du côté droit.|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes. Les colonnes du côté droit seront automatiquement renommées en cas de conflit de nom. |
   
**Les enregistrements de sortie dépendent de la saveur de jointure :**

   > [!NOTE]
   > Si plusieurs lignes comportent les mêmes valeurs pour ces champs, des lignes s’affichent pour toutes les combinaisons.
   > Une correspondance est une ligne sélectionnée dans une table, dont tous les champs `on` ont la même valeur qu’une ligne dans l’autre table.

| Saveur de jointure | Enregistrements de sortie |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| Renvoie tous les enregistrements du côté gauche qui n'ont pas de correspondance du côté droit.|
| `kind=rightanti`, `kind=rightantisemi`| Renvoie tous les enregistrements du côté droit qui n'ont pas de correspondance du côté gauche.|
| `kind` non spécifié, `kind=innerunique`| Une seule ligne du côté gauche correspond à chaque valeur de la clé `on` . La sortie contient une ligne pour chaque correspondance de cette ligne avec des lignes du côté droit.|
| `kind=leftsemi`| Renvoie tous les enregistrements du côté gauche qui ont des correspondances du côté droit. |
| `kind=rightsemi`| Renvoie tous les enregistrements du côté droit qui ont des correspondances du côté gauche. |
|`kind=inner`| Contient une ligne pour chaque combinaison de lignes correspondantes des côtés gauche et droit. |
| `kind=leftouter` (ou `kind=rightouter` ou `kind=fullouter`)| Contient une ligne pour chaque ligne de gauche et de droite, même si elle n'a pas de correspondance. Les cellules de sortie sans correspondance contiennent des valeurs null. |

> [!TIP]
> Pour des performances optimales, si une table est toujours plus petite que l'autre, utilisez-la pour le côté gauche de la jointure.

## <a name="example"></a>Exemple

Obtenez des activités étendues à partir d'un `login` que certaines entrées marquent comme le début et la fin d'une activité.

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

## <a name="join-flavors"></a>Saveurs de jointure

La saveur exacte de l'opérateur de jointure est spécifiée avec le mot clé *kind* (type). Les saveurs suivantes de l'opérateur de jointure sont prises en charge :

|Type/saveur de jointure|Description|
|--|--|
|[`innerunique`](#default-join-flavor) (ou vide par défaut)|Jointure interne avec déduplication du côté gauche|
|[`inner`](#inner-join-flavor)|Jointure interne standard|
|[`leftouter`](#left-outer-join-flavor)|Jointure externe gauche|
|[`rightouter`](#right-outer-join-flavor)|Jointure externe droite|
|[`fullouter`](#full-outer-join-flavor)|Jointure externe entière|
|[`leftanti`](#left-anti-join-flavor), [`anti`](#left-anti-join-flavor) ou [`leftantisemi`](#left-anti-join-flavor)|Jointure anti gauche|
|[`rightanti`](#right-anti-join-flavor) ou [`rightantisemi`](#right-anti-join-flavor)|Jointure anti droite|
|[`leftsemi`](#left-semi-join-flavor)|Semi-jointure gauche|
|[`rightsemi`](#right-semi-join-flavor)|Semi-jointure droite|

### <a name="default-join-flavor"></a>Saveur de jointure par défaut

La saveur de jointure par défaut est une jointure interne avec déduplication du côté gauche. L'implémentation de la jointure par défaut est utile dans les scénarios classiques d'analyse de journal/suivi dans lesquels vous souhaitez corréler deux événements, chacun correspondant à un critère de filtrage, sous le même ID de corrélation. Vous souhaitez récupérer toutes les apparitions du phénomène et ignorer les apparitions multiples des enregistrements de suivi contributifs.

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

Les deux exemples de tables suivants expliquent le fonctionnement de la jointure.

**Table X**

|Clé |Value1
|---|---
|a |1
|b |2
|b |3
|c |4

**Table Y**

|Clé |Value2
|---|---
|b |10
|c |20
|c |30
|d |40

La jointure par défaut effectue une jointure interne après déduplication du côté gauche sur la clé de jointure (la déduplication conserve le premier enregistrement).

Soit l'instruction suivante : `X | join Y on Key`

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

> [!NOTE]
> Les clés « a » et « d » n'apparaissent pas dans la sortie, car elles ne figuraient pas à la fois du côté gauche et du côté droit.

### <a name="inner-join-flavor"></a>Saveur de jointure interne

La fonction inner-join est semblable à la fonction inner-join standard de l'environnement SQL. Un enregistrement de sortie est généré chaque fois qu'un enregistrement du côté gauche possède la même clé de jointure que l'enregistrement du côté droit.

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

> [!NOTE]
> * (b,10) du côté droit, a été joint deux fois : avec (b,2) et (b,3) du côté gauche.
> * (c,4) du côté gauche, a été joint deux fois : avec (c,20) et (c,30) du côté droit.

### <a name="innerunique-join-flavor"></a>Saveur innerunique-join
 
Utilisez la **saveur innerunique-join** pour dédupliquer des clés du côté gauche. Vous obtiendrez une ligne dans la sortie de chaque combinaison de clés gauches et droites dédupliquées.

> [!NOTE]
> La **saveur innerunique** peut générer deux sorties, et les deux sont correctes.
Dans la première sortie, l'opérateur de jointure a sélectionné au hasard la première clé qui apparaît dans t1, avec la valeur « val1.1 », et l'a mise en correspondance avec les clés t2.
Dans la deuxième sortie, l'opérateur de jointure a sélectionné au hasard la deuxième clé qui apparaît dans t1, avec la valeur « val1.2 », et l'a mise en correspondance avec les clés t2.

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

* Kusto est optimisé pour transmettre (push) les filtres qui suivent l'opérateur `join` vers le côté approprié de la jointure, à gauche ou à droite, lorsque cela est possible.

* Parfois, la saveur utilisée est **innerunique** et le filtre est propagé vers le côté gauche de la jointure. La saveur est automatiquement propagée, et les clés qui s'appliquent à ce filtre apparaissent toujours dans la sortie.
    
* Utilisez l'exemple ci-dessus et ajoutez un filtre `where value == "val1.2" `. Le deuxième résultat sera toujours fourni pour les jeux de données, mais jamais le premier :

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

### <a name="left-outer-join-flavor"></a>Saveur de jointure externe gauche

Le résultat d'une jointure externe gauche pour les tables X et Y contient toujours l'ensemble des enregistrements de la table de gauche (X), même si la condition de jointure ne trouve pas d'enregistrements correspondants dans la table de droite (Y).

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

### <a name="right-outer-join-flavor"></a>Saveur de jointure externe droite

La saveur de jointure externe droite est identique à celle de gauche, mais le traitement des tables est inversé.

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

### <a name="full-outer-join-flavor"></a>Saveur de jointure externe complète

Une jointure externe complète combine l'effet de l'application de jointures externes gauche et droite. Lorsque les enregistrements des tables jointes ne correspondent pas, le jeu de résultats contient des valeurs `null` pour chaque colonne de la table à laquelle il manque une ligne correspondante. Pour les enregistrements qui correspondent, une seule ligne est générée dans le jeu de résultats, les champs étant renseignés à partir des deux tables.

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

### <a name="left-anti-join-flavor"></a>Saveur de jointure anti gauche

Une jointure anti gauche renvoie tous les enregistrements du côté gauche qui ne correspondent à aucun enregistrement du côté droit.

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

> [!NOTE]
> L’anti-jointure modélise la requête « NOT IN ».

### <a name="right-anti-join-flavor"></a>Saveur de jointure anti droite

Une jointure anti droite renvoie tous les enregistrements du côté droit qui ne correspondent à aucun enregistrement du côté gauche.

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

> [!NOTE]
> L’anti-jointure modélise la requête « NOT IN ».

### <a name="left-semi-join-flavor"></a>Saveur semi-jointure gauche

Une semi-jointure gauche renvoie tous les enregistrements du côté gauche qui correspondent à un enregistrement du côté droit. Seules les colonnes du côté gauche sont renvoyées.

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

### <a name="right-semi-join-flavor"></a>Saveur semi-jointure droite

Une semi-jointure droite renvoie tous les enregistrements du côté droit qui correspondent à un enregistrement du côté gauche. Seules les colonnes du côté droit sont renvoyées.

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

### <a name="cross-join"></a>Jointure croisée

Kusto ne fournit pas de saveur de jointure croisée en mode natif. Vous ne pouvez pas marquer l'opérateur avec `kind=cross`.
Pour effectuer une simulation, utilisez une clé factice.

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>Indicateurs de jointure

L'opérateur `join` prend en charge un certain nombre d'indicateurs qui contrôlent la façon dont une requête s'exécute.
Ces indicateurs ne modifient pas la sémantique de `join`, mais peuvent affecter ses performances.

Les indicateurs de jointure sont décrits dans les articles suivants :

* `hint.shufflekey=<key>` et `hint.strategy=shuffle` - [lecture aléatoire](shufflequery.md)
* `hint.strategy=broadcast` - [jonction de diffusion](broadcastjoin.md)
* `hint.remote=<strategy>` - [jointure entre clusters](joincrosscluster.md)

---
title: opérateur de jointure-Azure Explorateur de données
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
ms.openlocfilehash: 4952e315a974e72135c722b255a96f57bf89cc12
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058777"
---
# <a name="join-operator"></a>opérateur join

Fusionnez les lignes de deux tables pour former une nouvelle table en faisant correspondre les valeurs des colonnes spécifiées de chaque table.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>Syntax

*LeftTable* `|` `join`[*JoinParameters*] `(` *RightTable* `)` `on` *attributs*

## <a name="arguments"></a>Arguments

* *LeftTable*: table de **gauche** ou expression tabulaire, parfois appelée table **externe** , dont les lignes doivent être fusionnées. Désigné comme `$left` .

* *RightTable*: table ou expression tabulaire **appropriée** , parfois appelée table **interne** , dont les lignes doivent être fusionnées. Désigné comme `$right` .

* *Attributs*: une ou plusieurs **règles** séparées par des virgules qui décrivent comment les lignes de *LeftTable* sont mises en correspondance avec les lignes de *RightTable*. Plusieurs règles sont évaluées à l’aide de l' `and` opérateur logique.

  Une **règle** peut être l’une des suivantes :

  |Type de règle        |Syntax          |Predicate    |
  |-----------------|--------------|-------------------------|
  |Égalité par nom |*ColumnName*    |`where`*LeftTable*. *ColumnName* `==` *RightTable*. *ColumnName*|
  |Égalité par valeur|`$left.`*LeftColumn* `==` `$right.` *Rightcolumn*|`where``$left.` *LeftColumn* `==` LeftColumn `$right.` *Rightcolumn*       |

    > [!NOTE]
    > Pour « égalité par valeur », les noms de colonnes *doivent* être qualifiés par la table de propriétaire applicable dénotée par les `$left` `$right` notations et.

* *JoinParameters*: zéro, un ou plusieurs paramètres séparés par des espaces sous la forme d’une valeur de *nom* `=` *Value* qui contrôlent le comportement de l’opération de correspondance de ligne et du plan d’exécution. Les paramètres suivants sont pris en charge :

    ::: zone pivot="azuredataexplorer"

    |Nom des paramètres           |Valeurs                                        |Description                                  |
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
> Si `kind` n’est pas spécifié, la version de jointure par défaut est `innerunique` . Cela diffère des autres produits d’analyse ayant `inner` comme version par défaut.  Consultez [jointure-versions](#join-flavors) pour comprendre les différences et vérifier que la requête produit les résultats souhaités.

## <a name="returns"></a>Retours

**Le schéma de sortie dépend de la version de jointure :**

| Version de jointure | Schéma de sortie |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| La table de résultats contient uniquement des colonnes du côté gauche.|
| `kind=rightanti`, `kind=rightsemi` | La table de résultats contient uniquement des colonnes du côté droit.|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes. Les colonnes du côté droit seront automatiquement renommées en cas de conflit de nom. |
   
**Les enregistrements de sortie dépendent de la version de jointure :**

   > [!NOTE]
   > Si plusieurs lignes comportent les mêmes valeurs pour ces champs, des lignes s’affichent pour toutes les combinaisons.
   > Une correspondance est une ligne sélectionnée dans une table, dont tous les champs `on` ont la même valeur qu’une ligne dans l’autre table.

| Version de jointure | Enregistrements de sortie |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| Retourne tous les enregistrements du côté gauche qui n’ont pas de correspondances de droite|
| `kind=rightanti`, `kind=rightantisemi`| Retourne tous les enregistrements du côté droit qui n’ont pas de correspondances à partir de la gauche.|
| `kind`non spécifié`kind=innerunique`| Une seule ligne du côté gauche correspond à chaque valeur de la clé `on` . La sortie contient une ligne pour chaque correspondance de cette ligne avec des lignes du côté droit.|
| `kind=leftsemi`| Retourne tous les enregistrements du côté gauche qui ont des correspondances à partir de la droite. |
| `kind=rightsemi`| Retourne tous les enregistrements du côté droit qui ont des correspondances à partir de la gauche. |
|`kind=inner`| Contient une ligne dans la sortie pour chaque combinaison de lignes correspondantes, de gauche à droite. |
| `kind=leftouter` (ou `kind=rightouter` ou `kind=fullouter`)| Contient une ligne pour chaque ligne à gauche et à droite, même si elle n’a pas de correspondance. Les cellules de sortie sans correspondance contiennent des valeurs NULL. |

> [!TIP]
> Pour de meilleures performances, si une table est toujours plus petite que l’autre, utilisez-la comme côté gauche (dirigé) de la jointure.

## <a name="example"></a>Exemple

Obtenez des activités étendues d’un `login` que certaines entrées marquent comme le début et la fin d’une activité.

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

## <a name="join-flavors"></a>Variantes de jointure

La version exacte de l’opérateur de jointure est spécifiée avec le mot clé *Kind* . Les versions suivantes de l’opérateur de jointure sont prises en charge :

|Type de jointure/version|Description|
|--|--|
|[`innerunique`](#default-join-flavor)(ou vide par défaut)|Jointure interne avec déduplication côté gauche|
|[`inner`](#inner-join-flavor)|Jointure interne standard|
|[`leftouter`](#left-outer-join-flavor)|Jointure externe gauche|
|[`rightouter`](#right-outer-join-flavor)|Jointure externe droite|
|[`fullouter`](#full-outer-join-flavor)|Jointure externe complète|
|[`leftanti`](#left-anti-join-flavor), [`anti`](#left-anti-join-flavor) ou[`leftantisemi`](#left-anti-join-flavor)|Jointure anti gauche|
|[`rightanti`](#right-anti-join-flavor)ni[`rightantisemi`](#right-anti-join-flavor)|Anti-jointure Right|
|[`leftsemi`](#left-semi-join-flavor)|Semi-jointure gauche|
|[`rightsemi`](#right-semi-join-flavor)|Semi-jointure droite|

### <a name="default-join-flavor"></a>Version de jointure par défaut

La version de jointure par défaut est une jointure interne avec déduplication côté gauche. L’implémentation de la jointure par défaut est utile dans les scénarios d’analyse de journal/suivi typiques où vous souhaitez mettre en corrélation deux événements, chacun correspondant à un critère de filtrage, sous le même ID de corrélation. Vous souhaitez récupérer toutes les apparences du phénomène et ignorer les apparences multiples des enregistrements de suivi de contribution.

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

Les deux exemples de tables suivants sont utilisés pour expliquer le fonctionnement de la jointure.

**Table X**

|Clé : |Value1
|---|---
|a |1
|b |2
|b |3
|c |4

**Table Y**

|Clé : |Value2
|---|---
|b |10
|c |20
|c |30
|d |40

La jointure par défaut effectue une jointure interne après la déduplication du côté gauche de la clé de jointure (la déduplication conserve le premier enregistrement).

À partir de cette instruction :`X | join Y on Key`

le côté gauche effectif de la jointure, table X après déduplication, est le suivant :

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

> [!NOTE]
> Les clés « a » et « d » n’apparaissent pas dans la sortie, car il n’y avait aucune clé correspondante sur les côtés gauche et droit.

### <a name="inner-join-flavor"></a>Version de jointure interne

La fonction de jointure interne est semblable à la jointure interne standard à partir de l’environnement SQL. Un enregistrement de sortie est généré chaque fois qu’un enregistrement sur le côté gauche a la même clé de jointure que l’enregistrement situé à droite.

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

> [!NOTE]
> * (b, 10) du côté droit, a été joint deux fois : avec (b, 2) et (b, 3) sur la gauche.
> * (c, 4) sur le côté gauche, a été joint deux fois : avec (c, 20) et (c, 30) à droite.

### <a name="innerunique-join-flavor"></a>Innerunique : version de jointure
 
Utilisez la **version innerunique-Join** pour dédupliquer les clés du côté gauche. Le résultat sera une ligne dans la sortie de chaque combinaison de clés de gauche et de clé de droite dédupliquées.

> [!NOTE]
> la **version innerunique** peut générer deux sorties possibles et les deux sont correctes.
Dans la première sortie, l’opérateur de jointure a sélectionné de façon aléatoire la première clé qui apparaît dans T1, avec la valeur « Val 1.1 » et le correspond aux clés T2.
Dans la deuxième sortie, l’opérateur de jointure a sélectionné de manière aléatoire la deuxième clé qui apparaît dans T1, avec la valeur « Val 1.2 » et l’a mise en correspondance avec les clés T2.

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

* Kusto est optimisé pour pousser les filtres qui viennent après le `join` , vers le côté de jointure approprié, à gauche ou à droite, lorsque cela est possible.

* Parfois, la version utilisée est **innerunique** et le filtre est propagé sur le côté gauche de la jointure. La version est automatiquement propagée et les clés qui s’appliquent à ce filtre apparaissent toujours dans la sortie.
    
* Utilisez l’exemple ci-dessus et ajoutez un filtre `where value == "val1.2" ` . Il donne toujours le deuxième résultat et ne donne jamais le premier résultat pour les jeux de données :

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

### <a name="left-outer-join-flavor"></a>Version Left externe-jointure

Le résultat d’une jointure externe gauche pour les tables X et Y contient toujours tous les enregistrements de la table de gauche (X), même si la condition de jointure ne trouve pas d’enregistrement correspondant dans la table de droite (Y).

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

### <a name="right-outer-join-flavor"></a>Version de jointure externe droite

La version de jointure externe droite ressemble à la jointure externe gauche, mais le traitement des tables est inversé.

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

### <a name="full-outer-join-flavor"></a>Version de jointure externe complète

Une jointure externe complète combine l’effet de l’application des jointures externes gauche et droite. Chaque fois que les enregistrements des tables jointes ne correspondent pas, le jeu de résultats contient des `null` valeurs pour chaque colonne de la table qui n’a pas de ligne correspondante. Pour les enregistrements qui ne correspondent pas, une seule ligne est produite dans le jeu de résultats, contenant les champs remplis par les deux tables.

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

### <a name="left-anti-join-flavor"></a>Version anti-jointure gauche

La fonction anti-jointure gauche retourne tous les enregistrements du côté gauche qui ne correspondent à aucun enregistrement du côté droit.

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

> [!NOTE]
> L’anti-jointure modélise la requête « NOT IN ».

### <a name="right-anti-join-flavor"></a>Version anti-jointure droite

Right anti-Join retourne tous les enregistrements du côté droit qui ne correspondent à aucun enregistrement du côté gauche.

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

> [!NOTE]
> L’anti-jointure modélise la requête « NOT IN ».

### <a name="left-semi-join-flavor"></a>Version semi-jointure gauche

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

### <a name="right-semi-join-flavor"></a>Version semi-jointure droite

La semi-jointure droite retourne tous les enregistrements du côté droit qui correspondent à un enregistrement du côté gauche. Seules les colonnes du côté droit sont retournées.

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

Kusto ne fournit pas en mode natif une version de jointure croisée. Vous ne pouvez pas marquer l’opérateur avec `kind=cross` .
Pour simuler, utilisez une clé factice.

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>Indicateurs de jointure

L' `join` opérateur prend en charge un certain nombre d’indicateurs qui contrôlent la façon dont une requête s’exécute.
Ces indicateurs ne modifient pas la sémantique de `join` , mais peuvent affecter ses performances.

Les indicateurs de jointure sont expliqués dans les articles suivants :

* `hint.shufflekey=<key>`et `hint.strategy=shuffle`  -  [requête de lecture aléatoire](shufflequery.md)
* `hint.strategy=broadcast` - [jonction de diffusion](broadcastjoin.md)
* `hint.remote=<strategy>` - [jointure entre clusters](joincrosscluster.md)

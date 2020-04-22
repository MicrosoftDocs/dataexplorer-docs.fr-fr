---
title: trouver opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de recherche dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4c3db23128c47c86639f15286cbcbcb748157386
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765910"
---
# <a name="find-operator"></a>Opérateur find

Trouve des lignes qui correspondent à un prédicat à travers un ensemble de tables.

::: zone pivot="azuredataexplorer"

La portée `find` de la peut également être multi-base de données ou multi-cluster.

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"

find in (database('*').*) where Fruit == "apple"

find in (cluster('cluster_name').database('MyDB*'.*)) where Fruit == "apple"
```

::: zone-end

::: zone pivot="azuremonitor"

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"
```

::: zone-end

## <a name="syntax"></a>Syntaxe

* `find`[`withsource`=*ColumnName*] `in` `(` *[Tableau* `,` [ *Tableau*, ...] `)`] `where` *Predicate* [`project-smart`  |  `project` *ColumnName* [`:``,` *ColumnType*] [ *ColumnName*[`:`*ColumnType*], ...] [`,` `pack(*)`]] 

* `find`*Predicate* `project-smart`  |  `project` [ *ColumnName*`:`[*ColumnType*] [`,` *ColumnName*[`:`*ColumnType*], ...] [`, pack(*)`]] 

## <a name="arguments"></a>Arguments

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*: Facultatif. Par défaut, la sortie comprendra une colonne appelée *source_* dont les valeurs indiquent quelle table source a contribué chaque ligne. Si spécifié, *ColumnName* sera utilisé au lieu de *source_* .
Si la requête efficacement (après correspondance wildcard) références tables de plus d’une base de données (base de données par défaut compte toujours), la valeur de cette colonne aura un nom de table qualifié avec la base de données. De même, les qualifications __des clusters et des bases de données__ seront présentes dans la valeur si plus d’un cluster est référencé.
* *Predicate*: [voir les détails](./findoperator.md#predicate-syntax). Une `boolean` [expression](./scalar-data-types/bool.md) sur les colonnes des tableaux d’entrée *Table* `,` *[Tableau*,]. Il est évalué pour chaque rangée dans chaque tableau d’entrée. 
* `Table` : facultatif. Par *défaut, trouver* effectuera une recherche dans toutes les tables de la base de données actuelle
    *  Le nom d’une `Events` table, tel que ou
    *  Expression de requête, telle que `(Events | where id==42)`
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’union de toutes les `E`tables dans la base de données dont les noms commencent .
* `project-smart` | `project`: [voir les](./findoperator.md#output-schema) `project-smart` détails s’ils ne sont pas spécifiés seront utilisés par défaut

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*: Facultatif. Par défaut, la sortie comprendra une colonne appelée *source_* dont les valeurs indiquent quelle table source a contribué chaque ligne. Si spécifié, *ColumnName* sera utilisé au lieu de *source_* .
* *Predicate*: [voir les détails](./findoperator.md#predicate-syntax). Une `boolean` [expression](./scalar-data-types/bool.md) sur les colonnes des tableaux d’entrée *Table* `,` *[Tableau*,]. Il est évalué pour chaque rangée dans chaque tableau d’entrée. 
* `Table` : facultatif. Par défaut *trouver* cherchera dans toutes les tables
    *  Le nom d’une `Events` table, tel que ou
    *  Expression de requête, telle que `(Events | where id==42)`
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’union de `E`toutes les tables dont les noms commencent .
* `project-smart` | `project`: [voir les](./findoperator.md#output-schema) `project-smart` détails s’ils ne sont pas spécifiés seront utilisés par défaut

::: zone-end

## <a name="returns"></a>Retours

Transformation des lignes dans *la table* `,` *[Tableau*, `true`...] pour lequel *Predicate* est . Les lignes sont transformées selon le schéma de sortie tel que décrit ci-dessous.

## <a name="output-schema"></a>Production Schema

**colonne source_**

La sortie de l’opérateur de recherche inclura toujours une colonne *de source_* avec le nom de table source. La colonne peut être `withsource` rebaptisée à l’aide du paramètre.

**colonnes de résultats**

Les tableaux sources qui ne contiennent aucune colonne utilisée par l’évaluation prédicée seront filtrés.

Lors `project-smart`de l’utilisation , les colonnes qui apparaîtront dans la sortie sera:
1. Colonnes qui apparaissent explicitement dans le prédicat
2. Colonnes qui sont communes à toutes les tables filtrées Le reste des colonnes `pack_` sera emballé dans un sac de propriété et apparaîtra dans une colonne supplémentaire.
Une colonne qui est référencée explicitement par le prédicat et apparaît dans plusieurs tableaux avec plusieurs types, aura une colonne différente dans le schéma de résultat pour chaque type. Chacun des noms de colonnes sera construit à partir du nom de colonne d’origine et du type, séparé par un soulignement.

Lors de `project` *l’utilisation de ColumnName*`:`[`:`*ColumnType*] [`,` *ColumnName*[*ColumnType*], ...] [`,` `pack(*)`]:
1. Le tableau de résultat inclura les colonnes spécifiées dans la liste. Si un tableau source ne contient pas une certaine colonne, les valeurs dans les lignes correspondantes seront nulles.
2. Lors de la spécifier un *ColumnType* avec un *ColumnName*, cette colonne dans le **résultat** aura le type donné, et les valeurs seront jetées à ce type si nécessaire. Notez que cela n’aura pas d’effet sur le type de colonne lors de l’évaluation du *Predicate*.
3. Lorsqu’elle `pack(*)` est utilisée, le reste des colonnes sera emballé dans `pack_` un sac de propriété et apparaîtra dans une colonne supplémentaire

**colonne pack_**

Cette colonne contiendra un sac de propriété avec les données de toutes les colonnes qui n’apparaissent pas dans le schéma de sortie. Le nom de colonne source servira de nom de propriété et la valeur de la colonne servira de valeur de propriété.

## <a name="predicate-syntax"></a>Syntaxe prédicat

Veuillez consulter [l’opérateur de](./whereoperator.md) certains résumés des fonctions de filtrage.

trouver opérateur prend en `* has` charge une syntaxe alternative pour *terme* , et en utilisant juste *terme* va rechercher un terme à travers toutes les colonnes d’entrée

## <a name="notes"></a>Notes

* Si `project` la clause fait référence à une colonne qui apparaît dans plusieurs tableaux et qui comporte plusieurs types, un type doit suivre cette référence de colonne dans la clause de projet
* Si une colonne apparaît dans plusieurs `project-smart` tableaux et a plusieurs types et est en `find`usage, il y aura une colonne correspondante pour chaque type dans le résultat de l’Union, comme décrit dans [le syndicat](./unionoperator.md)
* Lors de l’utilisation *de projet-intelligent*, les changements dans le prédicat, dans les tables à source ensemble ou dans le schéma des tables peuvent entraîner des changements dans le schéma de sortie. Si un schéma de résultat constant est nécessaire, utilisez le *projet* à la place
* `find`la portée ne peut pas inclure les [fonctions](../management/functions.md). Pour inclure une fonction dans la portée de la recherche - définir une [instruction de laisser](./letstatement.md) avec mot clé de [vue](./letstatement.md)

## <a name="performance-tips"></a>Conseils pour les performances

* Utiliser [des tableaux](../management/tables.md) par opposition aux expressions [tabulaires](./tabularexpressionstatements.md)- en `union` cas d’expression tabulaire, l’opérateur de recherche retombe à une requête qui peut entraîner une dégradation des performances
* Si une colonne qui apparaît dans plusieurs tableaux et a plusieurs types fait partie de la clause de projet, `find` préférez ajouter un *ColumnType* à la clause de projet sur la modification de la table avant de la passer à (voir astuce précédente)
* Ajouter des filtres basés sur le temps au prédicat (à l’aide d’une valeur de colonne de date ou [d’une ingestion_time))](./ingestiontimefunction.md))
* Préférez rechercher dans des colonnes spécifiques sur la recherche texte intégrale 
* Préférez ne pas référence explicitement colonnes qui apparaît dans plusieurs tableaux et a plusieurs types. Si le prédicat est valide lors de la résolution de ce type de colonnes pour plus d’un type, la requête se repliera sur l’union

[voir des exemples](./findoperator.md#examples-of-cases-where-find-will-perform-as-union)

## <a name="examples"></a>Exemples

::: zone pivot="azuredataexplorer"

###  <a name="term-lookup-across-all-tables-in-current-database"></a>Recherche à terme sur toutes les tables (dans la base de données actuelle)

La requête suivante trouve toutes les lignes de toutes les tables `Kusto`de la base de données actuelle dans laquelle toute colonne inclut le mot . Les enregistrements qui en résultent sont transformés en fonction du [schéma de sortie.](#output-schema) 

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>Recherche à terme sur toutes les tables correspondant à un modèle de nom (dans la base de données actuelle)

La requête suivante trouve toutes les lignes de toutes les `K`tables de la base `Kusto`de données actuelle dont le nom commence par , et dans lequel toute colonne comprend le mot .
Les enregistrements qui en résultent sont transformés en fonction du [schéma de sortie.](#output-schema) 

```kusto
find in (K*) where * has "Kusto"
```

###  <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>Recherche à terme sur toutes les tables dans toutes les bases de données (dans le cluster)

La requête suivante trouve toutes les lignes de toutes les tables `Kusto`dans toutes les bases de données dans lesquelles toute colonne inclut le mot . Il s’agit d’une requête [de requête de base de données croisées.](./cross-cluster-or-database-queries.md) Les enregistrements qui en résultent sont transformés en fonction du [schéma de sortie.](#output-schema) 

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>Recherche à terme sur toutes les tables et bases de données correspondant à un modèle de nom (dans le cluster)

La requête suivante trouve toutes les lignes de `K` toutes les tables dont `B` le nom commence dans `Kusto`toutes les bases de données dont le nom commence par et dans lequel toute colonne comprend le mot . Les enregistrements qui en résultent sont transformés en fonction du [schéma de sortie.](#output-schema) 

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>Recherche à terme dans plusieurs clusters

La requête suivante trouve toutes les lignes de `K` toutes les tables dont `B` le nom commence dans `Kusto`toutes les bases de données dont le nom commence par et dans lequel toute colonne comprend le mot . Les enregistrements qui en résultent sont transformés en fonction du [schéma de sortie.](#output-schema) 

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>Recherche à terme sur toutes les tables

La requête suivante trouve toutes les lignes de toutes `Kusto`les tables dans lesquelles n’importe quelle colonne inclut le mot . Les enregistrements qui en résultent sont transformés en fonction du [schéma de sortie.](#output-schema) 

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>Exemples `find` de résultats de sortie  

Les exemples `find` suivants montrent comment peut être utilisé sur deux tables: EventsTable1 et EventsTable2. Supposons que nous avons le contenu suivant de ces deux tables: 

### <a name="eventstable1"></a>ÉvénementsTable1

|Session_id|Level|EventText|Version
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Quelques textes1|v1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Quelques textes2|v1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|Error|Quelques text 3|v1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|Information|Quelques text4|v1.1.0

### <a name="eventstable2"></a>ÉvénementsTable2

|Session_id|Level|EventText|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Information|Un autre texte1|Événement1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Quelques autres textes2|Événement2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Quelques autres textes3|Événement3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|Error|Quelques autres textes4|Événement4


### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>Rechercher dans des colonnes communes, projeter des colonnes communes et rares et emballer le reste  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|EventText|Version|EventName|pack_
|---|---|---|---|---|
|ÉvénementsTable1|Quelques textes2|v1.0.0||"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Error"
|ÉvénementsTable2|Quelques autres textes3||Événement3|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Error"


### <a name="search-in-common-and-uncommon-columns"></a>Rechercher dans des colonnes courantes et rares

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_id|EventText|Version|EventName|
|---|---|---|---|---|
|ÉvénementsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Quelques textes1|v1.0.0
|ÉvénementsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Quelques textes2|v1.0.0
|ÉvénementsTable2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Un autre texte1||Événement1

Remarque : dans la pratique, les lignes ```Version == 'v1.0.0'``` *EventsTable1* seront filtrées avec des lignes ```EventName == 'Event1'``` predicate et *EventsTable2* sera filtrée avec predicate.

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>Utilisez une notation abrégée pour rechercher sur toutes les tables de la base de données actuelle

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_id|Level|EventText|pack_|
|---|---|---|---|---|
|ÉvénementsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Quelques textes1|"Version":"v1.0.0"
|ÉvénementsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Quelques textes2|"Version":"v1.0.0"
|ÉvénementsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Quelques autres textes2|"EventName":"Event2"
|ÉvénementsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Quelques autres textes3|"EventName":"Event3"


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>Retourner les résultats de chaque rangée comme un sac de propriété

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|ÉvénementsTable1|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Information", "EventText":"Some Text1", "Version":"v1.0.0"
|ÉvénementsTable1|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Error", "EventText":"Some Text2", "Version":"v1.0.0"
|ÉvénementsTable2|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Information", "EventText":"Some Other Text2", "EventName":"Event2"
|ÉvénementsTable2|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Error", "EventText":"Some Other Text3", "EventName":"Event3"


## <a name="examples-of-cases-where-find-will-perform-as-union"></a>Exemples de `find` cas où les`union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>Utilisation d’une expression non tabulaire comme trouver l’opéra

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>Référencement d’une colonne qui apparaît dans plusieurs tableaux et a plusieurs types

Supposons que nous avons créé deux tables en courant: 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* La requête suivante sera exécutée sous la forme `union`de :
```kusto
find in (Table1, Table2) where ProcessId == 1001
```

Et le schéma de résultat de sortie sera __(Level:string, Timestamp, ProcessId_string, ProcessId_int)__

* La requête suivante sera également exécutée, `union` mais produira un schéma de résultat différent :
```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

Et le schéma de résultat de sortie sera __(Level:string, Timestamp, ProcessId_string)__

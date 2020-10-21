---
title: opérateur de recherche-Azure Explorateur de données
description: Cet article décrit l’opérateur de recherche dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d1e01f366c1bae677111c67b0e60fde59683706e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245037"
---
# <a name="find-operator"></a>Opérateur find

Recherche les lignes qui correspondent à un prédicat dans un ensemble de tables.

::: zone pivot="azuredataexplorer"

La portée du `find` peut également être une base de données croisée ou entre plusieurs clusters.

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

* `find`[ `withsource` = *ColumnName*] [ `in` `(` *table* [ `,` *table*,...] `)` ] `where` *Predicate* [ `project-smart`  |  `project` *ColumnName* [ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`,` `pack(*)`]] 

* `find`*Predicate* [ `project-smart`  |  `project` *ColumnName*[ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`, pack(*)`]] 

## <a name="arguments"></a>Arguments

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*: facultatif. Par défaut, la sortie inclut une colonne appelée *source_* dont les valeurs indiquent la table source qui a participé à chaque ligne. S’il est spécifié, *ColumnName* sera utilisé à la place de *source_*.
Après la correspondance des caractères génériques, si la requête fait référence à des tables de plusieurs bases de données (y compris la base de données par défaut), la valeur de cette colonne aura un nom de table qualifié avec la base de données. De même, les compétences de *cluster* et *de base de données* sont présentes dans la valeur si plusieurs clusters sont référencés.
* *Predicate*: `boolean` [expression](./scalar-data-types/bool.md) sur les colonnes de la *table* de tables d’entrée [ `,` *table*,...]. Elle est évaluée pour chaque ligne de chaque table d’entrée. Pour plus d’informations, consultez détails de la  [syntaxe de prédicat](./findoperator.md#predicate-syntax).
* `Table` : Facultatif. Par défaut, *Find* recherchera dans toutes les tables de la base de données active, pour :
    *  Nom d’une table, tel que `Events`
    *  Expression de requête, telle que `(Events | where id==42)`
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’Union de toutes les tables de la base de données dont le nom commence par `E` .
* `project-smart` | `project`: S’il n’est pas spécifié, `project-smart` sera utilisé par défaut. Pour plus d’informations, consultez [sortie-Détails du schéma](./findoperator.md#output-schema).

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*: facultatif. Par défaut, la sortie inclut une colonne appelée *source_* dont les valeurs indiquent la table source qui a apporté chaque ligne. S’il est spécifié, *ColumnName* sera utilisé à la place de *source_*.
* *Predicate*: `boolean` [expression](./scalar-data-types/bool.md) sur les colonnes de la *table* de tables d’entrée [ `,` *table*,...]. Elle est évaluée pour chaque ligne de chaque table d’entrée. Pour plus d’informations, consultez détails de la  [syntaxe de prédicat](./findoperator.md#predicate-syntax).
* `Table` : Facultatif. Par défaut, *Rechercher* recherche dans toutes les tables les éléments suivants :
    *  Nom d’une table, tel que `Events` 
    *  Expression de requête, telle que `(Events | where id==42)`
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’Union de toutes les tables dont les noms commencent par `E` .
* `project-smart` | `project`: S’il n’est pas spécifié, `project-smart` est utilisé par défaut. Pour plus d’informations, consultez [sortie-Détails du schéma](./findoperator.md#output-schema).

::: zone-end

## <a name="returns"></a>Retours

Transformation des lignes de la *table* [ `,` *table*,...] pour lesquelles le *prédicat* est `true` . Les lignes sont transformées en fonction du [schéma de sortie](#output-schema).

## <a name="output-schema"></a>Schéma de sortie

**source_ colonne**

La sortie de l’opérateur de recherche inclut toujours une *source_* colonne avec le nom de la table source. Vous pouvez renommer la colonne à l’aide du `withsource` paramètre.

**colonnes de résultats**

Les tables sources qui ne contiennent aucune colonne utilisée par l’évaluation de prédicat sont filtrées.

Lorsque vous utilisez `project-smart` , les colonnes qui s’affichent dans la sortie sont les suivantes :
* Colonnes qui s’affichent explicitement dans le prédicat.
* Colonnes communes à toutes les tables filtrées.

Le reste des colonnes sera empaqueté dans un conteneur de propriétés et apparaîtra dans une colonne supplémentaire `pack_` .
Une colonne référencée explicitement par le prédicat et apparaissant dans plusieurs tables avec plusieurs types, aura une colonne différente dans le schéma de résultat pour chaque type. Chacun des noms de colonnes est construit à partir du nom et du type de la colonne d’origine, séparés par un trait de soulignement.

Lors de l’utilisation `project` de *ColumnName*[ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`,` `pack(*)`]:
* La table de résultats inclut les colonnes spécifiées dans la liste. Si une table source ne contient pas une certaine colonne, les valeurs des lignes correspondantes sont null.
* Lorsque vous spécifiez un *ColumnType* avec un *ColumnName*, cette colonne dans le « résultat » aura le type donné, et les valeurs seront converties en ce type si nécessaire. La conversion n’aura pas d’effet sur le type de colonne lors de l’évaluation du *prédicat*.
* Lorsque `pack(*)` est utilisé, le reste des colonnes est empaqueté dans un conteneur de propriétés et apparaît dans une `pack_` colonne supplémentaire.

**pack_ colonne**

Cette colonne contient un conteneur de propriétés avec les données de toutes les colonnes qui n’apparaissent pas dans le schéma de sortie. Le nom de la colonne source servira de nom de propriété et la valeur de colonne servira de valeur de propriété.

## <a name="predicate-syntax"></a>Syntaxe de prédicat

L’opérateur de *recherche* prend en charge une autre syntaxe pour le `* has` terme, et l’utilisation d’un *terme*, recherchera un terme dans toutes les colonnes d’entrée.

Pour obtenir un résumé de certaines fonctions de filtrage, consultez [Where, opérateur](./whereoperator.md).

## <a name="notes"></a>Notes

* Si la `project` clause fait référence à une colonne qui apparaît dans plusieurs tables et possède plusieurs types, un type doit suivre cette référence de colonne dans la clause Project
* Si une colonne apparaît dans plusieurs tables et a plusieurs types et `project-smart` est en cours d’utilisation, il y aura une colonne correspondante pour chaque type dans le `find` résultat de, comme décrit dans [Union](./unionoperator.md)
* Lorsque vous utilisez *Project-Smart*, les modifications apportées au prédicat, dans les tables sources définies ou dans le schéma de la table, peuvent entraîner une modification du schéma de sortie. Si un schéma de résultat constant est nécessaire, utilisez plutôt *Project*
* `find` l’étendue ne peut pas inclure de [fonctions](../management/functions.md). Pour inclure une fonction dans l’étendue de recherche, définissez une [instruction Let](./letstatement.md) avec le [mot clé View](./letstatement.md).

## <a name="performance-tips"></a>Conseils sur les performances

* Utilisez des [tables](../management/tables.md) plutôt que des [expressions tabulaires](./tabularexpressionstatements.md).
Si l’expression est tabulaire, l’opérateur de recherche revient à une `union` requête qui peut entraîner une dégradation des performances.
* Si une colonne qui apparaît dans plusieurs tables et a plusieurs types, fait partie de la clause Project, préférez ajouter un *ColumnType* à la clause Project sur la modification de la table avant de la passer à `find` .
* Ajoutez des filtres basés sur le temps au prédicat. Utilisez une valeur de colonne datetime ou [ingestion_time ()](./ingestiontimefunction.md).
* Recherchez dans des colonnes spécifiques plutôt que dans une recherche en texte intégral.
* Il est préférable de ne pas faire référence à des colonnes qui apparaissent dans plusieurs tables et qui ont plusieurs types. Si le prédicat est valide lors de la résolution de ce type de colonnes pour plusieurs types, la requête revient à Union.
Par exemple, consultez [exemples de cas où la fonction Find se comporte comme une Union](./findoperator.md#examples-of-cases-where-find-will-act-as-union).
 
## <a name="examples"></a>Exemples

::: zone pivot="azuredataexplorer"

### <a name="term-lookup-across-all-tables-in-current-database"></a>Recherche de terme dans toutes les tables de la base de données active

La requête recherche toutes les lignes de toutes les tables de la base de données active dans lesquelles toute colonne contient le mot `Kusto` .
Les enregistrements résultants sont transformés en fonction du [schéma de sortie](#output-schema).

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>Recherche de terme dans toutes les tables correspondant à un modèle de nom dans la base de données active

La requête recherche toutes les lignes de toutes les tables de la base de données active dont le nom commence par `K` , et dans laquelle toute colonne contient le mot `Kusto` .
Les enregistrements résultants sont transformés en fonction du [schéma de sortie](#output-schema).

```kusto
find in (K*) where * has "Kusto"
```

### <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>Recherche de terme dans toutes les tables de toutes les bases de données du cluster

La requête recherche toutes les lignes de toutes les tables de toutes les bases de données dans lesquelles une colonne contient le mot `Kusto` .
Cette requête est une requête [de base de données croisée](./cross-cluster-or-database-queries.md) .
Les enregistrements résultants sont transformés en fonction du [schéma de sortie](#output-schema).

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>Recherche de terme dans toutes les tables et bases de données correspondant à un modèle de nom dans le cluster

La requête recherche toutes les lignes de toutes les tables dont le nom commence par `K` dans toutes les bases de données dont le nom commence par `B` et dans lequel une colonne contient le mot `Kusto` .
Les enregistrements résultants sont transformés en fonction du [schéma de sortie](#output-schema).

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>Recherche de terme dans plusieurs clusters

La requête recherche toutes les lignes de toutes les tables dont le nom commence par `K` dans toutes les bases de données dont le nom commence par `B` et dans lequel une colonne contient le mot `Kusto` .
Les enregistrements résultants sont transformés en fonction du [schéma de sortie](#output-schema).

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>Recherche de terme dans toutes les tables

La requête recherche toutes les lignes de toutes les tables dans lesquelles toute colonne contient le mot `Kusto` .
Les enregistrements résultants sont transformés en fonction du [schéma de sortie](#output-schema).

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>Exemples de `find` résultats de sortie  

Les exemples suivants montrent comment `find` peut être utilisé sur deux tables : *EventsTable1* et *EventsTable2*.
Supposons que nous ayons le contenu suivant de ces deux tables :

### <a name="eventstable1"></a>EventsTable1

|Session_Id|Level|EventText|Version
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Certaines Texte1|v1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Un Texte2|v1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|Error|Certains Text3|v1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|Information|Un Text4|v1.1.0

### <a name="eventstable2"></a>EventsTable2

|Session_Id|Level|EventText|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Information|Autre Texte1|Event1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Autre Texte2|Event2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Autre Text3|Event3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|Error|Autre Text4|Event4

### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>Recherchez dans des colonnes communes, projetez des colonnes communes et peu courantes, et Empaquetez le reste  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|EventText|Version|EventName|pack_
|---|---|---|---|---|
|EventsTable1|Un Texte2|v1.0.0||{« Session_Id » : « acbd207d-51aa-4df7-BFA7-be70eb68f04e », « Level » : « Error »}
|EventsTable2|Autre Text3||Event3|{« Session_Id » : « acbd207d-51aa-4df7-BFA7-be70eb68f04e », « Level » : « Error »}


### <a name="search-in-common-and-uncommon-columns"></a>Rechercher dans les colonnes courantes et inhabituelles

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|EventText|Version|EventName|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Certaines Texte1|v1.0.0
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Un Texte2|v1.0.0
|EventsTable2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Autre Texte1||Event1

Remarque : dans la pratique, les lignes *EventsTable1* sont filtrées avec le ```Version == 'v1.0.0'``` prédicat et les lignes *EventsTable2* sont filtrées avec le ```EventName == 'Event1'``` prédicat.

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>Utilisez la notation abrégée pour effectuer une recherche dans toutes les tables de la base de données active

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|Level|EventText|pack_|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Certaines Texte1|{"Version" : "v 1.0.0"}
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Un Texte2|{"Version" : "v 1.0.0"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Autre Texte2|{"EventName" : "event2"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Autre Text3|{"EventName" : "Event3"}


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>Retourne les résultats de chaque ligne en tant que conteneur de propriétés

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|EventsTable1|{« Session_Id » : « acbd207d-51aa-4df7-BFA7-be70eb68f04e », « Level » : « information », « EventText » : « Some Text1 », « version » : « v 1.0.0 »}
|EventsTable1|{« Session_Id » : « acbd207d-51aa-4df7-BFA7-be70eb68f04e », « Level » : « Error », « EventText » : « Some text2 », « version » : « v 1.0.0 »}
|EventsTable2|{« Session_Id » : « acbd207d-51aa-4df7-BFA7-be70eb68f04e », « Level » : « information », « EventText » : « Other text2 », « EventName » : « event2 »}
|EventsTable2|{« Session_Id » : « acbd207d-51aa-4df7-BFA7-be70eb68f04e », « Level » : « Error », « EventText » : « Some Other », « EventName » : « Event3 »}


## <a name="examples-of-cases-where-find-will-act-as-union"></a>Exemples de cas où `find` agira comme `union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>Utilisation d’une expression non tabulaire comme opérande de recherche

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>Référencement d’une colonne qui apparaît dans plusieurs tables et a plusieurs types

Supposons que nous ayons créé deux tables en exécutant : 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* La requête suivante sera exécutée en tant que `union` .

```kusto
find in (Table1, Table2) where ProcessId == 1001
```

Le schéma de résultat de sortie sera *(Level : String, timestamp, ProcessId_string, ProcessId_int)*.

* La requête suivante sera également exécutée en tant que `union` , mais produira un schéma de résultat différent.

```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

Le schéma de résultat de sortie sera *(niveau : chaîne, horodatage, ProcessId_string)*

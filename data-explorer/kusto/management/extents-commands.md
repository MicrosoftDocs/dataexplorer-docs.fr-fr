---
title: Étendues (partitions de données) gestion-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la gestion des étendues (partitions de données) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca66beaad41796dff38a5bd5ce1fad2e0f41fc72
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550501"
---
# <a name="extents-data-shards-management"></a>Gestion des étendues (partitions de données)

Les partitions de données sont des **Extensions** appelées dans Kusto, et toutes les commandes utilisent « extent » ou « extents » comme synonyme.

## <a name="show-extents"></a>. afficher les étendues

**Niveau de cluster**

`.show` `cluster` `extents` [`hot`]

Affiche des informations sur les étendues (partitions de données) qui sont présentes dans le cluster.
Si `hot` est spécifié, affiche uniquement les extensions qui sont supposées être dans le cache à chaud.

**Niveau de la base de données**

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,` *ExtentIdN* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *: Étiquette1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *tag2*...]]

Affiche des informations sur les étendues (partitions de données) qui sont présentes dans la base de données spécifiée.
Si `hot` est spécifié, affiche uniquement les étendues attendues dans le cache à chaud.

**Niveau table**

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,` *ExtentIdN* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *: Étiquette1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,` *TableNameN* `)` `extents`[ `(` *ExtentId1* `,` ... `,` *ExtentIdN* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *: Étiquette1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *tag2*...]]

Affiche des informations sur les étendues (partitions de données) qui sont présentes dans la ou les tables spécifiées (la base de données est extraite du contexte de la commande).
Si `hot` est spécifié, affiche uniquement les étendues attendues dans le cache à chaud.

**REMARQUES**

* L’utilisation de la base de données et/ou des commandes au niveau de la table est beaucoup plus rapide que le filtrage (ajout `| where DatabaseName == '...' and TableName == '...'` ) des résultats d’une commande au niveau du cluster.
* Si la liste facultative d’ID d’étendue est fournie, le jeu de données retourné est limité à ces étendues uniquement.
    * Là encore, cette opération est beaucoup plus rapide que le filtrage (ajout `| where ExtentId in(...)` à) des résultats des commandes « Bare ».
* Si `tags` des filtres sont spécifiés :
  * La liste retournée est limitée aux étendues dont la collection Tags obéit à *tous* les filtres de balises fournis.
    * Là encore, cette opération est beaucoup plus rapide que le filtrage (ajout `| where Tags has '...' and Tags contains '...'` ) des résultats des commandes « Bare ».
  * `has`les filtres sont des filtres d’égalité : les étendues qui ne sont pas marquées avec l’une des balises spécifiées sont exclues.
  * `!has`les filtres sont des filtres négatifs d’égalité : les étendues qui sont marquées avec l’une des balises spécifiées seront exclues.
  * `contains`les filtres sont des filtres de sous-chaîne ne respectant pas la casse : les étendues qui n’ont pas les chaînes spécifiées en tant que sous-chaîne d’une de leurs balises sont exclues. 
  * `!contains`les filtres sont des filtres négatifs de sous-chaîne non sensibles à la casse : les étendues qui ont les chaînes spécifiées sous la forme d’une sous-chaîne d’une de leurs balises sont exclues. 
  
  * **Exemples**
    * `E`L’étendue dans la table `T` est marquée avec des balises `aaa` , `BBB` et `ccc` .
    * Cette requête renverra `E` : 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * Cette requête ne *not* retourne pas `E` (car elle n’est pas marquée avec une balise qui est égale à `aa` ) :
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * Cette requête renverra `E` : 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|Paramètre de sortie |Type |Description 
|---|---|---
|ExtentId |Guid |ID de l’étendue 
|nom_base_de_données |String |Base de données à laquelle l’extension appartient 
|TableName |String |Table dont les étendues appartiennent à 
|MaxCreatedOn |DateTime |Date et heure de création de l’extension (pour une étendue fusionnée-la valeur maximale des heures de création parmi les extensions source) 
|OriginalSize |Double |Taille d’origine, en octets, des données d’étendue 
|Extenter |Double |Taille de l’étendue en mémoire (compressée + index) 
|CompressedSize |Double |Taille compressée des données d’étendue en mémoire 
|IndexSize |Double |Taille de l’index des données d’étendue 
|Blocs |Long |Quantité de blocs de données dans l’étendue 
|Segments |Long |Quantité de segment de données dans l’étendue 
|AssignedDataNodes |String | Déconseillé (chaîne vide)
|LoadedDataNodes |String |Déconseillé (chaîne vide)
|ExtentContainerId |String | ID du conteneur d’étendues dans lequel se trouve l’étendue
|RowCount |Long |Nombre de lignes dans l’étendue
|MinCreatedOn |DateTime |Date et heure de création de l’extension (pour une étendue fusionnée-le nombre minimal de fois de création entre des étendues sources) 
|Étiquettes|String|Balises, le cas échéant, définies pour l’étendue 
 
**Exemples**

```kusto 
// Show volume of extents being created per hour in a specific database
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  

// Show volume of data arriving by table per hour 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart

// Show data size distribution by table 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName

// Show all extents in the database named 'GamesDB' 
.show database GamesDB extents

// Show all extents in the table named 'Games' 
.show table Games extents

// Show all extents in the tables named 'TaggingGames1' and 'TaggingGames2', tagged with both 'tag1' and 'tag2' 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
``` 
 
## <a name="merge-extents"></a>. étendues de fusion

**Syntaxe**

`.merge``[async | dryrun]` *TableName* `(` *guid1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

Cette commande fusionne les étendues (voir la [vue d’ensemble des étendues (données partitions)](extents-overview.md)) indiquées par leurs ID dans la table spécifiée.

Il existe 2 versions pour les opérations de fusion : la *fusion* (qui reconstruit les index) et la *régénération* (qui recrée complètement les données).

* `async`: L’opération sera exécutée de façon asynchrone : le résultat sera un ID d’opération (GUID), avec lequel il est possible d’exécuter `.show operations <operation ID>` , pour afficher l’état de la commande & État.
* `dryrun`: L’opération répertorie uniquement les étendues qui doivent être fusionnées, mais ne les fusionne pas réellement. 
* `with(rebuild=true)`: les étendues sont régénérées (les données sont rereçues) au lieu d’être fusionnées (les index seront reconstruits).

**Sortie de retour**

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’étendue d’origine dans la table source, qui a été fusionné dans l’étendue cible. 
ResultExtentId |string |Identificateur unique (GUID) pour l’étendue du résultat, qui a été créé à partir des étendues sources. En cas d’échec : « échec » ou « abandonné ».
Duration |intervalle de temps |Durée nécessaire pour effectuer l’opération de fusion.

**Exemples**

Reconstruit deux extensions spécifiques dans `MyTable` , exécutées de façon asynchrone
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

Fusionne deux extensions spécifiques dans `MyTable` , exécutées de façon synchrone
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**Remarques**
- En général, les `.merge` commandes doivent rarement être exécutées manuellement, et elles sont exécutées automatiquement en arrière-plan du cluster Kusto (en fonction des [stratégies de fusion](mergepolicy.md) définies pour les tables et les bases de données qu’elle contient).  
  - Les considérations générales sur les critères de fusion de plusieurs extensions en une seule sont documentées sous [stratégie de fusion](mergepolicy.md).
- `.merge`les opérations ont un état final possible de `Abandoned` (qui peut être affiché lors de `.show operations` l’exécution avec l’ID d’opération) : cet État suggère que les étendues de source n’ont pas été fusionnées ensemble, car certaines étendues source n’existent plus dans la table source.
  - Un tel état est supposé se produire dans les cas suivants :
     - Les étendues sources ont été supprimées dans le cadre de la rétention de la table.
     - Les étendues sources ont été déplacées vers une autre table.
     - La table source a été entièrement supprimée ou renommée.

## <a name="move-extents"></a>. déplacer des étendues

**Syntaxe**

`.move`[ `async` ] `extents` `all` `from` `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[ `async` ] `extents` `(` *Guid1* [ `,` *GUID2* ...] `)` `from` `table` *SourceTableName* `to` `table` *DestinationTableName* 

`.move`[ `async` ] `extents` `to` `table` *DestinationTableName*  <|  *Requête* DestinationTableName

Cette commande s’exécute dans le contexte d’une base de données spécifique et déplace les étendues spécifiées de la table source vers la table de destination de façon transactionnelle.
Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour les tables source et de destination.

* `async`(facultatif) spécifie si la commande est exécutée de façon asynchrone (dans ce cas, un ID d’opération (Guid) est retourné et si l’état de l’opération peut être surveillé à l’aide de la commande [. afficher les opérations](operations.md#show-operations) ).
    * Si cette option est utilisée, les résultats d’une exécution réussie peuvent être récupérés dans la commande [. afficher les détails](operations.md#show-operation-details) de l’opération.

Il existe trois façons de spécifier les étendues à déplacer :
1. Toutes les étendues d’une table spécifique doivent être déplacées.
2. En spécifiant explicitement les ID d’étendue dans la table source.
3. En fournissant une requête dont les résultats spécifient les ID d’étendue dans la ou les tables sources.

**Restrictions**
- Les tables source et de destination doivent être dans la base de données de contexte. 
- Toutes les colonnes de la table source sont censées exister dans la table de destination avec le même nom et le même type de données.

**Spécification d’étendues à l’aide d’une requête**

```kusto 
.move extents to table TableName <| ...query... 
```

Les étendues sont spécifiées à l’aide d’une requête Kusto qui retourne un jeu d’enregistrements avec une colonne appelée « ExtentId ». 

**Sortie de retour** (pour l’exécution de la synchronisation)

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’étendue d’origine dans la table source, qui a été déplacée vers la table de destination. 
ResultExtentId |string |Identificateur unique (GUID) pour l’étendue du résultat, qui a été déplacé de la table source vers la table de destination. En cas d’échec-« échec ».
Détails |string |Contient les détails de l’échec, en cas d’échec de l’opération.

**Exemples**

Déplace toutes les étendues de la table `MyTable` vers la table `MyOtherTable` .
```kusto
.move extents all from table MyTable to table MyOtherTable
```

Déplace 2 extensions spécifiques (par ID d’étendue) de la table `MyTable` à la table `MyOtherTable` .
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

Déplace toutes les étendues de 2 tables spécifiques ( `MyTable1` , `MyTable2` ) vers la table `MyOtherTable` .
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**Exemple de sortie** 

|OriginalExtentId |ResultExtentId| Détails
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>. extensions Drop

Supprime les étendues de la base de données/table spécifiée. Cette commande a plusieurs variantes : dans une seule variante, les étendues à supprimer sont spécifiées par une requête Kusto, tandis que dans les autres extensions de variantes sont spécifiées à l’aide d’un mini-langage décrit ci-dessous. 
 
### <a name="specifying-extents-with-a-query"></a>Spécification d’étendues à l’aide d’une requête

Nécessite l’autorisation de l' [administrateur de table](../management/access-control/role-based-authorization.md) foreach des tables qui ont des étendues retournées par la requête fournie.

Supprime les extensions (ou simplement les signale sans les supprimer si `whatif` est utilisé) :

**Syntaxe**

`.drop``extents`[ `whatif` ] <| *requête*

Les étendues sont spécifiées à l’aide d’une requête Kusto qui retourne un jeu d’enregistrements avec une colonne appelée « ExtentId ». 
 
### <a name="dropping-a-specific-extent"></a>Suppression d’une étendue spécifique

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) dans le cas où le nom de la table est spécifié.

Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md) au cas où le nom de la table n’est pas spécifié.

**Syntaxe**

`.drop``extent` *ExtentId* [ `from` *TableName*]

### <a name="dropping-specific-multiple-extents"></a>Suppression de plusieurs extensions spécifiques

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) dans le cas où le nom de la table est spécifié.

Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md) au cas où le nom de la table n’est pas spécifié.

**Syntaxe**

`.drop``extents` `(` *ExtentId1* `,` ... *ExtentIdN* `)` [ `from` *TableName*]

### <a name="specifying-extents-by-properties"></a>Spécification d’étendues par propriétés

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) dans le cas où le nom de la table est spécifié.

Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md) au cas où le nom de la table n’est pas spécifié.

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *n* () `MB`  |  `GB`  |  `bytes` ] [ `limit` *LimitCount*]

* `older`: Seules les extensions datant de plus de *N* jours/heures seront supprimées. 
* `trim`: L’opération supprimera les données dans la base de données jusqu’à ce que la somme des étendues corresponde à la taille souhaitée (MaxSize) 
* `limit`: L’opération sera appliquée aux premières étendues *LimitCount* 

La commande prend en charge le `.drop-pretend` mode d’émulation (au lieu de `.drop` ), qui produit une sortie comme si la commande avait été exécutée, mais sans réellement l’exécuter.

**Exemples**

Supprimer toutes les étendues créées il y a plus de 10 jours dans toutes les tables de la base de données`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

Supprime toutes les étendues dans les tables `Table1` et `Table2` , dont l’heure de création a eu lieu il y a plus de 10 jours :

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

Mode d’émulation : affiche les étendues qui sont supprimées par la commande (le paramètre d’ID d’étendue n’est pas applicable pour cette commande) :

```kusto
.drop-pretend extents older 10 days from all tables
```

Supprime toutes les étendues de « TestTable » :

```kusto
.drop extents from TestTable
```
 
**Sortie de retour**

|Paramètre de sortie |Type |Description 
|---|---|---
|ExtentId |String |ExtentId qui a été supprimé à la suite de la commande 
|TableName |String |Nom de la table, où l’étendue appartenait  
|Created |DateTime |Horodateur qui contient des informations sur la création initiale de l’extension 
 
**Exemple de sortie** 

|ID d’étendue |Nom de la table |Créée le 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 

## <a name="replace-extents"></a>. remplacement des extensions

**Syntaxe**

`.replace`[ `async` ] `extents` `in` `table` *DestinationTableName* `<| 
{` *Requête DestinationTableName pour les étendues à supprimer de*la requête de table `},{` *pour les étendues à déplacer vers la table*`}`

Cette commande s’exécute dans le contexte d’une base de données spécifique, déplace les étendues spécifiées à partir de leurs tables sources vers la table de destination et supprime les étendues spécifiées de la table de destination.
Toutes les opérations de déplacement et de déplacement sont effectuées dans une transaction unique.

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour les tables source et de destination.

* `async`(facultatif) spécifie si la commande est exécutée de façon asynchrone (dans ce cas, un ID d’opération (Guid) est retourné et si l’état de l’opération peut être surveillé à l’aide de la commande [. afficher les opérations](operations.md#show-operations) ).
    * Si cette option est utilisée, les résultats d’une exécution réussie peuvent être récupérés dans la commande [. afficher les détails](operations.md#show-operation-details) de l’opération.

La spécification des étendues qui doivent être supprimées ou déplacées est effectuée en fournissant 2 requêtes
- *requête d’étendues à supprimer de la table* : les résultats de cette requête spécifient les ID d’étendue.  
qui doit être supprimé de la table de destination.
- *interroger les étendues à déplacer vers la table* : les résultats de cette requête spécifient les ID d’étendue dans la ou les tables source qui doivent être déplacées vers la table de destination.

Les deux requêtes doivent retourner un jeu d’enregistrements avec une colonne appelée « ExtentId ».

**Restrictions**
- Les tables source et de destination doivent être dans la base de données de contexte. 
- Toutes les étendues spécifiées par la *requête pour les étendues à supprimer de la table* sont censées appartenir à la table de destination.
- Toutes les colonnes des tables sources sont supposées exister dans la table de destination avec le même nom et le même type de données.

**Sortie de retour** (pour l’exécution de la synchronisation)

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’étendue d’origine dans la table source, qui a été déplacée vers la table de destination, ou-l’étendue de la table de destination, qui a été supprimée.
ResultExtentId |string |Identificateur unique (GUID) pour l’étendue du résultat, qui a été déplacé de la table source vers la table de destination, ou-Empty, dans le cas où l’étendue a été supprimée de la table de destination. En cas d’échec-« échec ».
Détails |string |Contient les détails de l’échec, en cas d’échec de l’opération.

> [!NOTE]
> La commande échoue si les étendues retournées par les *étendues à supprimer de* la requête de table n’existent pas dans la table de destination. Cela peut se produire si les étendues ont été fusionnées avant l’exécution de la commande Replace. Pour vous assurer que la commande échoue sur les extensions manquantes, vérifiez que la requête retourne le ExtentIds attendu. L’exemple de #1 ci-dessous échoue si l’étendue à supprimer n’existe pas dans la table MyOtherTable. Toutefois, l’exemple de #2 réussit même si l’étendue à supprimer n’existe pas, puisque la requête à supprimer n’a retourné aucun ID d’étendue. 

**Exemples**

La commande suivante déplace toutes les étendues de 2 tables spécifiques ( `MyTable1` , `MyTable2` ) vers la table `MyOtherTable` , et supprime toutes les étendues dans les `MyOtherTable` balises avec `drop-by:MyTag` :

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

**Exemple de sortie** 

|OriginalExtentId |ResultExtentId |Détails
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 


Les commandes suivantes déplacent toutes les étendues de 1 table spécifique ( `MyTable1` ) à la table `MyOtherTable` et déposent une étendue spécifique dans `MyOtherTable` , à l’aide de son ID :


```kusto
.replace extents in table MyOtherTable <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable  <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

La commande suivante implémente une logique idempotent, afin qu’elle ignore les étendues de la table `t_dest` uniquement si des étendues peuvent être déplacées d’une table `t_source` vers une table `t_dest` :

```kusto
.replace async extents in table t_dest <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```

## <a name="drop-extent-tags"></a>. supprimer des balises d’étendue

**Syntaxe**

`.drop`[ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*: étiquette1*' [ `,` '*tag2*' `,` ... `,` ' *TagN*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *requête*

* `async`(facultatif) spécifie si la commande est exécutée de façon asynchrone (dans ce cas, un ID d’opération (Guid) est retourné et si l’état de l’opération peut être surveillé à l’aide de la commande [. afficher les opérations](operations.md#show-operations) ).
    * Si cette option est utilisée, les résultats d’une exécution réussie peuvent être récupérés dans la commande [. afficher les détails](operations.md#show-operation-details) de l’opération.

La commande s’exécute dans le contexte d’une base de données spécifique et supprime la ou les [balises d’étendue](extents-overview.md#extent-tagging) fournies de toute extension de la base de données et de la table fournies, y compris les balises.  

Il existe deux façons de spécifier les balises à supprimer des extensions :

1. En spécifiant explicitement les balises qui doivent être supprimées de toutes les étendues dans la table spécifiée.
2. En fournissant une requête dont les résultats spécifient les ID d’étendue dans la table et l’étendue foreach-les balises qui doivent être supprimées.

**Restrictions**
- Toutes les étendues doivent être dans la base de données de contexte et doivent appartenir à la même table. 

**Spécification d’étendues à l’aide d’une requête**

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour toutes les tables source et de destination impliquées.

```kusto 
.drop extent tags <| ...query... 
```

Les étendues et les balises à supprimer sont spécifiées à l’aide d’une requête Kusto qui retourne un jeu d’enregistrements avec une colonne appelée « ExtentId » et une colonne appelée « Tags ». 

*Remarque*: lorsque vous utilisez la [bibliothèque cliente .net Kusto](../api/netfx/about-kusto-data.md), vous pouvez utiliser les méthodes suivantes pour générer la commande souhaitée :
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**Sortie de retour**

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’extension d’origine dont les balises ont été modifiées (et qui est supprimée dans le cadre de l’opération). 
ResultExtentId |string |Identificateur unique (GUID) de l’extension de résultat qui a modifié les balises (et qui est créé et ajouté dans le cadre de l’opération). En cas d’échec-« échec ».
ResultExtentTags |string |Collection de balises avec lesquelles l’étendue du résultat est marquée (le cas échéant) ou « null » en cas d’échec de l’opération.
Détails |string |Contient les détails de l’échec, en cas d’échec de l’opération.

**Exemples**

Supprime la `drop-by:Partition000` balise de toute extension de la table `MyOtherTable` qui est marquée avec celle-ci.

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

Supprime les balises `drop-by:20160810104500` , `a random tag` et/ou `drop-by:20160810` de n’importe quelle étendue dans `My Table` la table qui est marquée avec l’une ou l’autre de ces balises.

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

Supprime toutes les `drop-by` balises des étendues dans la table `MyTable` .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

Supprime toutes les balises correspondant `drop-by:StreamCreationTime_20160915(\d{6})` à Regex des étendues dans la table `MyTable` .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**Exemple de sortie** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | Détails
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>. Alter, balises d’étendue

**Syntaxe**

`.alter`[ `async` ] `extent` `tags` `(` '*: Étiquette1*' [ `,` '*tag2*' `,` ... `,` ' *TagN*'] `)`  <|  *requête*

La commande s’exécute dans le contexte d’une base de données spécifique et modifie la ou les [balises d’étendue](extents-overview.md#extent-tagging) de toutes les étendues retournées par la requête spécifiée, à l’ensemble de balises fourni.

Les étendues et les balises à modifier sont spécifiées à l’aide d’une requête Kusto qui retourne un jeu d’enregistrements avec une colonne appelée « ExtentId ».

* `async`(facultatif) spécifie si la commande est exécutée de façon asynchrone (dans ce cas, un ID d’opération (Guid) est retourné et si l’état de l’opération peut être surveillé à l’aide de la commande [. afficher les opérations](operations.md#show-operations) ).
    * Si cette option est utilisée, les résultats d’une exécution réussie peuvent être récupérés dans la commande [. afficher les détails](operations.md#show-operation-details) de l’opération.

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour toutes les tables impliquées.

**Restrictions**
- Toutes les étendues doivent être dans la base de données de contexte et doivent appartenir à la même table. 

**Sortie de retour**

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’extension d’origine dont les balises ont été modifiées (et qui est supprimée dans le cadre de l’opération). 
ResultExtentId |string |Identificateur unique (GUID) de l’extension de résultat qui a modifié les balises (et qui est créé et ajouté dans le cadre de l’opération). En cas d’échec-« échec ».
ResultExtentTags |string |Collection de balises avec lesquelles l’étendue du résultat est marquée, ou « null » en cas d’échec de l’opération.
Détails |string |Contient les détails de l’échec, en cas d’échec de l’opération.

**Exemples**

Modifie les balises de toutes les étendues de la table en `MyTable` `MyTag` .

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

Modifie les balises de toutes les étendues de la table `MyTable` , marquées avec `drop-by:MyTag` à `drop-by:MyNewTag` et `MyOtherNewTag` .

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**Exemple de sortie** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | Détails
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Drop-by : MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | Drop-by : MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f | Drop-by : MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | Drop-by : MyNewTag MyOtherNewTag| 


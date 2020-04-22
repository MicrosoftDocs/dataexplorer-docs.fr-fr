---
title: Gestion des étendues (éclats de données) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des étendues (éclats de données) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e94b830809bf079e612b477292d00d2dbe85e60e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744730"
---
# <a name="extents-data-shards-management"></a>Gestion des étendues (éclats de données)

Les éclats de données sont appelés **étendues** dans Kusto, et toutes les commandes utilisent « l’étendue » ou les « étendues » comme synonyme.

## <a name="show-extents"></a>.afficher les étendues

**Niveau cluster**

`.show` `cluster` `extents` [`hot`]

Affiche des informations sur les étendues (éclats de données) qui sont présents dans le cluster.
Si `hot` elle est spécifiée - montre uniquement les étendues qui devraient être dans le cache chaud.

**Niveau de base de données**

`.show``database` *DatabaseName* `extents` `(`[*ExtentId1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`] [ )`has`|`contains`|`!has`| *Tag1* [`and` `tags` (`!contains`) *Tag2*...]]

Affiche des informations sur les étendues (éclats de données) qui sont présentes dans la base de données spécifiée.
Si `hot` elle est spécifiée - montre uniquement les étendues qui s’attendaient à être dans le cache chaud.

**Niveau table**

`.show``table` *TableName* `extents` `(`[*ExtentId1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`] [ )`has`|`contains`|`!has`| *Tag1* [`and` `tags` (`!contains`) *Tag2*...]]

`.show``tables` `,`TableName1 ... *TableName1* `(` `,` *TableNameN* `)` `extents` `(`[*ExtentId1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`] [ )`has`|`contains`|`!has`| *Tag1* [`and` `tags` (`!contains`) *Tag2*...]]

Affiche des informations sur les étendues (les fragments de données) qui sont présents dans le tableau spécifié (la base de données est tirée du contexte de la commande).
Si `hot` elle est spécifiée - montre uniquement les étendues qui s’attendaient à être dans le cache chaud.

**REMARQUES**

* L’utilisation de commandes de base de données et/ou de niveau de table est beaucoup plus rapide que le filtrage (ajout) `| where DatabaseName == '...' and TableName == '...'`des résultats d’une commande au niveau du cluster.
* Si la liste facultative des DIU d’étendue est fournie, l’ensemble de données retournés est limité à ces seules étendues.
    * Encore une fois, c’est `| where ExtentId in(...)` beaucoup plus rapide que le filtrage (en ajoutant) les résultats des commandes « nues ».
* Dans `tags` le cas où les filtres sont spécifiés :
  * La liste retournée est limitée aux étendues dont la collection d’étiquettes obéit à *tous les* filtres de balises fournis.
    * Encore une fois, c’est `| where Tags has '...' and Tags contains '...'`beaucoup plus rapide que le filtrage (en ajoutant) les résultats de commandes "nues".
  * `has`les filtres sont des filtres d’égalité : les étendues qui ne sont pas étiquetées avec l’une ou l’autre des balises spécifiées seront filtrées.
  * `!has`les filtres sont des filtres négatifs d’égalité : les étendues qui sont étiquetées avec l’une ou l’autre des balises spécifiées seront filtrées.
  * `contains`les filtres sont des filtres de sous-cordes insensibles aux cas : les étendues qui n’ont pas les chaînes spécifiées comme sous-corde de l’une de leurs étiquettes seront filtrées. 
  * `!contains`les filtres sont des filtres négatifs de sous-cordes insensibles aux cas : les étendues qui ont les chaînes spécifiées comme une sous-corde de n’importe laquelle de leurs étiquettes seront filtrées. 
  
  * **Exemples**
    * `E` L’étendue `T` dans le `aaa`tableau `BBB` `ccc`est étiquetée avec des étiquettes, et .
    * Cette requête reviendra `E`: 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * Cette requête *ne* `E` reviendra pas (car elle n’est pas `aa`étiquetée avec une étiquette qui est égale à ):
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * Cette requête reviendra `E`: 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|Paramètre de sortie |Type |Description 
|---|---|---
|ExtentId (extentId) |Guid |ID de l’étendue 
|nom_base_de_données |String |Base de données qui appartient à 
|TableName |String |Tableau auquel les étendues appartiennent 
|MaxCreatedOn (en anglais) |DateTime |Date-temps où l’étendue a été créée (pour une ampleur fusionnée - le maximum de temps de création entre les étendues de source) 
|OriginalSize |Double |Taille originale dans les octets des données d’étendue 
|ExtentSize (en) |Double |Taille de l’étendue de la mémoire (compressé et index) 
|CompressésSize |Double |Taille comprimée des données d’étendue dans la mémoire 
|IndexSize |Double |Taille de l’indice des données d’étendue 
|Blocs |Long |Quantité de blocs de données dans l’étendue 
|Segments |Long |Quantité de segment de données dans l’étendue 
|AssignedDataNodes |String | Déprécié (Une ficelle vide)
|LoadedDataNodes |String |Déprécié (Une ficelle vide)
|ExtentContainerId |String | ID de l’étendue du conteneur dans lequel se trouve l’étendue
|RowCount |Long |Quantité de lignes dans l’étendue
|MinCreatedOn |DateTime |Date-temps où l’étendue a été créée (pour une ampleur fusionnée - le minimum de temps de création entre les étendues de source) 
|Balises|String|Tags, le cas échéant, définis pour l’étendue 
 
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
 
## <a name="merge-extents"></a>.merge étendues

**Syntaxe**

`.merge``[async | dryrun]` *TableName* `(` *GUID1* [`,` *GUID2* ...] `)``[with(rebuild=true)]`

Cette commande fusionne les étendues (voir: [Extents (Data Shards) Aperçu](extents-overview.md)) indiquées par leurs cartes d’ID dans le tableau spécifié.

Il y a 2 saveurs pour les opérations de fusion : *Fusionner* (qui reconstruit les index), et *Reconstruire* (qui reingest complètement les données).

* `async`: L’opération sera effectuée asynchronement - le résultat sera une pièce `.show operations <operation ID>` d’identité d’opération (GUID), que l’on peut exécuter avec, pour voir l’état de la commande & statut.
* `dryrun`: L’opération ne répertoriera que les étendues qui devraient être fusionnées, mais ne les fusionnera pas réellement. 
* `with(rebuild=true)`: les étendues seront reconstruites (les données seront réintégées) au lieu d’être fusionnées (les index seront reconstruits).

**Sortie de retour**

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Un identifiant unique (GUID) pour l’étendue originale du tableau source, qui a été fusionné dans l’étendue cible. 
RésultatExtentId |string |Un identifiant unique (GUID) pour l’étendue du résultat qui a été créé à partir des étendues de source. Sur l’échec - "Failed" ou "Abandoned".
Duration |intervalle de temps |Le temps qu’il a fallu pour terminer l’opération de fusion.

**Exemples**

Reconstruit deux étendues `MyTable`spécifiques dans , effectué asynchroneously
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

Fusionne deux étendues `MyTable`spécifiques dans , effectué de façon synchrone
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**Remarques**
- En général, `.merge` les commandes doivent rarement être exécutées manuellement, et elles sont continuellement exécutées automatiquement à l’arrière-plan du cluster Kusto (selon les [politiques de fusion](mergepolicy.md) définies pour les tables et les bases de données qu’il y a).  
  - Les considérations générales concernant les critères de fusion de plusieurs étendues en une seule sont documentées dans le cadre [de la politique de fusion](mergepolicy.md).
- `.merge`opérations ont un état `Abandoned` final possible de `.show operations` (qui peut être vu lors de l’exécution avec l’ID d’opération) - cet état suggère que les étendues de source n’ont pas été fusionnées, comme certaines des étendues de source n’existent plus dans le tableau source.
  - On s’attend à ce qu’un tel état se produise dans des cas tels que :
     - Les étendues de source ont été supprimées dans le cadre de la rétention de la table.
     - Les étendues de source ont été déplacées à une table différente.
     - Le tableau source a été entièrement supprimé ou renommé.

## <a name="move-extents"></a>.déplacer les étendues

**Syntaxe**

`.move`[`async` `extents` `all` `from` ] `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[`async` `extents` `(` ] *GUID1* [`,` *GUID2* ...] `)` `to` `table` *DestinationTableName* *SourceTableName* Destination DestinationTableName SourceTableName `from` `table` 

`.move`[`async` `extents` `to` `table` ] Question de *destinationTableName* <| *query*

Cette commande s’exécute dans le cadre d’une base de données spécifique, et déplace les étendues spécifiées de la table source à la table de destination transactionnellement.
Nécessite [l’autorisation d’administration de table](../management/access-control/role-based-authorization.md) pour les tables de source et de destination.

* `async`(facultatif) précise si la commande est exécutée asynchrone (auquel cas, une pièce d’identité d’opération (Guid) est retournée, et l’état de l’opération peut être surveillé à l’aide de la commande [d’opérations .show).](operations.md#show-operations)
    * Dans le cas où cette option est utilisée, les résultats d’une exécution réussie peuvent être récupérés la commande [de détails d’opération .show).](operations.md#show-operation-details)

Il y a trois façons de préciser les étendues à déplacer :
1. Toutes les étendues d’une table spécifique doivent être déplacées.
2. En précisant explicitement l’étendue des cartes d’ID dans le tableau source.
3. En fournissant une requête dont les résultats spécifient l’étendue des cartes d’ID dans le tableau source.

**Restrictions**
- Les tables de source et de destination doivent être dans la base de données contextuelle. 
- Toutes les colonnes du tableau source sont censées exister dans le tableau de destination du même nom et du même type de données.

**Spécifier les étendues avec une requête**

```kusto 
.move extents to table TableName <| ...query... 
```

Les étendues sont spécifiées à l’aide d’une requête Kusto qui renvoie un enregistrement avec une colonne appelée "ExtentId". 

**Sortie de retour** (pour l’exécution de synchronisation)

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Un identifiant unique (GUID) pour l’étendue d’origine dans le tableau source, qui a été déplacé à la table de destination. 
RésultatExtentId |string |Un identifiant unique (GUID) pour l’étendue du résultat qui a été déplacé de la table source à la table de destination. Sur l’échec - "Échec".
Détails |string |Inclut les détails de l’échec, en cas d’échec de l’opération.

**Exemples**

Déplace toutes les `MyTable` étendues `MyOtherTable`dans la table à la table .
```kusto
.move extents all from table MyTable to table MyOtherTable
```

Déplace 2 étendues spécifiques (par leur mesure `MyTable` les `MyOtherTable`cartes d’Œd) de table en table.
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

Déplace toutes les étendues de`MyTable1` `MyTable2`2 `MyOtherTable`tables spécifiques ( , ) à la table .
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**Exemple de sortie** 

|OriginalExtentId |RésultatExtentId| Détails
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>.drop étendues

Baisse les étendues de la base de données/tableau spécifié. Cette commande a plusieurs variantes : Dans une variante, les étendues à laisser tomber sont spécifiées par une requête De Kusto, tandis que dans les autres variantes, des étendues sont spécifiées à l’aide d’une mini-langue décrite ci-dessous. 
 
### <a name="specifying-extents-with-a-query"></a>Spécifier les étendues avec une requête

Nécessite [l’autorisation d’administration](../management/access-control/role-based-authorization.md) de la table de l’avant-plan des tables qui ont des étendues retournées par la requête fournie.

Les étendues de gouttes (ou `whatif` simplement les signalent sans réellement tomber si elle est utilisée) :

**Syntaxe**

`.drop``extents` [`whatif`] <*requête*

Les étendues sont spécifiées à l’aide d’une requête Kusto qui renvoie un enregistrement avec une colonne appelée "ExtentId". 
 
### <a name="dropping-a-specific-extent"></a>Baisse d’une ampleur spécifique

Nécessite [l’autorisation d’administration de table](../management/access-control/role-based-authorization.md) dans le cas où le nom de table est spécifié.

Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md) dans le nom de table de cas n’est pas spécifié.

**Syntaxe**

`.drop``extent` *ExtentId* `from` [ *Nom de table*]

### <a name="dropping-specific-multiple-extents"></a>Baisse des étendues multiples spécifiques

Nécessite [l’autorisation d’administration de table](../management/access-control/role-based-authorization.md) dans le cas où le nom de table est spécifié.

Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md) dans le nom de table de cas n’est pas spécifié.

**Syntaxe**

`.drop``extents` `,`ExtentId1 ... *ExtentId1* `(` *ExtentIdN* `)` `from` [ *Nom de table*]

### <a name="specifying-extents-by-properties"></a>Spécifier les étendues par propriétés

Nécessite [l’autorisation d’administration de table](../management/access-control/role-based-authorization.md) dans le cas où le nom de table est spécifié.

Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md) dans le nom de table de cas n’est pas spécifié.

`.drop``extents` [`older` *N* N`days` | `hours`( `from` )] (*TableName*  |  `all` `tables``MB` | `GB` | `bytes`) [`trim` `by` `extentsize` | `datasize`( ) *N* ( )] [`limit` *LimitCount*]

* `older`: Seules les étendues plus anciennes que les jours et les heures *de N* seront supprimées. 
* `trim`: L’opération permettra de réduire les données dans la base de données jusqu’à ce que la somme des étendues corresponde à la taille souhaitée (MaxSize) 
* `limit`: L’opération sera appliquée aux premières étendues *de LimitCount* 

La commande prend`.drop-pretend` en `.drop`charge l’émulation ( au lieu) mode, qui produit une sortie comme si la commande aurait couru, mais sans réellement l’exécuter.

**Exemples**

Supprimer toutes les étendues créées il y a plus de 10 jours à partir de toutes les tables de la base de données`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

Supprime toutes les étendues dans les tables `Table1` et `Table2`, dont le temps de création a été plus de 10 jours:

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

Mode d’émulation : indique quelles étendues seraient supprimées par la commande (le paramètre d’identification d’étendue n’est pas applicable pour cette commande ) :

```kusto
.drop-pretend extents older 10 days from all tables
```

Supprime toutes les étendues de 'TestTable':

```kusto
.drop extents from TestTable
```
 
**Sortie de retour**

|Paramètre de sortie |Type |Description 
|---|---|---
|ExtentId (extentId) |String |ExtentId qui a été abandonné à la suite de la commande 
|TableName |String |Nom de table, où l’étendue appartenait  
|CrééOn |DateTime |Timestamp qui contient des informations sur le moment où l’étendue a été initialement créée 
 
**Exemple de sortie** 

|Id Étendue |Nom de la table |Créée le 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 

## <a name="replace-extents"></a>.remplacer les étendues

**Syntaxe**

`.replace`[`async` `extents` `in` `table` ] *DestinationTableName* `<| 
{` *requête pour les étendues à laisser tomber de la*`},{`requête de table*pour les étendues à déplacer à la table*`}`

Cette commande s’exécute dans le cadre d’une base de données spécifique, déplace les étendues spécifiées de leurs tables sources vers le tableau de destination, et laisse tomber les étendues spécifiées à partir du tableau de destination.
Toutes les opérations de baisse et de déménagement se font en une seule transaction.

Nécessite [l’autorisation d’administration de table](../management/access-control/role-based-authorization.md) pour les tables de source et de destination.

* `async`(facultatif) précise si la commande est exécutée asynchrone (auquel cas, une pièce d’identité d’opération (Guid) est retournée, et l’état de l’opération peut être surveillé à l’aide de la commande [d’opérations .show).](operations.md#show-operations)
    * Dans le cas où cette option est utilisée, les résultats d’une exécution réussie peuvent être récupérés la commande [de détails d’opération .show).](operations.md#show-operation-details)

Spécifier les étendues à laisser tomber ou à déplacer est fait en fournissant 2 requêtes
- *demande pour les étendues à laisser tomber de la table* - les résultats de cette requête spécifient l’étendue des DI  
qui devrait être retiré de la table de destination.
- *demande pour les étendues à déplacer à la table* - les résultats de cette requête spécifient l’étendue des cartes d’ID dans le tableau source(s) qui doit être déplacé à la table de destination.

Les deux requêtes doivent retourner un jeu d’enregistrement avec une colonne appelée "ExtentId".

**Restrictions**
- Les tables de source et de destination doivent être dans la base de données contextuelle. 
- Toutes les étendues spécifiées par la requête pour que *les étendues soient retirées de* la table devraient appartenir à la table de destination.
- Toutes les colonnes dans les tableaux sources sont censées exister dans le tableau de destination avec le même nom et le même type de données.

**Sortie de retour** (pour l’exécution de synchronisation)

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Un identifiant unique (GUID) pour l’étendue d’origine dans le tableau source, qui a été déplacé à la table de destination, ou - l’étendue dans le tableau de destination, qui a été abandonné.
RésultatExtentId |string |Un identifiant unique (GUID) pour l’étendue du résultat qui a été déplacé de la table source à la table de destination, ou - vide, au cas où l’étendue a été supprimée de la table de destination. Sur l’échec - "Échec".
Détails |string |Inclut les détails de l’échec, en cas d’échec de l’opération.

**Exemples**

Déplace toutes les étendues de`MyTable1` `MyTable2`2 `MyOtherTable`tables spécifiques ( , `MyOtherTable` ) `drop-by:MyTag`à la table , et laisse tomber toutes les étendues dans étiqueté avec :

```kusto
.replace extents in table MyOtherTable <|
    {.show table MyOtherTable extents where tags has 'drop-by:MyTag'},
    {.show tables (MyTable1,MyTable2) extents}
```

**Exemple de sortie** 

|OriginalExtentId |RésultatExtentId |Détails
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

*REMARQUE* : 
> [!NOTE]
> La commande échouera si les étendues retournées par les *étendues à laisser tomber de la* requête de table n’existent pas dans le tableau de destination. Cela peut se produire si les étendues ont été fusionnées avant l’exécution du commandement de remplacement. Pour vous assurer que la commande échoue sur les étendues manquantes, vérifiez que la requête renvoie les ExtentIds attendus. Exemple #1 ci-dessous échouera si la mesure de chute n’existe pas dans le tableau MyOtherTable. Exemple #2, cependant, réussira même si la mesure de baisse n’existe pas, puisque la requête à la baisse n’a pas retourné toutes les pièces d’identité d’étendue. 

Exemple 1 : 

```kusto
.replace extents in table MyOtherTable <|
     { datatable(ExtentId:guid)[ "2cca5844-8f0d-454e-bdad-299e978be5df"] }, { .show table MyTable1 extents }
```

Exemple 2 :

```kusto
.replace extents in table MyOtherTable  <|
     { .show table MyOtherTable extents | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) }, { .show table MyTable1 extents }
```



## <a name="drop-extent-tags"></a>.drop étiquettes d’étendue

**Syntaxe**

`.drop`[`async` `extent` `tags` `from` ] `table` *TableName* `(`'`,`*Tag1*'[ '*Tag2*'`,`... `,`*TagN*']`)`

`.drop`[`async` `extent` `tags`  <| ] *requête*

* `async`(facultatif) précise si la commande est exécutée asynchrone (auquel cas, une pièce d’identité d’opération (Guid) est retournée, et l’état de l’opération peut être surveillé à l’aide de la commande [d’opérations .show).](operations.md#show-operations)
    * Dans le cas où cette option est utilisée, les résultats d’une exécution réussie peuvent être récupérés la commande [de détails d’opération .show).](operations.md#show-operation-details)

La commande s’exécute dans le cadre d’une base de données spécifique, et laisse tomber [l’étiquette d’étendue](extents-overview.md#extent-tagging) fournie de toute étendue dans la base de données et le tableau fournis, qui comprend l’une des balises.  

Il existe deux façons de spécifier les balises à supprimer à partir des étendues :

1. En spécifiant explicitement les balises qui doivent être supprimées de toutes les étendues du tableau spécifié.
2. En fournissant une requête dont les résultats spécifient l’étendue des cartes d’identité dans le tableau et l’étendue de l’avant-point - les balises qui doivent être supprimées.

**Restrictions**
- Toutes les étendues doivent être dans la base de données contextuelle, et doivent appartenir à la même table. 

**Spécifier les étendues avec une requête**

Nécessite [l’autorisation d’administration de table](../management/access-control/role-based-authorization.md) pour toutes les tables de source et de destination impliquées.

```kusto 
.drop extent tags <| ...query... 
```

Les étendues et les balises à déposer sont spécifiées à l’aide d’une requête Kusto qui renvoie un enregistrement avec une colonne appelée "ExtentId" et une colonne appelée "Tags". 

*REMARQUE*: Lors de l’utilisation de la [bibliothèque client Kusto .NET](../api/netfx/about-kusto-data.md), vous pouvez utiliser les méthodes suivantes afin de générer la commande souhaitée :
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**Sortie de retour**

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Un identifiant unique (GUID) pour l’étendue d’origine dont les étiquettes ont été modifiées (et est abandonnée dans le cadre de l’opération) 
RésultatExtentId |string |Un identifiant unique (GUID) pour l’étendue du résultat qui a modifié les balises (et est créé et ajouté dans le cadre de l’opération). Sur l’échec - "Échec".
RésultatExtentTags |string |La collecte des étiquettes avec lesquelles l’étendue du résultat est étiquetée (le cas échéant), ou "null" en cas de panne de l’opération.
Détails |string |Inclut les détails de l’échec, en cas d’échec de l’opération.

**Exemples**

Dépose `drop-by:Partition000` l’étiquette de `MyOtherTable` toute étendue dans le tableau qui est étiqueté avec elle.

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

Baisse les `drop-by:20160810104500` `a random tag`étiquettes , `drop-by:20160810` , et `My Table` / ou de toute étendue dans le tableau qui est marqué avec l’un ou l’autre d’entre eux.

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

Gouttes `drop-by` toutes les étiquettes des étendues dans le tableau `MyTable`.

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

Gouttes toutes les `drop-by:StreamCreationTime_20160915(\d{6})` étiquettes correspondant `MyTable`regex des étendues dans le tableau .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**Exemple de sortie** 

|OriginalExtentId |RésultatExtentId | RésultatExtentTags | Détails
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>.modifier les étiquettes d’étendue

**Syntaxe**

`.alter`[`async` `extent` `tags` `(`] '*Tag1*`,`'[`,`'*Tag2*' ... `,`'*TagN*`)` <| ']*requête*

La commande s’exécute dans le cadre d’une base de données spécifique, et modifie [l’étendue de l’étiquette (s)](extents-overview.md#extent-tagging) de toutes les étendues retournées par la requête spécifiée, à l’ensemble fourni de balises.

Les étendues et les balises à modifier sont spécifiées à l’aide d’une requête Kusto qui renvoie un enregistrement avec une colonne appelée "ExtentId".

* `async`(facultatif) précise si la commande est exécutée asynchrone (auquel cas, une pièce d’identité d’opération (Guid) est retournée, et l’état de l’opération peut être surveillé à l’aide de la commande [d’opérations .show).](operations.md#show-operations)
    * Dans le cas où cette option est utilisée, les résultats d’une exécution réussie peuvent être récupérés la commande [de détails d’opération .show).](operations.md#show-operation-details)

Nécessite [la permission d’administration de la table](../management/access-control/role-based-authorization.md) pour toutes les tables impliquées.

**Restrictions**
- Toutes les étendues doivent être dans la base de données contextuelle, et doivent appartenir à la même table. 

**Sortie de retour**

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Un identifiant unique (GUID) pour l’étendue d’origine dont les étiquettes ont été modifiées (et est abandonnée dans le cadre de l’opération) 
RésultatExtentId |string |Un identifiant unique (GUID) pour l’étendue du résultat qui a modifié les balises (et est créé et ajouté dans le cadre de l’opération). Sur l’échec - "Échec".
RésultatExtentTags |string |La collecte des étiquettes avec lesquelles l’étendue du résultat est étiquetée, ou "null" en cas d’échec de l’opération.
Détails |string |Inclut les détails de l’échec, en cas d’échec de l’opération.

**Exemples**

Modifie les balises de `MyTable` toutes `MyTag`les étendues dans la table à .

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

Modifie les balises de `MyTable`toutes les `drop-by:MyTag` étendues dans la table , étiqueté avec `drop-by:MyNewTag` et `MyOtherNewTag`.

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**Exemple de sortie** 

|OriginalExtentId |RésultatExtentId | RésultatExtentTags | Détails
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | drop-by:MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | drop-by:MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | drop-by:MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | drop-by:MyNewTag MyOtherNewTag| 


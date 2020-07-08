---
title: . afficher les étendues-Azure Explorateur de données
description: Cet article décrit la commande Afficher les étendues dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: c0e2ffa76becc747b03b907dfe8a356e4c1a49a3
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060630"
---
# <a name="show-extents"></a>. afficher les étendues

Affiche les étendues d’une base de données ou d’une table spécifiée.

> [!NOTE]
> Les partitions de données sont des **Extensions** appelées dans Kusto, et toutes les commandes utilisent « extent » ou « extents » comme synonyme.
> Pour plus d’informations sur les extensions, consultez [extents (Data partitions) Overview](extents-overview.md).

## <a name="cluster-level"></a>Niveau de cluster

`.show` `cluster` `extents` [`hot`]

Affiche des informations sur les étendues (partitions de données) qui sont présentes dans le cluster.
Si `hot` est spécifié, affiche uniquement les étendues supposées se trouver dans le cache à chaud.

## <a name="database-level"></a>Au niveau de la base de données

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,` *ExtentIdN* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *: Étiquette1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *tag2*...]]

Affiche des informations sur les étendues (partitions de données) qui sont présentes dans la base de données spécifiée.
Si `hot` est spécifié, affiche uniquement les extensions qui devraient se trouver dans le cache à chaud.

## <a name="table-level"></a>Niveau de la table

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,` *ExtentIdN* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *: Étiquette1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,` *TableNameN* `)` `extents`[ `(` *ExtentId1* `,` ... `,` *ExtentIdN* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *: Étiquette1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *tag2*...]]

Affiche des informations sur les étendues (partitions de données) qui sont présentes dans les tables spécifiées. La base de données est extraite du contexte de la commande.
Si `hot` est spécifié, affiche uniquement les extensions qui sont supposées se trouver dans le cache à chaud.

## <a name="notes"></a>Notes

* L’utilisation des commandes de base de données ou de niveau table est beaucoup plus rapide que le filtrage (ajout `| where DatabaseName == '...' and TableName == '...'` ) des résultats d’une commande au niveau du cluster.
* Si la liste facultative d’ID d’étendue est fournie, le jeu de données retourné est limité à ces étendues uniquement.
    * Cette méthode est beaucoup plus rapide que le filtrage (ajout `| where ExtentId in(...)` à) des résultats de commandes « Bare ».
* Si `tags` des filtres sont spécifiés :
    * La liste retournée est limitée aux étendues dont la collection Tags obéit à *tous* les filtres de balises fournis.
    * Cette méthode est beaucoup plus rapide que le filtrage (ajout `| where Tags has '...' and Tags contains '...'` à) des résultats de commandes « Bare ».
    * `has`les filtres sont des filtres d’égalité. Les extensions qui ne sont pas marquées avec l’une des balises spécifiées seront exclues.
    * `!has`les filtres sont des filtres négatifs d’égalité. Les extensions marquées avec l’une des balises spécifiées seront exclues.
    * `contains`les filtres sont des filtres de sous-chaînes non sensibles à la casse. Les extensions qui n’ont pas les chaînes spécifiées en tant que sous-chaîne de l’une de leurs balises sont exclues.
    * `!contains`les filtres sont des filtres négatifs de sous-chaîne non sensibles à la casse. Les extensions qui ont les chaînes spécifiées en tant que sous-chaîne de l’une de leurs balises sont exclues.
  
## <a name="output-parameters"></a>Paramètres de sortie

|Paramètre de sortie |Type |Description |
|---|---|---|
|ExtentId |Guid |ID de l’étendue 
|nom_base_de_données |String |Base de données à laquelle appartient l’extension
|TableName |String |Table à laquelle les étendues appartiennent
|MaxCreatedOn |DateTime |Date et heure de création de l’étendue. Pour une étendue fusionnée, le nombre maximal d’heures de création entre des étendues sources
|OriginalSize |Double |Taille d’origine, en octets, des données d’étendue
|Extenter |Double |Taille de l’étendue en mémoire (compressée + index)
|CompressedSize |Double |Taille compressée des données d’étendue en mémoire
|IndexSize |Double |Taille de l’index des données d’étendue
|Blocs |Long |Nombre de blocs de données dans l’étendue
|Segments |Long |Nombre de segments de données dans l’étendue
|AssignedDataNodes |String | Déconseillé (chaîne vide)
|LoadedDataNodes |String |Déconseillé (chaîne vide)
|ExtentContainerId |String | ID du conteneur d’étendues dans lequel se trouve l’étendue
|RowCount |Long |Nombre de lignes dans l’étendue
|MinCreatedOn |DateTime |Date et heure de création de l’étendue. Pour une étendue fusionnée, la valeur minimale des heures de création entre les étendues sources
|Balises|String|Balises, le cas échéant, définies pour l’étendue
 
## <a name="examples"></a>Exemples

### <a name="tagged-extent"></a>Étendue avec balises

`E`L’étendue dans la table `T` est marquée avec des balises `aaa` , `BBB` et `ccc` .

* Cette requête renverra `E` :
    
   ```kusto
    .show table T extents where tags has 'aaa' and tags contains 'bb'
   ```
   
* Cette requête n’est pas renvoyée `E` , car elle n’est pas marquée avec `aa` :
    
   ```kusto
    .show table T extents where tags has 'aa' and tags contains 'bb'
   ```
    
* Cette requête renverra `E` :
    
   ```kusto
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
   ```

### <a name="show-volume-of-extents-created"></a>Afficher le volume des étendues créées

Afficher le volume des étendues créées par heure dans une base de données spécifique

```kusto 
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  
```

### <a name="show-volume-of-data-arriving-by-table-per-hour"></a>Afficher le volume de données arrivant par table/heure

```kusto 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart
```

### <a name="show-data-size-distribution-by-table"></a>Afficher la distribution de la taille des données par table

```kusto 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName
```

### <a name="show-all-extents-in-the-database-named-gamesdb"></a>Afficher toutes les étendues dans la base de données nommée « GamesDB »

```kusto 
.show database GamesDB extents
```

### <a name="show-all-extents-in-the-table-named-games"></a>Afficher toutes les étendues dans la table nommée « Games »

```kusto 
.show table Games extents
```

### <a name="show-all-extents-in-specific-tables"></a>Afficher toutes les étendues dans des tables spécifiques

Afficher toutes les étendues dans les tables nommées « TaggingGames1 » et « TaggingGames2 », marquées avec «  : étiquette1 » et « tag2 »

```kusto 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
```

---
title: Ingestion de requêtes Kusto (set, append, replace) - Azure Data Explorer
description: Cet article décrit l’ingestion à partir d’une requête (.set, .append, .set-or-append, .set-or-replace) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
ms.openlocfilehash: 18959e2387a1a0faf92261dc3c35eca0db44c158
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512891"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>Ingestion à partir d’une requête (.set, .append, .set-or-append, .set-or-replace)

Ces commandes exécutent une requête ou une commande de contrôle et ingèrent les résultats de la requête dans une table. La différence entre ces commandes est la manière dont elles traitent les données et tables existantes ou inexistantes.

|Commande          |Si la table existe                     |Si la table n’existe pas                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |La commande échoue                  |La table est créée et les données sont ingérées|
|`.append`        |Les données sont ajoutées à la table      |La commande échoue                        |
|`.set-or-append` |Les données sont ajoutées à la table      |La table est créée et les données sont ingérées|
|`.set-or-replace`|Les données remplacent les données de la table|La table est créée et les données sont ingérées|

**Syntaxe**

`.set` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...`])`] `<|` *QueryOrCommand*

`.set-or-append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.set-or-replace` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

**Arguments**

* `async` : si cette argument est spécifié, la commande retourne immédiatement une réponse et continue l’ingestion en arrière-plan. Les résultats de la commande incluent une valeur `OperationId` qui peut ensuite être utilisée avec la commande `.show operations` pour récupérer l’état d’achèvement de l’ingestion et les résultats.
* *TableName* : nom de la table dans laquelle les données doivent être ingérées.
  Le nom de la table est toujours associé à la base de données en contexte.
* *PropertyName*, *PropertyValue* : un nombre quelconque de propriétés d’ingestion qui affectent le processus d’ingestion.

 Propriétés d’ingestion prises en charge.

|Propriété        |Description|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | Valeur DateHeure (sous forme de chaîne ISO8601) à utiliser comme heure de création des étendues de données ingérées. Si la propriété n’est pas spécifiée, la valeur actuelle (`now()`) est utilisée.|
|`extend_schema`  | Valeur booléenne qui, si elle est spécifiée, indique à la commande d’étendre le schéma de la table. La valeur par défaut est « false ». Cette option s’applique uniquement aux commandes `.append`, `.set-or-append` et `set-or-replace`. Les seules extensions de schéma autorisées sont celles pour lesquelles des colonnes supplémentaires sont ajoutées à la fin de la table.|
|`recreate_schema`  | Une valeur booléenne qui, si elle est spécifiée, indique si la commande peut recréer ou non le schéma de la table. La valeur par défaut est « false ». Cette option s’applique uniquement à la commande *set-or-replace*. Cette option est prioritaire sur la propriété extend_schema si les deux sont définies|
|`folder`         | Dossier à assigner à la table. Si la table existe déjà, cette propriété remplace le dossier de la table.|
|`ingestIfNotExists`   | Valeur de chaîne qui, si elle est spécifiée, empêche l’ingestion de s’effectuer correctement si la table a déjà des données balisées avec une balise `ingest-by:` de la même valeur.|
|`policy_ingestiontime`   | Valeur booléenne. Si celle-ci est spécifiée, indique d’activer ou non la [stratégie de durée d’ingestion](../../management/ingestiontime-policy.md) sur une table créée par cette commande. La valeur par défaut est « true ».|
|`tags`   | Chaîne JSON qui indique les validations à exécuter pendant l’ingestion.|
|`docstring`   | Chaîne qui documente la table|

 Propriété qui contrôle le comportement de la commande.

|Propriété        |Type    |Description|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |Indique que la commande ingère à partir de tous les nœuds exécutant la requête en parallèle. La valeur par défaut est « false ».  Consultez la section Notes ci-dessous.|

* *QueryOrCommand* : texte d’une requête ou d’une commande de contrôle dont les résultats sont utilisés comme données à ingérer.

> [!NOTE]
> Seules les commandes de contrôle `.show` sont prises en charge.

**Remarques**

* La commande `.set-or-replace` remplace les données de la table si celle-ci existe. Elle supprime les partitions de données existantes ou crée la table cible, si elle n’existe pas déjà.
  Le schéma de la table est préservé, sauf si l’une des propriétés d’ingestion `extend_schema` ou `recreate_schema` est définie sur « true ». Si le schéma est modifié, il se produit avant l’ingestion réelle des données dans sa propre transaction. L’échec de l’ingestion des données ne signifie pas que le schéma n’a pas été modifié.
* Les commandes `.set-or-append` et `.append` conservent le schéma, sauf si la propriété d’ingestion `extend_schema` est définie sur « true ». Si le schéma est modifié, il se produit avant l’ingestion réelle des données dans sa propre transaction. L’échec de l’ingestion des données ne signifie pas que le schéma n’a pas été modifié.
* Nous vous recommandons de limiter les données pour l’ingestion à moins de 1 Go par opération d’ingestion. Plusieurs commandes d’ingestion peuvent être utilisées, si nécessaire.
* L’ingestion de données est une opération gourmande en ressources qui peut affecter les activités simultanées sur le cluster, notamment les requêtes en cours d’exécution. Évitez d’exécuter un trop grand nombre de commandes de ce type en même temps.
* En cas de correspondance entre le schéma du jeu de résultats et celui de la table cible, la comparaison est basée sur les types de colonnes. Il n’y a aucune correspondance avec les noms de colonnes. Assurez-vous que les colonnes du schéma des résultats de la requête sont dans le même ordre que la table. Sinon, les données sont ingérées dans la mauvaise colonne.
* La définition de l’indicateur `distributed` sur « true » est utile lorsque la quantité de données produites par la requête est importante, dépasse 1 Go et que la requête ne nécessite pas de sérialisation, de sorte que plusieurs nœuds peuvent produire une sortie en parallèle.
  Lorsque les résultats de la requête sont petits, n’utilisez pas cet indicateur, car il peut inutilement générer un grand nombre de petites partitions de données.

**Exemples** 

Créez une table nommée :::no-loc text="RecentErrors"::: dans la base de données qui a le même schéma que :::no-loc text="LogsTable"::: et qui contient tous les enregistrements d’erreur de la dernière heure.

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

Créez une table appelée « OldExtents » dans la base de données qui comporte une seule colonne, « ExtentId », et qui contient les ID d’étendue de toutes les étendues de la base de données qui ont été créées plus de 30 jours plus tôt. La base de données a une table existante nommée « MyExtents ».

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

Ajoutez des données à une table existante appelée « OldExtents » dans la base de données active qui contient une seule colonne, « ExtentId », et qui contient les ID d’étendue de toutes les étendues de la base de données qui ont été créées plus de 30 jours plus tôt.
Marquez la nouvelle étendue avec des balises `tagA` et `tagB`, en fonction d’une table existante nommée « MyExtents ».

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

Ajoutez des données à la table « OldExtents » de la base de données active ou créez la table si elle n’existe pas déjà. Balisez la nouvelle étendue avec `ingest-by:myTag`. Procédez ainsi uniquement si la table ne contient pas déjà une extension marquée avec `ingest-by:myTag`, en fonction d’une table existante nommée « MyExtents ».

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

Remplacez les données de la table « OldExtents » de la base de données active ou créez la table si elle n’existe pas déjà. Balisez la nouvelle étendue avec `ingest-by:myTag`.

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

Ajoutez des données à la table « OldExtents » dans la base de données active, tout en définissant l’heure de création de l’étendue créée sur une valeur DateTime spécifique dans le passé.

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**Sortie de retour**
 
Retourne des informations sur les étendues créées en raison de la commande `.set` ou `.append`.

**Exemple de sortie**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |

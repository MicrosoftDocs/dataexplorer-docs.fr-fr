---
title: Ingestion de requêtes Kusto (Set, Append, Replace)-Azure Explorateur de données
description: Cet article décrit la réception de la requête (. Set,. Append,. set-or-Append,. set-or-Replace) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 5248b9d986845ff7f35085cef0100cf3ab4b90da
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264468"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>Réception à partir de la requête (. Set,. Append,. set-or-Append,. set-or-Replace)

Ces commandes exécutent une requête ou une commande de contrôle et ingèrent les résultats de la requête dans une table. La différence entre ces commandes est la manière dont elles traitent les données et les tables existantes ou inexistantes.

|Commande          |Si la table existe                     |Si la table n’existe pas                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |Échec de la commande                  |La table est créée et les données sont ingérées.|
|`.append`        |Les données sont ajoutées à la table      |Échec de la commande                        |
|`.set-or-append` |Les données sont ajoutées à la table      |La table est créée et les données sont ingérées.|
|`.set-or-replace`|Les données remplacent les données de la table|La table est créée et les données sont ingérées.|

**Syntaxe**

`.set`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *QueryOrCommand*

`.append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ... `])` ] `<|` *QueryOrCommand*

`.set-or-append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *QueryOrCommand*

`.set-or-replace`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *QueryOrCommand*

**Arguments**

* `async`: S’il est spécifié, la commande retourne immédiatement et continue la réception en arrière-plan. Les résultats de la commande incluent une `OperationId` valeur qui peut ensuite être utilisée avec la `.show operations` commande pour récupérer l’état d’achèvement de l’ingestion et les résultats.
* *TableName*: nom de la table à laquelle les données doivent être ingérées.
  Le nom de la table est toujours associé à la base de données en contexte.
* *PropertyName*, *PropertyValue*: nombre quelconque de propriétés d’ingestion qui affectent le processus d’ingestion.

 Propriétés d’ingestion prises en charge.

|Propriété        |Description|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | Valeur DateTime, mise en forme en tant que chaîne ISO8601, à utiliser au moment de la création des étendues de données ingérées. S’il n’est pas spécifié, la valeur actuelle ( `now()` ) est utilisée|
|`extend_schema`  | Valeur booléenne qui, si elle est spécifiée, indique à la commande d’étendre le schéma de la table. La valeur par défaut est « false ». Cette option s’applique uniquement `.append` aux `.set-or-append` commandes, et `set-or-replace` . Les seules extensions de schéma autorisées ont des colonnes supplémentaires ajoutées à la table à la fin|
|`recreate_schema`  | Valeur booléenne qui. S’il est spécifié, indique si la commande peut recréer le schéma de la table. La valeur par défaut est « false ». Cette option s’applique uniquement à la commande *set-ou-Replace* . Cette option est prioritaire sur la propriété extend_schema si les deux sont définies|
|`folder`         | Dossier à assigner à la table. Si la table existe déjà, cette propriété remplacera le dossier de la table.|
|`ingestIfNotExists`   | Valeur de chaîne qui. Si ce paramètre est spécifié, empêche la réception de la tentative si la table a déjà des données marquées avec une `ingest-by:` balise avec la même valeur|
|`policy_ingestiontime`   | Valeur booléenne. Si ce paramètre est spécifié, indique si la [stratégie de temps](../../management/ingestiontime-policy.md) d’ingestion doit être activée sur une table créée par cette commande. La valeur par défaut est « true ».|
|`tags`   | Chaîne JSON qui indique les validations à exécuter pendant l’ingestion|
|`docstring`   | Chaîne qui documente la table|

 Propriété qui contrôle le comportement de la commande.

|Propriété        |Type    |Description|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |Indique que la commande est ingérée à partir de tous les nœuds exécutant la requête en parallèle. La valeur par défaut est « false ».  Consultez les notes ci-dessous.|

* *QueryOrCommand*: texte d’une requête ou d’une commande de contrôle dont les résultats seront utilisés comme données à ingérer.

> [!NOTE]
> Seules `.show` les commandes de contrôle sont prises en charge.

**Remarques**

* `.set-or-replace`remplace les données de la table si elle existe. Il supprime le partitions de données existant ou crée la table cible, si elle n’existe pas déjà.
  Le schéma de la table est préservé, sauf si l’une des propriétés d’ingestion `extend_schema` ou `recreate_schema` est définie sur « true ». Si le schéma est modifié, il se produit avant l’ingestion réelle des données dans sa propre transaction. L’échec de la réception des données ne signifie pas que le schéma n’a pas été modifié.
* `.set-or-append``.append`les commandes et conservent le schéma, sauf si la propriété ingestion `extend_schema` est définie sur « true ». Si le schéma est modifié, il se produit avant l’ingestion réelle des données dans sa propre transaction. L’échec de la réception des données ne signifie pas que le schéma n’a pas été modifié.
* Nous vous recommandons de limiter les données pour l’ingestion à moins de 1 Go par opération d’ingestion. Plusieurs commandes d’ingestion peuvent être utilisées, si nécessaire.
* L’ingestion de données est une opération gourmande en ressources qui peut affecter les activités simultanées sur le cluster, notamment les requêtes en cours d’exécution. Évitez d’exécuter un trop grand nombre de commandes de ce type en même temps.
* En cas de correspondance entre le schéma du jeu de résultats et celui de la table cible, la comparaison est basée sur les types de colonnes. Il n’y a aucune correspondance avec les noms de colonnes. Assurez-vous que les colonnes du schéma des résultats de la requête sont dans le même ordre que la table. Sinon, les données sont ingérées dans la mauvaise colonne.
* L’affectation `distributed` de la valeur « true » à l’indicateur est utile lorsque la quantité de données produites par la requête est importante, dépasse 1 Go et la requête ne nécessite pas de sérialisation, de sorte que plusieurs nœuds peuvent produire une sortie en parallèle.
  Lorsque les résultats de la requête sont petits, n’utilisez pas cet indicateur, car il peut inutilement générer un grand nombre de petits partitions de données.

**Exemples** 

Créez une nouvelle table appelée « RecentErrors » dans la base de données qui a le même schéma que « LogsTable » et qui contient tous les enregistrements d’erreurs de la dernière heure.

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

Créez une nouvelle table appelée « OldExtents » dans la base de données qui comporte une seule colonne, « ExtentId », et qui contient les ID d’étendue de toutes les étendues de la base de données qui ont été créées plus de 30 jours plus tôt. La base de données a une table existante nommée « MyExtents ».

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

Ajoutez des données à une table existante appelée « OldExtents » dans la base de données active qui contient une seule colonne, « ExtentId », et contient les ID d’étendue de toutes les étendues de la base de données qui ont été créées plus tôt dans plus de 30 jours.
Marquez la nouvelle étendue avec des balises `tagA` et, sur la `tagB` base d’une table existante nommée « MyExtents ».

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

Ajoutez des données à la table « OldExtents » de la base de données active ou créez la table si elle n’existe pas déjà. Baliser la nouvelle étendue avec `ingest-by:myTag` . Procédez ainsi uniquement si la table ne contient pas déjà une extension marquée avec `ingest-by:myTag` , basée sur une table existante nommée « MyExtents ».

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

Remplacez les données de la table « OldExtents » de la base de données active ou créez la table si elle n’existe pas déjà. Baliser la nouvelle étendue avec `ingest-by:myTag` .

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

Ajoute des données à la table « OldExtents » dans la base de données active, tout en définissant l’heure de création de l’étendue créée sur une valeur DateTime spécifique dans le passé.

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**Sortie de retour**
 
Retourne des informations sur les étendues créées en raison de la `.set` `.append` commande ou.

**Exemple de sortie**

|ExtentId |OriginalSize |Extenter |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376D-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |

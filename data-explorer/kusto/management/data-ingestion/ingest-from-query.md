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
ms.openlocfilehash: bfa44859987d8f3c4f11221fd8370290f08f9a67
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382044"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>Réception à partir de la requête (. Set,. Append,. set-or-Append,. set-or-Replace)

Ces commandes exécutent une requête ou une commande de contrôle, et informent les résultats de la requête dans une table. La différence entre ces commandes est la manière dont elles traitent les données et les tables existantes ou inexistantes :

|Commande          |Si la table existe                     |Si la table n’existe pas                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |La commande échoue.                  |La table est créée et les données sont ingérées.|
|`.append`        |Les données sont ajoutées à la table.      |La commande échoue.                        |
|`.set-or-append` |Les données sont ajoutées à la table.      |La table est créée et les données sont ingérées.|
|`.set-or-replace`|Les données remplacent les données de la table.|La table est créée et les données sont ingérées.|

**Syntaxe**

`.set`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *QueryOrCommand*

`.append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ... `])` ] `<|` *QueryOrCommand*

`.set-or-append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *QueryOrCommand*

`.set-or-replace`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *QueryOrCommand*

**Arguments**

* `async`: S’il est spécifié, la commande est immédiatement retournée et continue l’ingestion en arrière-plan. Les résultats de la commande incluent une `OperationId` valeur qui peut ensuite être utilisée avec la `.show operation` commande pour récupérer l’état d’achèvement de l’ingestion et les résultats.
* *TableName*: nom de la table à laquelle les données doivent être ingérées.
  Le nom de la table est toujours relatif à la base de données en contexte.
* *PropertyName*, *PropertyValue*: nombre quelconque de propriétés d’ingestion qui affectent le processus d’ingestion.

 Propriétés d’ingestion prises en charge :

|Propriété        |Description|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | Valeur DateTime (mise en forme en tant que chaîne ISO8601) à utiliser au moment de la création des étendues de données ingérées. S’il n’est pas spécifié, la valeur actuelle (Now ()) est utilisée.|
|`extend_schema`  | Valeur booléenne qui, si elle est spécifiée, indique à la commande d’étendre le schéma de la table (false par défaut). Cette option s’applique uniquement aux commandes. Append. Set-ou-Append et set-or-Replace. Les seules extensions de schéma autorisées sont celles pour lesquelles des colonnes supplémentaires sont ajoutées à la fin de la table.|
|`recreate_schema`  | Valeur booléenne qui, si elle est spécifiée, indique si la commande peut recréer le schéma de la table (false par défaut). Cette option s’applique uniquement à la commande Set-ou-Replace. Cette option est prioritaire sur la propriété extend_schema si les deux sont définies.|
|`folder`         | Dossier à assigner à la table. Si la table existe déjà, cette propriété remplace le dossier de la table.|
|`ingestIfNotExists`   | Valeur de chaîne qui, si elle est spécifiée, empêche l’ingestion de s’effectuer correctement si la table a déjà des données marquées avec une balise de réception par : avec la même valeur.|
|`policy_ingestiontime`   | Valeur booléenne qui, si elle est spécifiée, indique d’activer ou non la [stratégie de durée d’ingestion](../../management/ingestiontime-policy.md) sur une table créée par cette commande. La valeur par défaut est true.|
|`tags`   | Chaîne JSON qui indique les validations à exécuter pendant l’ingestion.|
|`docstring`   | Chaîne qui documente la table.|

  En outre, il existe une propriété qui contrôle le comportement de la commande elle-même :

|Propriété        |Type    |Description|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |Indique que la commande est ingérée à partir de tous les nœuds exécutant la requête en parallèle. (La valeur par défaut est `false` .)  Consultez les remarques ci-dessous.|

* *QueryOrCommand*: texte d’une requête ou d’une commande de contrôle dont les résultats seront utilisés comme données à ingérer.

> [!NOTE]
> Seules `.show` les commandes de contrôle sont prises en charge.

**Remarques**

* `.set-or-replace`remplace les données de la table si elles existent (supprime le partitions de données existant) ou crée la table cible si elle n’existe pas déjà.
  Le schéma de table est préservé, sauf si une propriété d’ingestion `extend_schema` `recreate_schema` est définie sur `true` . Si le schéma est modifié, cela se produit avant l’ingestion réelle des données dans sa propre transaction. par conséquent, l’échec de l’ingestion des données ne signifie pas que le schéma n’a pas été modifié.
* `.set-or-append``.append`les commandes et conservent le schéma à moins que la propriété ingestion `extend_schema` ne soit définie sur `true` . Si le schéma est modifié, cela se produit avant l’ingestion réelle des données dans sa propre transaction. par conséquent, l’échec de l’ingestion des données ne signifie pas que le schéma n’a pas été modifié.
* Il est **fortement recommandé** que les données d’ingestion soient limitées à moins de 1 Go par opération d’ingestion. Plusieurs commandes d’ingestion peuvent être utilisées, si nécessaire.
* L’ingestion de données est une opération gourmande en ressources qui peut affecter les activités simultanées sur le cluster, notamment les requêtes en cours d’exécution. Évitez d’exécuter des « trop nombreuses » commandes simultanément.
* En cas de correspondance entre le schéma du jeu de résultats et celui de la table cible, la comparaison est basée sur les types de colonnes. Il n’y a pas de correspondance entre les noms de colonnes. Assurez-vous que les colonnes du schéma de résultat de la requête sont dans le même ordre que la table, sinon les données seront ingérées dans la mauvaise colonne.
* L’affectation `distributed` de la valeur à l’indicateur `true` est utile lorsque la quantité de données produites par la requête est importante (dépasse 1 Go de données) **et** que la requête ne nécessite pas de sérialisation (afin que plusieurs nœuds puissent produire une sortie en parallèle).
  Lorsque les résultats de la requête sont petits, il n’est pas recommandé d’utiliser cet indicateur, car il peut générer un grand nombre de petits partitions de données inutilement.

**Exemples** 

Créez une nouvelle table appelée « RecentErrors » dans la base de données active qui a le même schéma que « LogsTable » et qui contient tous les enregistrements d’erreurs de la dernière heure :

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

Créez une nouvelle table appelée « OldExtents » dans la base de données active qui contient une seule colonne (« ExtentId ») et contient les ID d’étendue de toutes les étendues de la base de données qui ont été créées il y a plus de 30 jours, sur la base d’une table existante nommée « MyExtents » :

```kusto
.set async OldExtents <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Ajoutez des données à une table existante appelée « OldExtents » dans la base de données active qui contient une seule colonne (« ExtentId ») et contient les ID d’étendue de toutes les étendues de la base de données qui ont été créées il y a plus de 30 jours, tout en marquant la nouvelle étendue avec des balises et, sur la base d' `tagA` `tagB` une table existante nommée « MyExtents » :

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```
 
Ajoutez des données à la table « OldExtents » dans la base de données active (ou créez la table si elle n’existe pas déjà), tout en marquant la nouvelle extension avec `ingest-by:myTag` . Procédez ainsi uniquement si la table ne contient pas déjà une extension marquée avec `ingest-by:myTag` , basée sur une table existante nommée « MyExtents » :

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Remplacez les données de la table « OldExtents » de la base de données active (ou créez la table si elle n’existe pas déjà), tout en marquant la nouvelle extension avec `ingest-by:myTag` .

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Ajoute des données à la table « OldExtents » dans la base de données active, tout en définissant l’heure de création de l’étendue créée sur une valeur DateTime spécifique dans le passé :

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**Sortie de retour**
 
Retourne des informations sur les étendues créées à la suite de `.set` la `.append` commande ou.

**Exemple de sortie**

|ExtentId |OriginalSize |Extenter |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376D-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |
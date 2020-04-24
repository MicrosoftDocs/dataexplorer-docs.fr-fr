---
title: Ingest from query (.set, .append, .set-or-append, .set-or-replace) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Ingest à partir de requêtes (.set, .append, .set-or-append, .set-or-replace) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: fb0bbb06bb8d28dd3951a7faedf55b0d84155301
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521452"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>Ingest de requête (.set, .append, .set-or-append, .set-or-replace)

Ces commandes exécutent une requête ou une commande de contrôle, et ingèrent les résultats de la requête dans une table. La différence entre ces commandes est la façon dont elles traitent les tableaux et les données existants ou inexistants :

|Commande          |Si le tableau existe                     |Si la table n’existe pas                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |La commande échoue.                  |Le tableau est créé et les données sont ingérées.|
|`.append`        |Les données sont jointes à la table.      |La commande échoue.                        |
|`.set-or-append` |Les données sont jointes à la table.      |Le tableau est créé et les données sont ingérées.|
|`.set-or-replace`|Les données remplacent les données dans le tableau.|Le tableau est créé et les données sont ingérées.|

**Syntaxe**

`.set`[`async`] *TableName* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *DemandeOrCommand*

`.append`[`async`] *TableName* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ... `])`] `<|` *DemandeOrCommand*

`.set-or-append`[`async`] *TableName* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *DemandeOrCommand*

`.set-or-replace`[`async`] *TableName* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *DemandeOrCommand*

**Arguments**

* `async`: Si spécifié, la commande reviendra immédiatement et continuera l’ingestion en arrière-plan. Les résultats de la `OperationId` commande incluront une valeur `.show operation` qui peut ensuite être utilisée avec la commande pour récupérer l’état et les résultats d’achèvement de l’ingestion.
* *TableName*: Le nom de la table pour ingérer les données.
  Le nom de table est toujours relatif à la base de données dans son contexte.
* *PropertyName*, *PropertyValue*: Tout nombre de propriétés d’ingestion qui affectent le processus d’ingestion.

 Propriétés d’ingestion soutenues :

|Propriété        |Description|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | La valeur de date (formatée comme chaîne ISO8601) à utiliser au moment de la création des étendues de données ingérées. Si elle n’est pas spécifiée, la valeur actuelle (maintenant)) sera utilisée.|
|`extend_schema`  | Une valeur Boolean qui, si elle est spécifiée, ordonne à la commande d’étendre le schéma de la table (par défaut à faux). Cette option ne s’applique qu’aux commandes .append.set-or-append et set-or-replace. Les seules extensions de schéma autorisées sont celles pour lesquelles des colonnes supplémentaires sont ajoutées à la fin de la table.|
|`recreate_schema`  | Une valeur Boolean qui, si elle est spécifiée, décrit si la commande peut recréer le schéma de la table (par défaut à faux). Cette option ne s’applique qu’à la commande de définis ou de remplacement. Cette option a préséance sur la propriété extend_schema si les deux sont définies.|
|`folder`         | Le dossier à attribuer à la table. Si la table existe déjà, cette propriété remplacera le dossier de la table.|
|`ingestIfNotExists`   | Une valeur de chaîne qui, si elle est spécifiée, empêche l’ingestion de réussir si la table a déjà des données étiquetées avec un ingest-by: tag avec la même valeur.|
|`policy_ingestiontime`   | Valeur booléenne qui, si elle est spécifiée, indique d’activer ou non la [stratégie de durée d’ingestion](../../management/ingestiontime-policy.md) sur une table créée par cette commande. La valeur par défaut est true.|
|`tags`   | Chaîne JSON qui indique les validations à exécuter pendant l’ingestion.|
|`docstring`   | Une chaîne documentant la table.|

  En outre, il ya une propriété qui contrôle le comportement de la commande elle-même:

|Propriété        |Type    |Description|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |Indique que la commande ingérer de tous les nœuds exécutant la requête en parallèle. (Par défaut `false`à .)  Voir les remarques ci-dessous.|

* *QuestionryOrCommand*: Le texte d’une requête ou d’une commande de contrôle dont les résultats seront utilisés comme données pour ingérer.

> [!NOTE]
> Seules `.show` les commandes de contrôle sont prises en charge.

**Remarques**

* `.set-or-replace`remplace les données du tableau s’il existe (gouttes les éclats de données existants), ou crée la table cible si elle n’existe pas déjà.
  Le schéma de table sera conservé `extend_schema` `recreate_schema` à moins que l’un des biens ou l’ingestion est réglé à `true`. Si le schéma est modifié, cela se produit avant l’ingestion réelle de données dans sa propre transaction, de sorte qu’un défaut d’ingérer les données ne signifie pas que le schéma n’a pas été modifié.
* `.set-or-append`et `.append` les commandes préserveront `extend_schema` le schéma à moins `true`que la propriété d’ingestion ne soit fixée à . Si le schéma est modifié, cela se produit avant l’ingestion réelle de données dans sa propre transaction, de sorte qu’un défaut d’ingérer les données ne signifie pas que le schéma n’a pas été modifié.
* Il est **fortement recommandé** que les données relatives à l’ingestion soient limitées à moins de 1 Go par opération d’ingestion. Plusieurs commandes d’ingestion peuvent être utilisées, si nécessaire.
* L’ingestion de données est une opération à forte intensité de ressources qui pourrait affecter les activités simultanées sur le cluster, y compris les requêtes en cours d’exécution. Évitez d’exécuter «trop» de telles commandes ensemble en même temps.
* Lorsque vous comparez le schéma de l’ensemble de résultat à celui de la table cible, la comparaison est basée sur les types de colonnes. Il n’y a pas de correspondance des noms de colonne, alors assurez-vous que les colonnes de schéma de résultat de requête sont dans le même ordre que le tableau, sinon les données seront ingérées dans la mauvaise colonne.
* La `distributed` configuration `true` du drapeau est utile lorsque la quantité de données produites par la requête est importante (dépasse 1 Go de données) **et** que la requête ne nécessite pas de sérialisation (de sorte que plusieurs nœuds peuvent produire la sortie en parallèle).
  Lorsque les résultats de la requête sont faibles, il n’est pas recommandé d’utiliser ce drapeau, car il pourrait générer beaucoup de petits éclats de données inutilement.

**Exemples** 

Créez un nouveau tableau appelé "RecentErrors" dans la base de données actuelle qui a le même schéma que "LogsTable" et détient tous les enregistrements d’erreurs de la dernière heure:

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

Créer un nouveau tableau appelé "OldExtents" dans la base de données actuelle qui a une seule colonne ("ExtentId") et détient l’étendue des cartes d’accord de toutes les étendues dans la base de données qui ont été créés il ya plus de 30 jours, basé sur une table existante nommée "MyExtents":

```kusto
.set async OldExtents <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Appendez les données d’un tableau existant appelé « OldExtents » dans la base de données actuelle qui a une seule colonne (« ExtentId ») et contient l’étendue des cartes d’identité de toutes les étendues dans la base de données qui ont été créées il y a plus de 30 jours, tout en taguant la nouvelle mesure avec des balises `tagA` et, `tagB`sur la base d’une table existante nommée « MyExtents » :

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```
 
Appendez les données au tableau "OldExtents" dans la base de données actuelle (ou créez `ingest-by:myTag`la table si elle n’existe pas déjà), tout en taguant la nouvelle étendue avec . Ne le faites que si la table ne `ingest-by:myTag`contient pas déjà une étendue étiquetée avec , basé sur une table existante nommée "MyExtents":

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Remplacez les données dans le tableau "OldExtents" dans la base de données actuelle (ou créez `ingest-by:myTag`la table si elle n’existe pas déjà), tout en taguant la nouvelle étendue par .

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

Applez les données au tableau « OldExtents » dans la base de données actuelle, tout en définissant le temps de création de l’étendue de la création à une date précise dans le passé :

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**Sortie de retour**
 
Renvoie des informations sur les étendues `.append` créées à la suite de la commande ou de la `.set` commande.

**Exemple de sortie**

|ExtentId (extentId) |OriginalSize |ExtentSize (en) |CompressésSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |
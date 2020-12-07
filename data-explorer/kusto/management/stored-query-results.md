---
title: Résultats de la requête stockée (version préliminaire)-Azure Explorateur de données
description: Cet article explique comment créer et utiliser des résultats de requête stockée dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mispecto
ms.service: data-explorer
ms.topic: reference
ms.date: 12/3/2020
ms.openlocfilehash: fb998499205be7645f011fe727f5e37495ff3697
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749302"
---
# <a name="stored-query-results-preview"></a>Résultats de la requête stockée (version préliminaire)

Enregistre les résultats d’une requête à haute densité et la récupère rapidement.
Les résultats de la requête stockée peuvent être utiles dans les scénarios suivants :
* Implémenter la pagination des résultats. Un résultat de requête stocké est créé sur la base d’une requête et un aperçu est affiché sur la première page. Chaque page suivante affiche la partie suivante du résultat précalculé, sans qu’il soit nécessaire de réexécuter la requête initiale.
* Enregistrer temporairement les résultats de la requête pendant l’exploration des données.

> [!NOTE]
> Les résultats de la requête stockée sont en phase préliminaire et ne doivent pas être utilisés pour les scénarios de production. 

Les résultats de la requête stockée sont accessibles jusqu’à 24 heures après le moment de la création. Les mises à jour des stratégies de sécurité (par exemple, l’accès à la base de données, la sécurité au niveau des lignes, etc.) ne sont pas propagées aux résultats de la requête stockée. Utilisez la [stored_query_results. Drop](#drop-stored_query_results) en cas de révocation des autorisations de l’utilisateur. Le résultat d’une requête stockée n’est accessible qu’à partir de l’identité principale qui l’a créée. 

Les résultats de la requête stockée se comportent comme des tables, en ce sens que l’ordre des enregistrements n’est pas préservé. Pour effectuer une pagination dans les résultats, il est recommandé que la requête contienne des colonnes d’ID uniques. Pour plus d’informations, consultez les [exemples](#examples). Si plusieurs jeux de résultats sont retournés par une requête, seul le premier jeu de résultats est stocké. 

L’utilisation des résultats de la requête stockée requiert `Database User` ou un rôle d’accès supérieur.

## <a name="store-the-results-of-a-query"></a>Stocker les résultats d’une requête

**Syntaxe**

`.set``stored_query_result` *StoredQueryResultName* [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <| *Requête*

**Arguments**

* *StoredQueryResultName*: nom du résultat de la requête stocké conforme aux règles des [noms d’entités](../query/schema-entities/entity-names.md) .
* *Requête*: une requête KQL potentiellement lourde dont les résultats seront stockés.
* *PropertyName*: (toutes les propriétés sont facultatives)
    
    | Propriété       | Type       | Description       |
    |----------------|------------|-------------------------------------------------------------------------------------|
    | `expiresAfter` | `timespan` | Littéral TimeSpan indiquant le moment où le résultat de la requête stockée expire (au maximum 24 heures). |
    | `previewCount` | `int`      | Nombre de lignes à retourner dans un aperçu. Si la valeur de cette propriété `0` est (valeur par défaut), la commande retourne toutes les lignes de résultat de la requête. |
    | `distributed`  | `bool`     | Indique que la commande stocke les résultats de requête de tous les nœuds qui exécutent la requête en parallèle. La valeur par défaut est *true*. La définition de `distributed` l’indicateur sur *false* est utile lorsque la quantité de données produites par une requête est faible ou que le nombre de nœuds de cluster est important pour empêcher la création de plusieurs petits partitions de données. |

## <a name="retrieve-a-stored-query-result"></a>Récupérer un résultat de requête stocké

Pour récupérer un résultat de requête stocké, utilisez `stored_query_result()` la fonction dans votre requête :

`stored_query_result``(`« StoredQueryResultName » `)` `|` ...

## <a name="examples"></a>Exemples

### <a name="simple-query"></a>Requête simple

Stockage d’un résultat de requête simple :

<!-- csl -->
```kusto
.set stored_query_result Numbers <| range X from 1 to 1000000 step 1
```

**Output:**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

Récupérer le résultat de la requête stockée :

<!-- csl -->
```kusto
stored_query_result("Numbers")
```

**Output:**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

### <a name="pagination"></a>Pagination

Récupérer les clics par réseau publicitaire et par jour, les sept derniers jours :

<!-- csl -->
```kusto
.set stored_query_result DailyClicksByAdNetwork7Days with (previewCount = 100) <|
Events
| where Timestamp > ago(7d)
| where EventType == 'click'
| summarize Count=count() by Day=bin(Timestamp, 1d), AdNetwork
| order by Count desc
| project Num=row_number(), Day, AdNetwork, Count
```

**Output:**

| Num | Jour | AdNetwork | Count |
|-----|-----|-----------|-------|
| 1 | 2020-01-01 00:00:00.0000000 | NeoAds | 1002 |
| 2 | 2020-01-01 00:00:00.0000000 | HighHorizons | 543 |
| 3 | 2020-01-01 00:00:00.0000000 | PieAds | 379 |
| ... | ... | ... | ... |

Récupérer la page suivante :

<!-- csl -->
```kusto
stored_query_result("DailyClicksByAdNetwork7Days")
| where Num between(100 .. 200)
```

**Output:**

| Num | Jour | AdNetwork | Nombre |
|-----|-----|-----------|-------|
| 100 | 2020-01-01 00:00:00.0000000 | CoolAds | 301 |
| 101 | 2020-01-01 00:00:00.0000000 | DreamAds | 254 |
| 102 | 2020-01-02 00:00:00.0000000 | SuperAds | 123 |
| ... | ... | ... | ... |

## <a name="control-commands"></a>Commandes de contrôle

### <a name="show-stored_query_results"></a>. afficher stored_query_results

Affiche des informations sur les résultats de la requête stockée active.

>[!NOTE]
> * Les utilisateurs disposant d' `DatabaseAdmin` `DatabaseMonitor` autorisations ou peuvent inspecter la présence de résultats de requête stockés actifs dans le contexte de la base de données.
> * Les utilisateurs disposant d' `DatabaseUser` `DatabaseViewer` autorisations ou peuvent inspecter la présence des résultats de la requête stockée actifs créés par leur principal.

#### <a name="syntax"></a>Syntaxe

`.show` `stored_query_results`

#### <a name="returns"></a>Retours

| StoredQueryResultId | Nom | nom_base_de_données | PrincipalIdentity | SizeInBytes | RowCount | Created | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | Événements | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |

### <a name="drop-stored_query_result"></a>. Drop stored_query_result

Supprime le résultat de la requête stockée active créée dans la base de données actuelle par le principal actuel.

#### <a name="syntax"></a>Syntaxe

`.drop``stored_query_result` *StoredQueryResultName*

`Database User` l’autorisation est requise pour appeler cette commande.

#### <a name="returns"></a>Retours

Retourne des informations sur les résultats de la requête stockée supprimée, par exemple :

| StoredQueryResultId | Nom | nom_base_de_données | PrincipalIdentity | SizeInBytes | RowCount | Created | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | Événements | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |

### <a name="drop-stored_query_results"></a>. Drop stored_query_results

Supprime les résultats de la requête stockée actifs créés dans la base de données active par le principal spécifié.

`Database Admin` l’autorisation est requise pour appeler cette commande.

#### <a name="syntax"></a>Syntaxe

`.drop``stored_query_results` `created-by` *Principal*

#### <a name="returns"></a>Retours

Retourne des informations sur les résultats de la requête stockée supprimée.

Exemple :

<!-- csl -->
```kusto
.drop stored_query_results by user 'aadapp=c28e9b80-2808-bed525fc0fbb'
```

| StoredQueryResultId | Nom | nom_base_de_données | PrincipalIdentity | SizeInBytes | RowCount | Created | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | Événements | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |
| 571f1a76-f5a9-49d4-b339-ba7caac19b46 | Traces | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 5212 | 100000 | 2020-10-07 14:31:01.8271231| 2020-10-08 14:31:01.8271231 |

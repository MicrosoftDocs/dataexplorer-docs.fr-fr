---
title: Splunk au langage de requête de journal Kusto dans Azure Monitor
description: Aide pour les utilisateurs familiarisés avec Splunk dans Learning Kusto log queries.
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: f574493f2029d08fd71b44c858592a6fd8467c82
ms.sourcegitcommit: b6f0f112b6ddf402e97c011a902bd70ba408e897
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94499101"
---
# <a name="splunk-to-kusto-query-language"></a>Splunk vers le langage de requête Kusto

Cet article a pour but d’aider les utilisateurs qui sont familiarisés avec Splunk à apprendre le langage de requête Kusto à écrire des requêtes de journal dans Kusto. Des comparaisons directes sont établies entre les deux pour vous permettre de comprendre les principales différences et les similitudes, et éventuellement d’approfondir vos connaissances.

## <a name="structure-and-concepts"></a>Structure et concepts

Le tableau suivant compare les concepts et les structures de données entre les journaux Splunk et Kusto.

 | Concept | Splunk | Kusto |  Commentaire |
 |:---|:---|:---|:---|
 | Unité de déploiement  | cluster |  cluster |  Kusto autorise des requêtes croisées entre les clusters arbitraires. Ce n’est pas le cas de Splunk. |
 | Caches de données |  compartiments  |  Stratégies de rétention et mise en cache |  Contrôle la période et le niveau de mise en cache des données. Ce paramètre a un impact direct sur les performances des requêtes et le coût du déploiement. |
 | Partition logique des données  |  index  |  database  |  Permet une séparation logique des données. Les deux implémentations autorisent les unions et les jointures entre ces partitions. |
 | Métadonnées d’événement structurées | N/A | table |  Splunk n’a pas le concept exposé au langage de recherche de métadonnées d’événement. Les journaux Kusto ont le concept d’une table, qui comporte des colonnes. Chaque instance d’événement est mappée à une ligne. |
 | Enregistrement de données | événement | ligne |  Changement de terminologie uniquement. |
 | Attribut d’enregistrement de données | field |  colonne |  Dans Kusto, il est prédéfini dans le cadre de la structure de la table. Dans Splunk, chaque événement possède son propre ensemble de champs. |
 | Types | type de données |  type de données |  Les types de données Kusto sont plus explicites, car ils sont définis sur les colonnes. Les deux ont la possibilité d’utiliser dynamiquement des types de données et un ensemble de types de données à peu près équivalent, notamment la prise en charge JSON. |
 | Requête et recherche  | recherche | query |  Les concepts sont essentiellement les mêmes entre Kusto et Splunk. |
 | Durée d’ingestion des événements | Temps système | ingestion_time() |  Dans Splunk, chaque événement obtient un horodatage système de l’heure à laquelle l’événement a été indexé. Dans Kusto, vous pouvez définir une stratégie appelée ingestion_time qui expose une colonne système pouvant être référencée via la fonction ingestion_time (). |

## <a name="functions"></a>Fonctions

Le tableau suivant spécifie les fonctions dans Kusto qui sont équivalentes aux fonctions Splunk.

|Splunk | Kusto |Comment
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br>`tolong()`<br>`toint()` | (1) |
| `upper`<br>`lower` |toupper()<br>`tolower()`|(1) |
| `replace` | `replace()` | (1)<br> Notez également que si `replace()` prend trois paramètres dans les deux produits, les paramètres sont différents. |
| `substr` | `substring()` | (1)<br>Notez également que Splunk utilise des index de base un. Index de base zéro Kusto notes. |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | Dans Splunk, `regex` est un opérateur. Dans Kusto, il s’agit d’un opérateur relationnel. |
| `searchmatch` | == | Dans Splunk, `searchmatch` permet de rechercher la chaîne exacte.
| `random` | rand()<br>rand(n) | La fonction de Splunk retourne un nombre compris entre zéro et 2<sup>31</sup>-1. Kusto’retourne un nombre compris entre 0,0 et 1,0, ou si un paramètre est fourni, entre 0 et n-1.
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br>Dans Kusto, l’équivalent de Splunk de relative_time (datetimeVal, offsetVal) est datetimeVal + ToTimeSpan (offsetVal).<br>Par exemple, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> devient <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) Dans Splunk, la fonction est appelée avec l’opérateur `eval`. Dans Kusto, il est utilisé dans le cadre de `extend` ou `project` .<br>(2) Dans Splunk, la fonction est appelée avec l’opérateur `eval`. Dans Kusto, il peut être utilisé avec l' `where` opérateur.


## <a name="operators"></a>Opérateurs

Les sections suivantes fournissent des exemples d’utilisation de différents opérateurs entre Splunk et Kusto.

> [!NOTE]
> Dans le cadre de l’exemple suivant, la _règle_ de champ Splunk est mappée à une table dans Kusto, et l’horodatage par défaut de Splunk est mappé à la colonne Logs Analytics _ingestion_time ()_ .

### <a name="search"></a>Recherche
Dans Splunk, vous pouvez omettre le mot clé `search` et spécifier une chaîne sans guillemets. Dans Kusto, vous devez démarrer chaque requête avec `find` , une chaîne sans guillemets est un nom de colonne et la valeur de recherche doit être une chaîne entre guillemets. 

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `search` | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Kusto | `find` | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Filtrer
Les requêtes de journal Kusto démarrent à partir d’un jeu de résultats tabulaires où le filtre. Dans Splunk, le filtrage est l’opération par défaut sur l’index actuel. Vous pouvez également utiliser l’opérateur `where` dans Splunk, mais ce n’est pas recommandé.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `search` | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Kusto | `where` | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>Obtention de n événements/lignes pour inspection 
Les requêtes de journal Kusto prennent également en charge `take` comme alias de `limit` . Dans Splunk, si les résultats sont triés, `head` retourne les n premiers résultats. Dans Kusto, Limit n’est pas ordonné, mais retourne les n premières lignes trouvées.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `head` | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Kusto | `limit` | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Obtention des n premiers événements/premières lignes classé(e)s selon un champ/une colonne
Pour les derniers résultats, dans Splunk vous utilisez `tail`. Dans Kusto, vous pouvez spécifier le sens de classement avec `asc` .

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `head` |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Kusto | `top` | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Extension du jeu de résultats avec de nouveaux champs ou de nouvelles colonnes
Splunk a également une fonction `eval`, qui ne doit pas être comparée à l’opérateur `eval`. L' `eval` opérateur dans Splunk et l' `extend` opérateur dans Kusto prennent uniquement en charge les fonctions scalaires et les opérateurs arithmétiques.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `eval` |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Kusto | `extend` | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Renommer 
Kusto utilise l' `project-rename` opérateur pour renommer un champ. `project-rename` permet à la requête de tirer parti de tous les index prédéfinis pour un champ. Splunk a un opérateur `rename` pour faire cela.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `rename` |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Kusto | `project-rename` | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Mettre en forme les résultats/Projection
Splunk ne semble pas avoir d’opérateur similaire à `project-away`. Vous pouvez utiliser l’interface utilisateur pour filtrer les champs.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `table` |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Kusto | `project`<br>`project-away` | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Agrégation
Consultez la [liste des fonctions d'](summarizeoperator.md#list-of-aggregation-functions) agrégation pour les différentes fonctions d’agrégation.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `stats` |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Kusto | `summarize` | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Join
La jointure dans Splunk présente des limitations importantes. La sous-requête a une limite de 10 000 résultats (définie dans le fichier de configuration de déploiement), et il existe un nombre limité de variantes de jointure.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `join` |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Kusto | `join` | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Trier
Dans Splunk, pour trier dans l’ordre croissant, vous devez utiliser l’opérateur `reverse`. Kusto prend également en charge la définition de l’emplacement où placer les valeurs NULL, au début ou à la fin.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `sort` |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Kusto | `order by` | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Développement à valeurs multiples
Il s’agit d’un opérateur similaire dans Splunk et Kusto.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand foo` |
| Kusto | `mvexpand` | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Facettes de résultats, champs intéressants
Dans Log Analytics, sur le Portail Azure, seule la première colonne est exposée. Toutes les colonnes sont disponibles via l’API.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `fields` |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Kusto | `facets` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>Dédupliquer
Vous pouvez utiliser `summarize arg_min()` à la place pour inverser l’ordre dans lequel l’enregistrement est choisi.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `dedup` |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Kusto | `summarize arg_max()` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue un didacticiel sur le [langage de requête Kusto](tutorial.md?pivots=azuremonitor).

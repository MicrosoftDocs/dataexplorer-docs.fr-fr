---
title: Splunk to Kusto Map for Azure Explorateur de données et Azure Monitor
description: Mappage de concept pour les utilisateurs qui sont familiarisés avec Splunk pour apprendre le langage de requête Kusto à écrire des requêtes de journal.
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 8679b47a2c698c88c4d1773f9c50dee495532ae7
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783231"
---
# <a name="splunk-to-kusto-query-language-map"></a>Carte du langage de requête Splunk vers Kusto

Cet article a pour but d’aider les utilisateurs qui sont familiarisés avec Splunk à apprendre le langage de requête Kusto à écrire des requêtes de journal avec Kusto. Les comparaisons directes sont effectuées entre les deux pour mettre en évidence les principales différences et similarités, afin que vous puissiez vous appuyer sur vos connaissances existantes.

## <a name="structure-and-concepts"></a>Structure et concepts

Le tableau suivant compare les concepts et les structures de données entre les journaux Splunk et Kusto :

 | Concept | Splunk | Kusto |  Commentaire |
 |:---|:---|:---|:---|
 | unité de déploiement  | cluster |  cluster |  Kusto autorise des requêtes entre clusters arbitraires. Ce n’est pas le cas de Splunk. |
 | caches de données |  compartiments  |  stratégies de mise en cache et de rétention |  Contrôle la période et le niveau de mise en cache des données. Ce paramètre affecte directement les performances des requêtes et le coût du déploiement. |
 | partition logique des données  |  index  |  database  |  Permet une séparation logique des données. Les deux implémentations autorisent les unions et les jointures entre ces partitions. |
 | métadonnées des événements structurés | N/A | table |  Splunk n’expose pas le concept de métadonnées d’événement au langage de recherche. Les journaux Kusto ont le concept d’une table, qui comporte des colonnes. Chaque instance d’événement est mappée à une ligne. |
 | enregistrement de données | événement | ligne |  Changement de terminologie uniquement. |
 | attribut enregistrement de données | field |  colonne |  Dans Kusto, ce paramètre est prédéfini dans le cadre de la structure de la table. Dans Splunk, chaque événement possède son propre ensemble de champs. |
 | types | type de données |  type de données |  Les types de données Kusto sont plus explicites, car ils sont définis sur les colonnes. Tous deux peuvent fonctionner de manière dynamique avec des types de données et à peu près un ensemble équivalent de types de données, notamment la prise en charge de JSON. |
 | requête et recherche  | recherche | query |  Les concepts sont essentiellement les mêmes entre Kusto et Splunk. |
 | heure d’ingestion des événements | heure système | `ingestion_time()` |  Dans Splunk, chaque événement obtient un horodateur système de l’heure à laquelle l’événement a été indexé. Dans Kusto, vous pouvez définir une stratégie appelée [ingestion_time](../management/ingestiontimepolicy.md) qui expose une colonne système pouvant être référencée via la fonction [ingestion_time ()](ingestiontimefunction.md) . |

## <a name="functions"></a>Fonctions

Le tableau suivant spécifie les fonctions dans Kusto qui sont équivalentes aux fonctions Splunk.

|Splunk | Kusto |Commentaire
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br />`tolong()`<br />`toint()` | (1) |
| `upper`<br />`lower` |toupper()<br />`tolower()`|(1) |
| `replace` | `replace()` | (1)<br /> Notez également que, bien que `replace()` prenne trois paramètres dans les deux produits, les paramètres sont différents. |
| `substr` | `substring()` | (1)<br />Notez également que Splunk utilise des index de base un. Index de base zéro Kusto notes. |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | Dans Splunk, `regex` est un opérateur. Dans Kusto, il s’agit d’un opérateur relationnel. |
| `searchmatch` | == | Dans Splunk, `searchmatch` permet de rechercher la chaîne exacte.
| `random` | rand()<br />rand(n) | La fonction de Splunk retourne un nombre compris entre 0 et 2<sup>31</sup>-1. Kusto retourne un nombre compris entre 0,0 et 1,0, ou si un paramètre est fourni, entre 0 et n-1.
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br />Dans Kusto, l’équivalent de Splunk `relative_time(datetimeVal, offsetVal)` est `datetimeVal + totimespan(offsetVal)` .<br />Par exemple, `search` &#124; `eval n=relative_time(now(), "-1d@d")` devient `...`  &#124; `extend myTime = now() - totimespan("1d")` .

(1) dans Splunk, la fonction est appelée à l’aide de l' `eval` opérateur. Dans Kusto, il est utilisé dans le cadre de `extend` ou `project` .<br />(2) dans Splunk, la fonction est appelée à l’aide de l' `eval` opérateur. Dans Kusto, il peut être utilisé avec l' `where` opérateur.


## <a name="operators"></a>Opérateurs

Les sections suivantes fournissent des exemples d’utilisation de différents opérateurs dans Splunk et Kusto.

> [!NOTE]
> Dans les exemples suivants, le champ Splunk `rule` est mappé à une table dans Kusto, et l’horodatage par défaut de Splunk est mappé à la colonne logs Analytics `ingestion_time()` .

### <a name="search"></a>Recherche

Dans Splunk, vous pouvez omettre le mot clé `search` et spécifier une chaîne sans guillemets. Dans Kusto, vous devez démarrer chaque requête avec `find` , une chaîne sans guillemets est un nom de colonne et la valeur de recherche doit être une chaîne entre guillemets. 

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `search` | `search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h` |
| Kusto | `find` | `find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)` |


### <a name="filter"></a>Filtrer

Les requêtes de journal Kusto démarrent à partir d’un jeu de résultats tabulaires dans lequel `filter` est appliqué. Dans Splunk, le filtrage est l’opération par défaut sur l’index actuel. Vous pouvez également utiliser l' `where` opérateur dans Splunk, mais nous ne le recommandons pas.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `search` | `Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h` |
| Kusto | `where` | `Office_Hub_OHubBGTaskError`<br />&#124; `where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)` |

### <a name="get-n-events-or-rows-for-inspection"></a>Obtenir *n* événements ou lignes à inspecter

Les requêtes de journal Kusto prennent également en charge `take` comme alias de `limit` . Dans Splunk, si les résultats sont ordonnés, `head` retourne les *n* premiers résultats. Dans Kusto, `limit` n’est pas ordonné, mais retourne les *n* premières lignes trouvées.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `head` | `Event.Rule=330009.2`<br />&#124; `head 100` |
| Kusto | `limit` | `Office_Hub_OHubBGTaskError`<br />&#124; `limit 100` |

### <a name="get-the-first-n-events-or-rows-ordered-by-a-field-or-column"></a>Obtenir les *n* premiers événements ou lignes classés par un champ ou une colonne

Pour les résultats inférieurs, dans Splunk, vous utilisez `tail` . Dans Kusto, vous pouvez spécifier le sens de classement à l’aide de `asc` .

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `head` |  `Event.Rule="330009.2"`<br />&#124; `sort Event.Sequence`<br />&#124; `head 20` |
| Kusto | `top` | `Office_Hub_OHubBGTaskError`<br />&#124; `top 20 by Event_Sequence` |

### <a name="extend-the-result-set-with-new-fields-or-columns"></a>Étendre le jeu de résultats avec de nouveaux champs ou colonnes

Splunk possède une `eval` fonction, mais elle n’est pas comparable à l' `eval` opérateur dans Kusto. L' `eval` opérateur dans Splunk et l' `extend` opérateur dans Kusto prennent uniquement en charge les fonctions scalaires et les opérateurs arithmétiques.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `eval` |  `Event.Rule=330009.2`<br />&#124; `eval state= if(Data.Exception = "0", "success", "error")` |
| Kusto | `extend` | `Office_Hub_OHubBGTaskError`<br />&#124; `extend state = iif(Data_Exception == 0,"success" ,"error")` |

### <a name="rename"></a>Renommer

Kusto utilise l' `project-rename` opérateur pour renommer un champ. Dans l' `project-rename` opérateur, une requête peut tirer parti de tous les index prégénérés pour un champ. Splunk a un `rename` opérateur qui fait la même chose.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `rename` |  `Event.Rule=330009.2`<br />&#124; `rename Date.Exception as execption` |
| Kusto | `project-rename` | `Office_Hub_OHubBGTaskError`<br />&#124; `project-rename exception = Date_Exception` |

### <a name="format-results-and-projection"></a>Mettre en forme les résultats et la projection

Splunk ne semble pas avoir un opérateur semblable à `project-away` . Vous pouvez utiliser l’interface utilisateur pour filtrer les champs.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `table` |  `Event.Rule=330009.2`<br />&#124; `table rule, state` |
| Kusto | `project`<br />`project-away` | `Office_Hub_OHubBGTaskError`<br />&#124; `project exception, state` |

### <a name="aggregation"></a>Agrégation

Consultez la [liste des fonctions d’agrégation](summarizeoperator.md#list-of-aggregation-functions) disponibles.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `stats` |  `search (Rule=120502.*)`<br />&#124; `stats count by OSEnv, Audience` |
| Kusto | `summarize` | `Office_Hub_OHubBGTaskError`<br />&#124; `summarize count() by App_Platform, Release_Audience` |


### <a name="join"></a>Join

`join` dans Splunk présente des limitations importantes. La sous-requête a une limite de 10 000 résultats (définie dans le fichier de configuration de déploiement) et un nombre limité de versions de jointures est disponible.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `join` |  `Event.Rule=120103* &#124; stats by Client.Id, Data.Alias` <br />&#124; `join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]` |
| Kusto | `join` | `cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions`<br />&#124; `where  Data_Hresult== -2147221040`<br />&#124; `join kind = inner (Office_System_SystemHealthMetadata`<br />&#124; `summarize by Client_Id, Data_Alias)on Client_Id`   |

### <a name="sort"></a>Trier

Dans Splunk, pour trier par ordre croissant, vous devez utiliser l' `reverse` opérateur. Kusto prend également en charge la définition de l’emplacement où placer les valeurs NULL, au début ou à la fin.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `sort` |  `Event.Rule=120103`<br />&#124; `sort Data.Hresult` <br />&#124; `reverse` |
| Kusto | `order by` | `Office_Hub_OHubBGTaskError`<br />&#124; `order by Data_Hresult,  desc` |

### <a name="multivalue-expand"></a>Développement à valeurs multiples

L’opérateur de développement à valeurs multiples est similaire dans Splunk et Kusto.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand solutions` |
| Kusto | `mv-expand` | `mv-expand solutions` |

### <a name="result-facets-interesting-fields"></a>Facettes de résultat, champs intéressants

Dans Log Analytics, sur le Portail Azure, seule la première colonne est exposée. Toutes les colonnes sont disponibles via l’API.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `fields` |  `Event.Rule=330009.2`<br />&#124; `fields App.Version, App.Platform` |
| Kusto | `facets` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `facet by App_Branch, App_Version` |

### <a name="deduplicate"></a>Dédupliquer

Dans Kusto, vous pouvez utiliser `summarize arg_min()` pour inverser l’ordre d’enregistrement choisi.

| Produit | Opérateur | Exemple |
|:---|:---|:---|
| Splunk | `dedup` |  `Event.Rule=330009.2`<br />&#124; `dedup device_id sortby -batterylife` |
| Kusto | `summarize arg_max()` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `summarize arg_max(batterylife, *) by device_id` |

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue un didacticiel sur le [langage de requête Kusto](tutorial.md?pivots=azuremonitor).

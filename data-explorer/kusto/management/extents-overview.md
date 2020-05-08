---
title: Étendues (partitions de données)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les étendues (partitions de données) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/13/2020
ms.openlocfilehash: 839042b50fce6409de29c4cf979a253a7485fb7f
ms.sourcegitcommit: ef009294b386cba909aa56d7bd2275a3e971322f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977148"
---
# <a name="extents-data-shards"></a>Étendues (partitions de données)

## <a name="overview"></a>Vue d’ensemble

Kusto est conçu pour prendre en charge les tables avec un grand nombre d’enregistrements (lignes) et beaucoup de données. Pour pouvoir gérer ces tables volumineuses, Kusto divise les données de chaque table en « tablettes » plus petites, appelées **partitions de données** ou **extents** (les deux termes sont synonymes), de sorte que l’Union de toutes les étendues de la table contient les données de la table. Les extensions individuelles sont ensuite conservées plus petites que la capacité d’un seul nœud, et les étendues sont réparties sur les nœuds du cluster, ce qui atteint la montée en charge. 

L’une d’elles peut être considérée comme une sorte de mini-table. L’extension contient des métadonnées (indiquant le schéma des données dans l’étendue et des informations supplémentaires telles que l’heure de création et les étiquettes facultatives associées aux données dans l’étendue) et les données. En outre, l’étendue contient généralement des informations qui permettent à Kusto d’interroger les données efficacement, comme un index pour chaque colonne de données dans l’étendue et un dictionnaire d’encodage si les données de colonne sont encodées. Ainsi, les données de la table sont l’Union de toutes les données dans les étendues de la table.

Les étendues sont *immuables*. Une fois créée, une extension n’est jamais modifiée et l’étendue ne peut être interrogée, réaffectée à un nœud différent ou supprimée de la table. La modification des données se produit en créant une ou plusieurs étendues et en échangeant de façon transactionnelle d’anciennes étendues avec de nouvelles étendues.

Les extensions contiennent une collection d’enregistrements, organisés physiquement en colonnes.
Cette technique (appelée « **magasin en colonnes**») permet d’encoder et de compresser efficacement les données (car différentes valeurs de la même colonne sont souvent « similaires ») et rendent l’interrogation des grandes étendues de données plus efficace, car seules les colonnes utilisées par la requête doivent être chargées. En interne, chaque colonne de données dans l’étendue est divisée en segments et les segments en blocs. Cette division (qui ne peut pas être observée pour les requêtes) permet à Kusto d’optimiser la compression et l’indexation des colonnes.

Pour maintenir l’efficacité des requêtes, les plus petites étendues sont fusionnées dans des étendues plus grandes.
Cela est effectué automatiquement par Kusto, en tant que processus en arrière-plan, selon la stratégie de [fusion](mergepolicy.md) et la stratégie [partitionnement](shardingpolicy.md)configurées.
La fusion d’étendues réduit la surcharge de gestion de l’existence d’un grand nombre d’étendues à suivre, mais plus important encore, elle permet à Kusto d’optimiser ses index et d’améliorer la compression. La fusion d’étendue s’arrête une fois qu’une extension atteint certaines limites, telles que la taille, au-delà d’une certaine étendue de fusion de point réduit au lieu d’augmenter l’efficacité.

Lorsqu’une [stratégie de partitionnement des données](partitioningpolicy.md) est définie sur une table, les étendues passent par un autre processus en arrière-plan une fois qu’elles ont été créées (après réception). Ce processus re-ingère les données à partir des étendues sources et crée des étendues *homogènes* , dans lesquelles les valeurs de la colonne qui est la *clé de partition* de la table appartiennent toutes à la même partition. Si la stratégie comprend une *clé de partition de hachage*, il est garanti que toutes les étendues homogènes qui appartiennent à la même partition sont affectées au même nœud de données dans le cluster.

> [!NOTE]
> Les opérations au niveau de l’étendue, telles que la fusion, la modification des balises d’étendue, etc., ne modifient pas les étendues existantes.
> Au lieu de cela, de nouvelles extensions sont créées dans ces opérations en fonction des étendues sources existantes, puis ces nouvelles étendues remplacent leurs Forefathers dans une transaction unique.

Le « cycle de vie » commun d’une étendue est donc :

1. L’extension est créée **par une opération** d’ingestion.
2. L’étendue est fusionnée avec d’autres extensions. Lorsque les étendues fusionnées sont peu volumineuses, Kusto effectue en fait un processus d’ingestion sur ceux-ci (cette opération est appelée **régénération**). Une fois que les étendues atteignent une certaine taille, la fusion est effectuée uniquement pour les index et les artefacts de données d’étendues dans le stockage ne sont pas modifiés.
3. L’étendue fusionnée (éventuellement un qui effectue le suivi de son lignage sur d’autres étendues fusionnées, etc.) est finalement supprimée en raison d’une stratégie de rétention. Lorsque des étendues sont supprimées en fonction de l’heure (x heures/jours plus anciennes), la date de création de l’étendue la plus récente à l’intérieur de la fusion est prise dans le calcul.

## <a name="extent-ingestion-time"></a>Temps d’ingestion des étendues

L’un des éléments d’information les plus importants pour chaque extension est son temps d’ingestion. Cette heure est utilisée par Kusto pour :

1. Rétention (les extensions qui ont été ingérées précédemment seront supprimées précédemment).
2. La mise en cache (extensions qui ont été ingérées récemment sera plus chaud).
3. L’échantillonnage (lors de l’utilisation d' `take`opérations de requête telles que, les extensions récentes sont privilégiées).

En fait, Kusto suit deux `datetime` valeurs par extension : `MinCreatedOn` et `MaxCreatedOn`.
Ces valeurs commencent de la même façon, mais lorsque l’étendue est fusionnée avec d’autres extensions, les valeurs de l’étendue résultante sont respectivement les valeurs minimale et maximale de toutes les étendues fusionnées.

L’heure d’ingestion d’une étendue peut être définie de trois manières :

1. Normalement, le nœud qui effectue l’ingestion définit cette valeur en fonction de son horloge locale.
2. Si une **stratégie de temps** d’ingestion est définie sur la table, le nœud qui effectue l’ingestion définit cette valeur en fonction de l’horloge locale du nœud d’administration du cluster, garantissant ainsi que toutes les ingestions ultérieures auront une valeur de temps d’ingestion supérieure.
3. Le client peut définir cette heure. (Cela est utile, par exemple, si le client souhaite obtenir des données de nouveau et ne souhaite pas que les données régérées apparaissent comme si elles étaient arrivées en retard, par exemple à des fins de rétention).    

## <a name="extent-tagging"></a>Balisage d’étendue

Dans le cadre des métadonnées stockées avec une extension, Kusto prend en charge l’attachement de plusieurs *balises d’étendue* facultatives dans l’étendue. Une balise d’étendue (ou simplement une *balise*) est une chaîne associée à l’étendue. Vous pouvez utiliser les commandes [. Show extents](extents-commands.md#show-extents) pour voir les balises associées à une extension, et la fonction [extent-Tags ()](../query/extenttagsfunction.md) pour voir les balises associées aux enregistrements dans une étendue.
Les balises d’étendue peuvent être utilisées pour décrire efficacement les propriétés qui se rapportent à toutes les données dans l’étendue.
Par exemple, il est possible d’ajouter une balise d’étendue pendant l’ingestion qui indique la source des données qui sont ingérées, et par la suite, d’utiliser cette balise. Lorsqu’ils décrivent des données, lorsque plusieurs étendues sont fusionnées, leurs balises associées sont également fusionnées en faisant en sorte que les balises d’étendue résultantes soient l’Union de toutes les balises d’étendue des étendues qui sont fusionnées.

Kusto assigne une signification spéciale à toutes les balises d’étendue dont la valeur a le *suffixe*de *préfixe* de format, où le *préfixe* est l’un des éléments suivants :

* `drop-by:`
* `ingest-by:`

### <a name="drop-by-extent-tags"></a>balises d’étendue « Drop-by : »

Les balises qui commencent **`drop-by:`** par un préfixe peuvent être utilisées pour contrôler les autres extensions avec lesquelles effectuer la fusion ; les extensions qui ont une balise donnée `drop-by:` peuvent être fusionnées ensemble, mais elles ne sont pas fusionnées avec d’autres extensions. Cela permet à l’utilisateur d’émettre une commande pour supprimer des extensions en fonction `drop-by:` de leur étiquette, telle que la commande suivante :

```kusto
.ingest ... with @'{"tags":"[\"drop-by:2016-02-17\"]"}'

.drop extents <| .show table MyTable extents where tags has "drop-by:2016-02-17" 
```

#### <a name="performance-notes"></a>Remarques sur les performances

* Les balises `drop-by` de surutilisation ne sont pas recommandées. La prise en charge de la suppression de données de la manière mentionnée ci-dessus est destinée à des événements qui se produisent rarement, ne remplace pas les données au niveau de l’enregistrement et s’appuie sur le fait que les données référencées de cette manière sont « en bloc ». Toute tentative de donner une étiquette différente pour chaque enregistrement, ou un petit nombre d’enregistrements, peut avoir un impact sérieux sur les performances.
* Dans les cas où ces balises ne sont pas nécessaires pendant la réception des données, il est recommandé de [Supprimer les balises](extents-commands.md#drop-extent-tags).

### <a name="ingest-by-extent-tags"></a>balises d’extension’ingestion : '

Les balises qui commencent **`ingest-by:`** par un préfixe peuvent être utilisées pour s’assurer que les données ne sont ingérées qu’une seule fois. L’utilisateur peut émettre une commande de réception qui empêche la réception des données s’il existe déjà une extension de cette balise `ingest-by:` spécifique à l’aide **`ingestIfNotExists`** de la propriété.
Les valeurs de `tags` et `ingestIfNotExists` sont des tableaux de chaînes sérialisés au format JSON.

L’exemple suivant ingère les données une seule fois (les 2e et 3e commandes ne feront rien) :

```kusto
.ingest ... with (tags = '["ingest-by:2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]', tags = '["ingest-by:2016-02-17"]')
```

> [!NOTE]
> Dans le cas courant, une commande de réception est susceptible d’inclure une `ingest-by:` balise et `ingestIfNotExists` une propriété, avec la même valeur (comme indiqué dans la troisième commande ci-dessus).

#### <a name="performance-notes"></a>Remarques sur les performances

- Il est `ingest-by` déconseillé d’utiliser des balises.
Si le Kusto d’alimentation du pipeline est connu pour avoir des doublons de données, il est recommandé de les résoudre autant que possible avant d’ingérer les données `ingest-by` dans Kusto, et d’utiliser des balises dans Kusto uniquement pour les cas où la partie ingérée par Kusto pourrait introduire des doublons en soi (par exemple, un mécanisme de nouvelle tentative peut se chevaucher avec des appels d' Toute tentative de définition d' `ingest-by` une balise unique pour chaque appel d’ingestion peut avoir un impact sérieux sur les performances.
- Dans les cas où ces balises ne sont pas nécessaires pendant la réception des données, il est recommandé de [Supprimer les balises](extents-commands.md#drop-extent-tags).

---
title: Étendues (éclats de données) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Extents (fragments de données) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/13/2020
ms.openlocfilehash: 16afb2dc7d2310e9e63ec3465937ac84c96b27e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521010"
---
# <a name="extents-data-shards"></a>Étendues (éclats de données)

## <a name="overview"></a>Vue d’ensemble

Kusto est conçu pour prendre en charge les tables avec un grand nombre d’enregistrements (lignes) et beaucoup de données. Pour être en mesure de gérer ces grandes tables, Kusto divise les données de chaque table en plus petites "tablettes" **appelées éclats** de données ou **des étendues** (les deux termes sont synonymes), de sorte que l’union de toutes les étendues de la table détient les données de la table. Les étendues individuelles sont ensuite maintenues plus petites que la capacité d’un seul nœud, et les étendues sont réparties sur les nœuds de la grappe, ce qui améliore l’échelle. 

On peut penser à une mesure comme une sorte de mini-table. L’étendue contient des métadonnées (indiquant le schéma des données dans l’étendue et des informations supplémentaires telles que son temps de création et ses balises facultatives associées aux données dans l’étendue) et les données. En outre, l’étendue contient généralement des informations qui permettent à Kusto d’interroger efficacement les données, comme un index pour chaque colonne de données dans l’étendue, et un dictionnaire de codage si les données de colonne sont codées. Ainsi, les données du tableau sont l’union de toutes les données dans les étendues de la table.

Les étendues sont *immuables*. Une fois créé, une étendue n’est jamais modifiée, et l’étendue ne peut être demandée, réaffectée à un nœud différent, ou abandonnée hors de la table. La modification des données se fait en créant une ou plusieurs nouvelles étendues et en échangeant avec transaction de l’ancienne étendue(s) avec une nouvelle ampleur.s.

Les étendues contiennent une collection de documents, physiquement disposés en colonnes.
Cette technique (appelée **magasin columnaire**) permet d’encoder et de compresser efficacement les données (parce que différentes valeurs de la même colonne souvent "ressembler" les unes aux autres), et rend la requête pour de grandes étendues de données plus efficaces parce que juste les colonnes utilisées par la requête doivent être chargées. En interne, chaque colonne de données dans l’étendue est subdivisé en segments, et les segments en blocs. Cette division (qui n’est pas observable aux requêtes) permet à Kusto d’optimiser la compression et l’indexation des colonnes.

Pour maintenir l’efficacité des requêtes, de plus petites étendues sont fusionnées dans de plus grandes étendues.
Ceci est effectué automatiquement par Kusto, comme un processus d’arrière-plan, selon la politique de [fusion](mergepolicy.md) configurée et la [politique d’éclat](shardingpolicy.md).
La fusion des étendues de travail réduit les frais généraux de gestion d’avoir un grand nombre d’étendues à suivre, mais plus important encore, il permet à Kusto d’optimiser ses index et d’améliorer la compression. La fusion de l’étendue s’arrête une fois qu’une étendue atteint certaines limites, comme la taille, car au-delà d’un certain point, les étendues de fusion réduisent plutôt qu’augmentent l’efficacité.

Lorsqu’une [politique de partitionnement des données](partitioningpolicy.md) est définie sur une table, les étendues passent par un autre processus d’arrière-plan après leur création (après l’ingestion). Ce processus réingére les données à partir des étendues de source et crée des étendues *homogènes,* dans lesquelles les valeurs de la colonne qui est la clé de *partition* de la table appartiennent toutes à la même partition. Si la stratégie comprend une *clé de partition de hachage,* il est garanti que toutes les étendues homogènes qui appartiennent à la même partition sont attribuées au même nœud de données dans le cluster.

> [!NOTE]
> Les opérations au niveau de l’étendue, telles que la fusion, la modification des étiquettes d’étendue, etc. ne modifient pas les étendues existantes.
> Au contraire, de nouvelles étendues sont créées dans ces opérations en fonction des étendues sources existantes, puis ces nouvelles étendues remplacent leurs ancêtres dans une seule transaction.

Le « cycle de vie » commun d’une ampleur est donc :

1. L’étendue est créée par une opération **d’ingestion.**
2. L’étendue est fusionnée avec d’autres étendues. Lorsque les étendues fusionnées sont petites, Kusto effectue en fait un processus d’ingestion sur eux (c’est ce qu’on appelle **la reconstruction**). Une fois que les étendues atteignent une certaine taille, la fusion n’est effectuée que pour les index et les artefacts de données des étendues dans le stockage ne sont pas modifiés.
3. L’étendue fusionnée (peut-être celle qui suit sa lignée à d’autres étendues fusionnées et ainsi de suite) est finalement abandonnée en raison d’une politique de rétention. Lorsque les étendues sont supprimées en fonction du temps (heures x plus anciennes / jours), la date de création de la plus récente étendue à l’intérieur de la fusion est prise dans le calcul.

## <a name="extent-ingestion-time"></a>Temps d’ingestion d’étendue

L’une des informations les plus importantes pour chaque étendue est son temps d’ingestion. Ce temps est utilisé par Kusto pour:

1. La rétention (les étendues qui ont été ingérées plus tôt seront supprimées plus tôt).
2. La cachage (les étendues qui ont été ingérées récemment seront plus chaudes).
3. Échantillonnage (lors de l’utilisation des opérations de requête telles que `take`, les étendues récentes sont favorisées).

En fait, Kusto `datetime` suit deux `MinCreatedOn` `MaxCreatedOn`valeurs par étendue: et .
Ces valeurs commencent de la même façon, mais lorsque l’étendue est fusionnée avec d’autres étendues, les valeurs de l’étendue qui en résultent sont les valeurs minimales et maximales, respectivement, sur toutes les étendues fusionnées.

Le temps d’ingestion d’une étendue peut être établi de l’une des trois façons :

1. Normalement, le nœud effectuant l’ingestion définit cette valeur en fonction de son horloge locale.
2. Si une politique de **temps d’ingestion** est mise sur la table, le nœud effectuant l’ingestion définit cette valeur en fonction de l’horloge locale du nœud d’administration du cluster, garantissant que toutes les ingestions ultérieures auront une valeur de temps d’ingestion plus élevée.
3. Le client peut définir cette fois. (Cela est utile, par exemple, si le client veut réingérer les données et ne veut pas que les données ingérées à nouveau apparaissent comme si elles étaient arrivées en retard, par exemple à des fins de conservation).    

## <a name="extent-tagging"></a>Mesure de marquage

Dans le cadre des métadonnées stockées dans une certaine mesure, Kusto prend en charge l’attachement de plusieurs *étiquettes d’étendue* facultative dans la mesure. Une étiquette d’étendue (ou simplement *tag*), est une chaîne qui est associée à l’étendue. On peut utiliser les commandes [d’étendues .show](extents-commands.md#show-extents) pour voir les balises associées à une étendue, et la fonction [d’extension-étiquettes()](../query/extenttagsfunction.md) pour voir les balises associées aux enregistrements dans une certaine mesure.
Les balises d’étendue peuvent être utilisées pour décrire efficacement les propriétés qui se rapportent à toutes les données dans la mesure.
Par exemple, on pourrait ajouter une étiquette d’étendue pendant l’ingestion qui indique la source des données étant ingérées, et plus tard utiliser cette étiquette. Lorsqu’ils décrivent les données, lorsque deux étendues ou plus sont fusionnées, leurs étiquettes associées sont également fusionnées en faisant en fait que les étiquettes de l’étendue qui en résultent sont l’union de toutes les étiquettes d’étendue des étendues fusionnées.

Kusto attribue une signification particulière dans toute mesure les balises dont la valeur a le *suffixe* *de préfixe* de format , où *le préfixe* est l’un des :

* `drop-by:`
* `ingest-by:`

## <a name="drop-by-extent-tags"></a>'drop-by:' étiquettes d’étendue

Les balises **`drop-by:`** qui commencent par un préfixe peuvent être utilisées pour contrôler les autres étendues avec lesquelles fusionner; les étendues qui `drop-by:` ont une étiquette donnée peuvent être fusionnées, mais elles ne seront pas fusionnées avec d’autres étendues. Cela permet à l’utilisateur d’émettre `drop-by:` une commande pour laisser tomber les étendues en fonction de leur balise, comme la commande suivante:

```kusto
.ingest ... with @'{"tags":"[\"drop-by:2016-02-17\"]"}'

.drop extents <| .show table MyTable extents where tags has "drop-by:2016-02-17" 
```

### <a name="performance-notes"></a>Notes de performance

* Les balises de sur-utilisation `drop-by` ne sont pas recommandées. L’appui à l’abandon des données de la manière mentionnée ci-dessus est destiné aux événements rarement survenus, n’est pas destiné à remplacer les données de niveau record et repose de façon critique sur le fait que les données étiquetées de cette manière sont « volumineuses ». Tenter de donner une étiquette différente pour chaque enregistrement, ou un petit nombre d’enregistrements, peut entraîner un impact grave sur le rendement.
* Dans les cas où ces balises ne sont pas nécessaires une certaine période de temps après la ingérée des données, il est recommandé de [laisser tomber les balises](extents-commands.md#drop-extent-tags).

## <a name="ingest-by-extent-tags"></a>'ingest-by:' étiquettes d’étendue

Les balises **`ingest-by:`** qui commencent par un préfixe peuvent être utilisées pour s’assurer que les données ne sont ingérées qu’une seule fois. L’utilisateur peut émettre une commande ingéreuse qui empêche les données d’être `ingest-by:` ingérées s’il ya déjà une étendue avec cette balise spécifique en utilisant la **`ingestIfNotExists`** propriété.
Les valeurs `tags` pour `ingestIfNotExists` les deux et sont des tableaux de cordes, sérialisé comme JSON.

L’exemple suivant n’ingèrent les données qu’une seule fois (les 2e et 3e commandes ne feront rien) :

```kusto
.ingest ... with (tags = '["ingest-by:2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]', tags = '["ingest-by:2016-02-17"]')
```

> [!NOTE]
> Dans le cas courant, une commande ingest `ingest-by:` est `ingestIfNotExists` susceptible d’inclure à la fois une étiquette et une propriété, réglée à la même valeur (comme indiqué dans la 3ème commande ci-dessus).

### <a name="performance-notes"></a>Notes de performance

- Il n’est pas recommandé de surutiliser les `ingest-by` étiquettes.
Si le pipeline alimentant Kusto est connu pour avoir des duplications de données, il est recommandé de `ingest-by` résoudre ces autant que possible avant d’ingériser les données dans Kusto, et d’utiliser des balises dans Kusto uniquement pour les cas où la partie qui ingère à Kusto pourrait introduire des doublons par lui-même (par exemple, il ya un mécanisme de retentie qui peut se chevaucher avec déjà en cours d’ingestion appels). Tenter de définir `ingest-by` une étiquette unique pour chaque appel d’ingestion peut entraîner un impact grave sur les performances.
- Dans les cas où ces balises ne sont pas nécessaires une certaine période de temps après la ingérée des données, il est recommandé de [laisser tomber les balises](extents-commands.md#drop-extent-tags).
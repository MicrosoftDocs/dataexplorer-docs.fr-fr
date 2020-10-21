---
title: Étendues (partitions de données)-Azure Explorateur de données
description: Cet article décrit les étendues (partitions de données) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/13/2020
ms.openlocfilehash: c1ed0de6f638828abe120caffcb5e14517f09a02
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342737"
---
# <a name="extents-data-shards"></a>Étendues (partitions de données)

## <a name="overview"></a>Vue d’ensemble

Kusto est conçu pour prendre en charge les tables avec un grand nombre d’enregistrements (lignes) et de grandes quantités de données. Pour gérer ces tables volumineuses, les données de chaque table sont divisées en « tablettes » plus petites, appelées **données partitions** ou **étendues** (les deux termes sont synonymes). L’Union de toutes les étendues de la table contient les données de la table. Les extensions individuelles sont conservées plus petites que la capacité d’un seul nœud, et les étendues sont réparties sur les nœuds du cluster, ce qui atteint la montée en charge.

Une extension est semblable à un type de mini-table. Il contient des données et des métadonnées, ainsi que des informations telles que son heure de création et des balises facultatives associées à ses données. En outre, l’étendue contient généralement des informations qui permettent à Kusto d’interroger efficacement les données.
Par exemple, un index pour chaque colonne de données dans l’étendue et un dictionnaire d’encodage, si les données de colonne sont encodées. Par conséquent, les données de la table sont l’Union de toutes les données dans les étendues de la table.

Les extensions sont immuables et ne peuvent jamais être modifiées. Elle peut uniquement être interrogée, réaffectée à un nœud différent, ou supprimée de la table. La modification des données se produit en créant une ou plusieurs étendues et en échangeant des extensions anciennes de façon transactionnelle avec d’autres.

Les extensions contiennent une collection d’enregistrements qui sont organisés physiquement en colonnes.
Cette technique est appelée **magasin en colonnes**. Il permet d’encoder et de compresser efficacement les données, car différentes valeurs de la même colonne sont souvent « similaires ». Cela permet également d’optimiser l’interrogation de grandes quantités de données, car seules les colonnes utilisées par la requête doivent être chargées. En interne, chaque colonne de données dans l’étendue est divisée en segments et les segments en blocs. Cette division n’est pas observable pour les requêtes et permet à Kusto d’optimiser la compression et l’indexation des colonnes.

Pour maintenir l’efficacité des requêtes, les plus petites étendues sont fusionnées dans des étendues plus grandes.
La fusion est effectuée automatiquement, en tant que processus en arrière-plan, en fonction de la stratégie de [fusion](mergepolicy.md) et [partitionnement](shardingpolicy.md)configurée.
La fusion des extensions réduit la charge de gestion liée à l’utilisation d’un grand nombre d’étendues. Plus important encore, il permet à Kusto d’optimiser ses index et d’améliorer la compression.

La fusion d’étendue s’arrête une fois qu’une extension atteint certaines limites, telles que la taille, puisque au-delà d’un certain point, la fusion réduit au lieu d’augmenter l’efficacité.

Lorsqu’une [stratégie de partitionnement des données](partitioningpolicy.md) est définie sur une table, les étendues passent par un autre processus en arrière-plan une fois qu’elles ont été créées (après réception). Ce processus reréceptionne les données à partir des étendues sources et crée des étendues *homogènes* , dans lesquelles les valeurs de la colonne qui correspond à la *clé de partition* de la table appartiennent toutes à la même partition. Si la stratégie comprend une *clé de partition de hachage*, toutes les étendues homogènes appartenant à la même partition seront affectées au même nœud de données dans le cluster.

> [!NOTE]
> Les opérations au niveau de l’étendue, telles que la fusion ou la modification des balises d’étendue, ne modifient pas les extensions existantes.
> Au lieu de cela, de nouvelles extensions sont créées dans ces opérations, en fonction des étendues sources existantes. Les nouvelles étendues remplacent leurs Forefathers dans une transaction unique.

Le cycle de vie des étendues courantes est le suivant :

1. L’extension est créée **par une opération** d’ingestion.
1. L’étendue est fusionnée avec d’autres extensions. Lorsque les étendues fusionnées sont peu volumineuses, Kusto effectue en fait un processus d’ingestion, appelé **Rebuild**. Une fois que les étendues atteignent une certaine taille, la fusion est effectuée uniquement pour les index. Les artefacts de données d’étendues dans le stockage ne sont pas modifiés.
1. L’étendue fusionnée (éventuellement un qui effectue le suivi de son lignage à d’autres extensions fusionnées, et ainsi de suite) est finalement supprimée en raison d’une stratégie de rétention. 
   Lorsque les extensions sont supprimées, en fonction de l’heure (x heures/jours plus anciennes), la date de création de la plus récente dans l’étendue fusionnée est utilisée dans le calcul.

## <a name="extent-creation-time"></a>Heure de création de l’extension

L’un des éléments d’information les plus importants pour chaque extension est son heure de création. Cette heure est utilisée pour :

1. Les étendues de **rétention** créées précédemment seront supprimées précédemment.
1. **Mise en cache** -étendues créées récemment seront conservées dans le [cache à chaud](cachepolicy.md))
1. **Échantillonnage** -les extensions récentes sont privilégiées lors de l’utilisation d’opérations de requête telles que `take`

En fait, Kusto suit deux `datetime` valeurs par extension : `MinCreatedOn` et `MaxCreatedOn` .
Au départ, les deux valeurs sont identiques. Lorsque l’étendue est fusionnée avec d’autres extensions, les nouvelles valeurs sont conformes aux valeurs minimale et maximale d’origine des étendues fusionnées.

Normalement, l’heure de création d’une extension est définie en fonction de l’heure à laquelle les données dans l’étendue sont ingérées. Les clients peuvent éventuellement remplacer l’heure de création de l’étendue en fournissant une autre heure de création dans les [Propriétés](../../ingestion-properties.md)d’ingestion.
Le remplacement est utile, par exemple, à des fins de rétention, si le client souhaite recevoir des données et ne souhaite pas qu’il apparaisse comme s’il arrivait en retard.

## <a name="extent-tagging"></a>Balisage d’étendue

Kusto prend en charge l’attachement de plusieurs *balises d’étendue* facultatives dans l’étendue, dans le cadre de ses métadonnées. Une balise d’étendue (ou simplement une *balise*) est une chaîne associée à l’étendue. Vous pouvez utiliser les commandes [. Show extents](./show-extents.md) pour voir les balises associées à une extension, et la fonction [extent-Tags ()](../query/extenttagsfunction.md) pour voir les balises associées aux enregistrements dans une étendue.
Les balises d’étendue peuvent être utilisées pour décrire efficacement les propriétés communes à toutes les données dans l’étendue.
Par exemple, vous pouvez ajouter une balise d’étendue lors de l’ingestion, qui indique la source des données ingérées et utiliser cette balise ultérieurement. Étant donné que les étendues décrivent les données, lorsque plusieurs fusions sont fusionnées, leurs balises associées sont également fusionnées. Les balises de l’étendue résultante seront l’Union de toutes les balises de ces étendues fusionnées.

Kusto assigne une signification spéciale à toutes les balises d’étendue dont la valeur a le *suffixe*de *préfixe* de format, où le *préfixe* est l’un des éléments suivants :

* `drop-by:`
* `ingest-by:`

### <a name="drop-by-extent-tags"></a>balises d’étendue « Drop-by : »

Les balises qui commencent par un `drop-by:` préfixe peuvent être utilisées pour contrôler les autres extensions avec lesquelles effectuer la fusion. Les extensions qui ont une `drop-by:` balise donnée peuvent être fusionnées ensemble, mais elles ne sont pas fusionnées avec d’autres extensions. Vous pouvez ensuite émettre une commande pour supprimer des extensions en fonction de leur `drop-by:` étiquette.

Par exemple :

```kusto
.ingest ... with @'{"tags":"[\"drop-by:2016-02-17\"]"}'

.drop extents <| .show table MyTable extents where tags has "drop-by:2016-02-17" 
```

#### <a name="performance-notes"></a>Remarques sur les performances

* N’abusez pas de `drop-by` balises. La suppression de données de la manière mentionnée ci-dessus est destinée à des événements qui se produisent rarement. Il ne s’agit pas de remplacer les données au niveau de l’enregistrement et s’appuie sur le fait que les données marquées de cette manière sont en vrac. Toute tentative de donner une étiquette différente pour chaque enregistrement, ou un petit nombre d’enregistrements, peut avoir un impact sérieux sur les performances.
* Si `drop-by` les balises ne sont pas nécessaires pendant un certain laps de temps après la réception des données, nous vous recommandons de [Supprimer les balises](#drop-by-extent-tags).

### <a name="ingest-by-extent-tags"></a>balises d’extension’ingestion : '

Les balises qui commencent par un `ingest-by:` préfixe peuvent être utilisées pour s’assurer que les données ne sont ingérées qu’une seule fois. Vous pouvez émettre une **`ingestIfNotExists`** commande de propriété qui empêche les données d’être ingérées s’il existe déjà une extension avec cette `ingest-by:` balise spécifique.
Les valeurs de `tags` et `ingestIfNotExists` sont des tableaux de chaînes sérialisés au format JSON.

L’exemple suivant ingère les données une seule fois. Les 2e et 3e commandes ne font rien :

```kusto
.ingest ... with (tags = '["ingest-by:2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]', tags = '["ingest-by:2016-02-17"]')
```

> [!NOTE]
> En règle générale, une commande de réception est susceptible d’inclure une `ingest-by:` balise et une `ingestIfNotExists` propriété, avec la même valeur, comme indiqué dans la troisième commande ci-dessus.

#### <a name="performance-notes"></a>Remarques sur les performances

* Il est déconseillé d’utiliser des `ingest-by` balises.
Si le Kusto d’alimentation du pipeline est connu pour avoir des doublons de données, nous vous recommandons de résoudre ces doublons autant que possible avant d’ingérer les données dans Kusto. En outre, utilisez `ingest-by` des balises dans Kusto uniquement lorsque la partie qui ingère Kusto peut introduire des doublons par lui-même (par exemple, un mécanisme de nouvelle tentative peut chevaucher des appels d’ingestion déjà en cours). Toute tentative de définition d’une `ingest-by` balise unique pour chaque appel d’ingestion peut avoir un impact sérieux sur les performances.
* Si ces étiquettes ne sont pas requises pendant un certain laps de temps après la réception des données, nous vous recommandons de [supprimer des balises d’étendue](drop-extent-tags.md).

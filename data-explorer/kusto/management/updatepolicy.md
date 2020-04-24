---
title: Stratégie de mise à jour - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de mise à jour dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 812a5af932f69d898bb419631fa6ea3c6bc0795e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519395"
---
# <a name="update-policy"></a>Mettre à jour la stratégie

La stratégie de mise à jour est fixée sur une **table cible** qui demande à Kusto d’y annexer automatiquement des données chaque fois que de nouvelles données sont insérées dans le **tableau source**. La **requête** de la stratégie de mise à jour s’exécute sur les données insérées dans le tableau source. Cela permet, par exemple, la création d’une table comme vue filtrée d’une autre table, peut-être avec un schéma différent, la politique de rétention, et ainsi de suite.

Par défaut, l’échec de l’exécution de la stratégie de mise à jour n’affecte pas l’ingestion de données vers le tableau source. Si la stratégie de mise à jour est définie comme **transactionnelle,** l’échec de l’exécution de la stratégie de mise à jour force l’ingestion de données dans le tableau source à l’échec ainsi. (Les soins doivent être utilisés lorsque cela est fait parce que certaines erreurs de l’utilisateur, telles que la définition d’une requête incorrecte dans la stratégie de mise à jour, pourraient empêcher **toute** donnée d’être ingérée dans le tableau source.) Les données ingérées dans la « limite » des stratégies de mise à jour transactionnelles deviennent disponibles pour une requête dans une seule transaction.

La requête de la stratégie de mise à jour est exécutée en mode spécial, dans lequel elle « ne voit » que les données nouvellement ingérées à la table source. Il n’est pas possible d’interroger les données déjà ingérées de la table source dans le cadre de cette requête. Le faire se traduit rapidement par des ingestions quadratiques-temps.

Étant donné que la stratégie de mise à jour est définie sur la table de destination, l’ingestion de données dans un tableau source peut entraîner l’exécution de multiples requêtes sur ces données. L’ordre d’exécution des politiques de mise à jour n’est pas défini.

Supposons, par exemple, que le tableau source est une table de traçabilité à haut débit avec des données intéressantes formatées comme colonne de texte libre. Supposons également que le tableau cible (sur lequel la politique de mise à jour est définie) n’accepte que des traces spécifiques, et avec un schéma bien structuré qui est une transformation des données originales de texte libre en utilisant [l’opérateur d’analyse de Kusto](../query/parseoperator.md).

La politique de mise à jour se comporte de la même façon que l’ingestion régulière et est soumise aux mêmes restrictions et aux mêmes pratiques exemplaires. Par exemple, il s’évolue avec la taille du cluster, et fonctionne plus efficacement si les ingestions sont faites en vrac.

## <a name="commands-that-trigger-the-update-policy"></a>Commandes qui déclenchent la stratégie de mise à jour

Les stratégies de mise à jour prennent effet lorsque les données sont ingérées ou déplacées vers (des étendues sont créées) un tableau utilisant l’une des commandes suivantes :

* [.ingest (tirer)](../management/data-ingestion/ingest-from-storage.md)
* [.ingest (en ligne)](../management/data-ingestion/ingest-inline.md)
* [.set .append .set-or-append .set-or-replace](../management/data-ingestion/ingest-from-query.md)
* [.déplacer les étendues](../management/extents-commands.md#move-extents)
* [.remplacer les étendues](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>L’objet de la politique de mise à jour

Un tableau peut avoir zéro, un ou plus d’objets de stratégie de mise à jour qui lui sont associés.
Chaque objet est représenté comme un sac de propriété JSON, avec les propriétés suivantes définies:

|Propriété |Type |Description  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |États si la politique de mise à jour est activée (vraie) ou désactivée (fausse)                                                                                                                               |
|Source                        |`string`|Nom du tableau qui déclenche la politique de mise à jour à invoquer                                                                                                                                 |
|Requête                         |`string`|Une requête Kusto CSL qui est utilisée pour produire les données pour la mise à jour                                                                                                                           |
|IsTransactional               |`bool`  |États si la politique de mise à jour est transactionnelle ou non (par défaut à faux). Le défaut d’exécuter une stratégie de mise à jour transactionnelle fait en sorte que le tableau source n’est pas également mis à jour avec de nouvelles données.   |
|PropagateIngestionProperties  |`bool`  |Les États si les propriétés d’ingestion (étiquettes d’étendue et temps de création) spécifiées lors de l’ingestion dans le tableau source doivent également s’appliquer à ceux du tableau dérivé.                 |

> [!NOTE]
>
> * Le tableau source et le tableau pour lequel la stratégie de mise à jour est définie **doivent être dans la même base de données**.
> * La requête **ne** peut pas inclure de requêtes croisées ni de requêtes à grappes croisées.
> * La requête peut invoquer des fonctions stockées.
> * La requête est automatiquement portée pour couvrir les enregistrements nouvellement ingérés seulement.
> * Les mises à jour en cascade`[update]`sont autorisées (TableA`[update]`-- --> TableB`[update]`-- --> TableC -- --> ...)
> * Dans le cas où les stratégies de mise à jour sont définies sur plusieurs tableaux d’une manière circulaire, cela est détecté au moment de la course et la chaîne de mises à jour est coupée (ce qui signifie que les données ne seront ingérées qu’une seule fois à chaque table de la chaîne des tables touchées).
> * Lorsque vous `Source` faites référence `Query` à la table dans la partie de la stratégie (ou dans les fonctions référencées par `cluster("ClusterName").database("DatabaseName").TableName`ce dernier), assurez-vous de ne **pas** utiliser le nom qualifié de la table (ce qui signifie, l’utilisation `TableName` et non **ni).** `database("DatabaseName").TableName`
> * La requête de la stratégie de mise à jour ne peut pas faire référence à une table avec une [stratégie de sécurité au niveau de ligne](./rowlevelsecuritypolicy.md) qui est activée.
> * Une requête qui est exécutée dans le cadre d’une politique de mise à jour n’a **pas** l’accès à des tableaux qui ont activé la [politique RestrictedViewAccess.](restrictedviewaccesspolicy.md)
> * `PropagateIngestionProperties`n’entre en vigueur que dans les opérations d’ingestion. Lorsque la stratégie de mise `.move extents` à `.replace extents` jour est déclenchée dans le cadre d’une commande ou d’une commande, cette option **n’a aucun** effet.
> * Lorsque la stratégie de mise à `.set-or-replace` jour est invoquée dans le cadre d’une commande, le comportement par défaut est que les données dans le tableau dérivé sont également remplacées, comme il est dans le tableau source.

## <a name="retention-policy-on-the-source-table"></a>Politique de rétention sur la table source

Afin de ne pas conserver les données brutes dans le tableau source, vous pouvez définir une période de suppression souple de 0 dans la stratégie de [conservation](retentionpolicy.md)du tableau source , tout en définissant la stratégie de mise à jour comme transactionnelle.

Cela se traduira par :
* Les données de source n’étant pas demandées à partir du tableau source.
* Les données sources n’ont pas persisté au stockage durable dans le cadre de l’opération d’ingestion.
* Amélioration des performances de l’opération.
* Réduction des ressources utilisées après l’ingestion, pour les opérations de toilettage des antécédents effectuées sur [les étendues](../management/extents-overview.md) du tableau source.

## <a name="failures"></a>Échecs

Dans certains cas, l’ingestion de données dans le tableau source réussit, mais la politique de mise à jour échoue lors de l’ingestion à la table cible.

Les défaillances rencontrées pendant la mise à jour des stratégies peuvent être récupérées à l’aide de la [commande d’échecs d’ingestion .show,](../management/ingestionfailures.md)comme suit :
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

Les échecs sont traités comme suit :

* **Politique non transactionnelle**: L’échec est ignoré par Kusto. Toute nouvelle tentative est la responsabilité du propriétaire de données.  
* **Politique transactionnelle**: L’opération d’ingestion initiale qui a déclenché la mise à jour échouera également. Le tableau source et la base de données ne seront pas modifiés avec de nouvelles données.
  * Dans le cas où `pull` la méthode d’ingestion est (le service de gestion des données de Kusto est impliqué dans le processus d’ingestion), il ya une nouvelle tentative automatisée sur l’ensemble de l’opération d’ingestion, orchestrée par le service de gestion des données de Kusto, selon la logique suivante:
    * Les retries sont faites jusqu’à ce qu’elles atteignent le plus tôt (par `DataImporterMaximumRetryPeriod` défaut de 2 jours) et `DataImporterMaximumRetryAttempts` (par défaut et 10).
    * Les deux paramètres ci-dessus peuvent être modifiés dans la configuration du service de gestion des données, par KustoOps.
    * La période de backoff commence à partir de 2 minutes, et croît de façon exponentielle (2 -> 4 -> 8 -> 16 ... minutes)
  * Dans tous les autres cas, toute nouvelle tentative est la responsabilité du propriétaire de données.



## <a name="control-commands"></a>Commandes de contrôle

* Utilisez [la mise à jour de la politique TABLE de tableau .show](../management/update-policy.md#show-update-policy) pour afficher la stratégie de mise à jour actuelle d’une table.
* Utilisez [la mise à jour de la stratégie TABLE de table .alter](../management/update-policy.md#alter-update-policy) pour définir la stratégie de mise à jour actuelle d’une table.
* Utilisez [la mise à jour de la politique TABLE de fusion .alter-fusion](../management/update-policy.md#alter-merge-table-table-policy-update) pour annexer à la stratégie de mise à jour actuelle d’un tableau.
* Utilisez [.supprimer la mise à jour de la stratégie TABLE](../management/update-policy.md#delete-table-table-policy-update) pour annexer à la stratégie de mise à jour actuelle d’un tableau.

## <a name="testing-an-update-policys-performance-impact"></a>Tester l’impact de la performance d’une politique de mise à jour

La définition d’une stratégie de mise à jour peut affecter les performances d’un cluster Kusto parce qu’elle affecte toute ingestion dans le tableau source. Il est fortement `Query` recommandé que la partie de la stratégie de mise à jour soit optimisée pour bien performer.
Vous pouvez tester l’impact supplémentaire des performances d’une stratégie de mise à jour sur une opération d’ingestion en l’invoquant sur des étendues spécifiques et déjà existantes, avant de créer ou de modifier la stratégie et/ou une fonction qu’elle utilise dans sa `Query` partie.

Cet exemple repose sur les données suivantes :

* Le nom de `Source` table source (la `MySourceTable`propriété de la stratégie de mise à jour) est .
* La `Query` propriété de la stratégie `MyFunction()`de mise à jour appelle une fonction nommée .

À l’aide de [requêtes .show](../management/queries.md), vous pouvez évaluer l’utilisation des ressources (CPU, mémoire, etc.) de la requête suivante, et/ou de multiples exécutions de celui-ci.

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```
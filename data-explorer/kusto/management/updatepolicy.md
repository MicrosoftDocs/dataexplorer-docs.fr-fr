---
title: Kusto mettre à jour la stratégie pour les données ajoutées à une source-Azure Explorateur de données
description: Cet article décrit la stratégie de mise à jour dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 7d2b89e0723bbecb29ffd582ae20c2ee41199e45
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618494"
---
# <a name="update-policy"></a>Mettre à jour la stratégie

La stratégie de mise à jour est définie sur une **table cible** qui indique à Kusto de lui ajouter automatiquement des données chaque fois que de nouvelles données sont insérées dans la **table source**. La **requête** de la stratégie de mise à jour s’exécute sur les données insérées dans la table source. Cela permet, par exemple, la création d’une table en tant que vue filtrée d’une autre table, éventuellement avec un schéma différent, une stratégie de rétention, etc.

Par défaut, l’exécution de la stratégie de mise à jour n’affecte pas l’ingestion des données dans la table source. Si la stratégie de mise à jour est définie comme étant **transactionnelle**, l’échec de l’exécution de la stratégie de mise à jour entraîne l’échec de l’ingestion des données dans la table source. (La prudence doit être utilisée lorsque cette opération est effectuée car certaines erreurs de l’utilisateur, telles que la définition d’une requête incorrecte dans la stratégie de mise à jour, **peuvent empêcher la réception de données** dans la table source.) Les données ingérées dans la « limite » des stratégies de mise à jour transactionnelle deviennent disponibles pour une requête dans une transaction unique.

La requête de la stratégie de mise à jour est exécutée dans un mode spécial, dans lequel elle « ne voit » que les données nouvellement ingérées dans la table source. Il n’est pas possible d’interroger les données déjà ingérées de la table source dans le cadre de cette requête. Cela permet d’obtenir rapidement des ingestions de temps quadratiques.

Étant donné que la stratégie de mise à jour est définie sur la table de destination, l’ingestion de données dans une table source peut entraîner l’exécution de plusieurs requêtes sur ces données. L’ordre d’exécution des stratégies de mise à jour n’est pas défini.

Par exemple, supposons que la table source est une table de trace à haut débit avec des données intéressantes mises en forme en tant que colonne de texte libre. Supposons également que la table cible (sur laquelle la stratégie de mise à jour est définie) accepte uniquement des lignes de suivi spécifiques et un schéma bien structuré qui est une transformation des données de texte libre d’origine à l’aide de l' [opérateur d’analyse](../query/parseoperator.md)de Kusto.

La stratégie de mise à jour se comporte de la même façon que l’ingestion normale et est soumise aux mêmes restrictions et meilleures pratiques. Par exemple, il évolue avec la taille du cluster et fonctionne plus efficacement si les ingestions sont effectuées dans de grands volumes.

## <a name="commands-that-trigger-the-update-policy"></a>Commandes qui déclenchent la stratégie de mise à jour

Les stratégies de mise à jour prennent effet lorsque les données sont ingérées ou déplacées vers (les extensions sont créées dans) une table à l’aide de l’une des commandes suivantes :

* [. ingestion (pull)](../management/data-ingestion/ingest-from-storage.md)
* [. ingestion (Inline)](../management/data-ingestion/ingest-inline.md)
* [. Set |. Append |. set-or-Append |. set-or-Replace](../management/data-ingestion/ingest-from-query.md)
* [. déplacer des étendues](../management/extents-commands.md#move-extents)
* [. remplacement des extensions](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>Objet de stratégie de mise à jour

Une table peut être associée à zéro, un ou plusieurs objets de stratégie de mise à jour.
Chaque objet de ce type est représenté sous la forme d’un conteneur de propriétés JSON, avec les propriétés suivantes définies :

|Propriété |Type |Description  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |Indique si la stratégie de mise à jour est activée (true) ou désactivée (false)                                                                                                                               |
|Source                        |`string`|Nom de la table qui déclenche l’appel de la stratégie de mise à jour                                                                                                                                 |
|Requête                         |`string`|Une requête Kusto CSL utilisée pour produire les données pour la mise à jour                                                                                                                           |
|IsTransactional               |`bool`  |Indique si la stratégie de mise à jour est transactionnelle ou non (false par défaut). L’échec de l’exécution d’un résultat de stratégie de mise à jour transactionnelle dans la table source n’est pas mis à jour avec de nouvelles données.   |
|PropagateIngestionProperties  |`bool`  |Indique si les propriétés d’ingestion (balises d’étendue et heure de création) spécifiées lors de l’ingestion dans la table source doivent également s’appliquer à celles de la table dérivée.                 |

> [!NOTE]
>
> * La table source et la table pour laquelle la stratégie de mise à jour est définie **doivent figurer dans la même base de données**.
> * La requête ne peut **pas** inclure des requêtes de bases de données croisées ou entre clusters.
> * La requête peut appeler des fonctions stockées.
> * La requête est automatiquement étendue pour couvrir uniquement les enregistrements nouvellement ingérés.
> * Les mises à jour en cascade sont autorisées (TableA-`[update]`---> TableB-`[update]`---> TableC-`[update]`--->...)
> * Si les stratégies de mise à jour sont définies sur plusieurs tables de manière circulaire, elles sont détectées au moment de l’exécution et la chaîne des mises à jour est coupée (ce qui signifie que les données sont ingérées une seule fois dans chaque table de la chaîne de tables affectées).
> * Lorsque vous faites `Source` référence à la `Query` table dans la partie de la stratégie (ou dans les fonctions référencées par ce dernier), assurez-vous que vous **n’utilisez pas** le nom `TableName` qualifié de la table (autrement dit, utilisez et **non pas** `database("DatabaseName").TableName` et `cluster("ClusterName").database("DatabaseName").TableName`).
> * La requête de mise à jour de la stratégie ne peut pas faire référence à une table avec une [stratégie de sécurité au niveau des lignes](./rowlevelsecuritypolicy.md) activée.
> * Une requête exécutée dans le cadre d’une stratégie de mise à jour ne dispose **pas** d’un accès en lecture aux tables pour lesquelles la [stratégie RestrictedViewAccess](restrictedviewaccesspolicy.md) est activée.
> * `PropagateIngestionProperties`prend effet uniquement dans les opérations d’ingestion. Quand la stratégie de mise à jour est déclenchée dans `.move extents` le `.replace extents` cadre d’une commande ou, cette option n’a **aucun** effet.
> * Quand la stratégie de mise à jour est appelée dans le `.set-or-replace` cadre d’une commande, le comportement par défaut est que les données de la ou des tables dérivées sont également remplacées, comme c’est le cas dans la table source.

## <a name="retention-policy-on-the-source-table"></a>Stratégie de rétention sur la table source

Pour ne pas conserver les données brutes dans la table source, vous pouvez définir une période de suppression réversible de 0 dans la [stratégie de rétention](retentionpolicy.md)de la table source, tout en définissant la stratégie de mise à jour comme transactionnelle.

Voici ce qui se produit :
* Les données sources ne sont pas interroger à partir de la table source.
* Les données sources ne sont pas rendues persistantes dans le stockage durable dans le cadre de l’opération d’ingestion.
* Amélioration des performances de l’opération.
* Réduction des ressources utilisées après la réception, pour les opérations de nettoyage en arrière-plan effectuées sur les [étendues](../management/extents-overview.md) de la table source.

## <a name="failures"></a>Échecs

Dans certains cas, l’ingestion des données dans la table source réussit, mais la stratégie de mise à jour échoue lors de l’ingestion de la table cible.

Les défaillances rencontrées pendant la mise à jour des stratégies peuvent être récupérées à l’aide de la [commande. Show ingestion Failures](../management/ingestionfailures.md), comme suit :
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

Les échecs sont traités comme suit :

* **Stratégie non transactionnelle**: l’échec est ignoré par Kusto. Toute nouvelle tentative est la responsabilité du propriétaire des données.  
* **Stratégie transactionnelle**: l’opération d’ingestion d’origine qui a déclenché la mise à jour échoue également. La table source et la base de données ne seront pas modifiées avec les nouvelles données.
  * Dans le cas où la méthode d' `pull` ingestion est (le service gestion des données de Kusto est impliqué dans le processus d’ingestion), il existe une nouvelle tentative automatisée sur l’ensemble de l’opération d’ingestion, orchestrée par le service gestion des données de Kusto, conformément à la logique suivante :
    * Les nouvelles tentatives sont effectuées jusqu’à atteindre la `DataImporterMaximumRetryPeriod` valeur la plus ancienne entre (valeur `DataImporterMaximumRetryAttempts` par défaut = 2 jours) et (valeur par défaut = 10).
    * Les deux paramètres ci-dessus peuvent être modifiés dans la configuration du service Gestion des données, par KustoOps.
    * La période d’interruption commence à 2 minutes et s’agrandit de façon exponentielle (2 > 4 > 8-> 16... maximum
  * Dans tous les autres cas, toute nouvelle tentative est la responsabilité du propriétaire des données.



## <a name="control-commands"></a>Commandes de contrôle

* Utilisez [. afficher la mise à jour](../management/update-policy.md#show-update-policy) de la stratégie de table de table pour afficher la stratégie de mise à jour actuelle d’une table.
* Utilisez la [mise à jour de la stratégie de table table ALTER TABLE](../management/update-policy.md#alter-update-policy) pour définir la stratégie de mise à jour actuelle d’une table.
* Utilisez la [mise à jour](../management/update-policy.md#alter-merge-table-table-policy-update) de la stratégie de table de table de fusion pour ajouter à la stratégie de mise à jour actuelle d’une table.
* Utilisez [. supprimer la table table mise à jour](../management/update-policy.md#delete-table-table-policy-update) de la stratégie pour ajouter à la stratégie de mise à jour actuelle d’une table.

## <a name="testing-an-update-policys-performance-impact"></a>Test de l’impact sur les performances d’une stratégie de mise à jour

La définition d’une stratégie de mise à jour peut affecter les performances d’un cluster Kusto, car cela affecte toute réception dans la table source. Il est fortement recommandé que la `Query` partie de la stratégie de mise à jour soit optimisée pour s’exécuter correctement.
Vous pouvez tester l’impact supplémentaire des performances d’une stratégie de mise à jour sur une opération d’ingestion en l’appelant sur des extensions spécifiques et déjà existantes, avant de créer ou de modifier la stratégie et/ou une `Query` fonction qu’elle utilise dans sa part.

Cet exemple repose sur les données suivantes :

* Le nom de la table source `Source` (la propriété de la stratégie de `MySourceTable`mise à jour) est.
* La `Query` propriété de la stratégie de mise à jour appelle `MyFunction()`une fonction nommée.

À l’aide des [requêtes. Show](../management/queries.md), vous pouvez évaluer l’utilisation des ressources (processeur, mémoire, etc.) de la requête suivante et/ou plusieurs exécutions de celle-ci.

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```
---
title: Kusto mettre à jour la stratégie pour les données ajoutées à une source-Azure Explorateur de données
description: Cet article décrit la stratégie de mise à jour dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 7eb5adc76c963065940365973aadc5281ff5f553
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803406"
---
# <a name="update-policy-overview"></a>Présentation de la stratégie de mise à jour

La [stratégie de mise à jour](update-policy.md) ordonne à Kusto d’ajouter automatiquement des données à une table cible chaque fois que de nouvelles données sont insérées dans la table source. La requête de la stratégie de mise à jour s’exécute sur les données insérées dans la table source. Par exemple, la stratégie permet à la création d’une table d’être la vue filtrée d’une autre table. La nouvelle table peut avoir un schéma, une stratégie de rétention, etc. différents. 

La stratégie de mise à jour est soumise aux mêmes restrictions et meilleures pratiques que l’ingestion normale. La stratégie s’ajuste à la taille du cluster et fonctionne plus efficacement si les ingestions sont effectuées dans de grands volumes.

:::image type="content" source="images/updatepolicy/update-policy-overview.png" alt-text="Vue d’ensemble de la stratégie de mise à jour dans Azure Explorateur de données":::

> [!NOTE]
> La table source et la table pour laquelle la stratégie de mise à jour est définie doivent figurer dans la même base de données.
> Le schéma de la fonction de stratégie de mise à jour et le schéma de la table cible doivent correspondre aux noms de colonnes, aux types et aux commandes.

## <a name="update-policys-query"></a>Requête de mise à jour de la stratégie

La requête de mise à jour de stratégie est exécutée dans un mode spécial, dans lequel elle est automatiquement étendue pour couvrir uniquement les enregistrements nouvellement ingérés, et vous ne pouvez pas interroger les données déjà ingérées de la table source dans le cadre de cette requête. Toutefois, les données ingérées dans la « limite » des stratégies de mise à jour transactionnelle deviennent disponibles pour une requête dans une transaction unique. Étant donné que la stratégie de mise à jour est définie sur la table de destination, l’ingestion de données dans une table source peut entraîner l’exécution de plusieurs requêtes sur ces données. L’ordre d’exécution de plusieurs stratégies de mise à jour n’est pas défini. 

### <a name="query-limitations"></a>Limitations des requêtes 

* La requête peut appeler des fonctions stockées, mais elle ne peut pas inclure de requêtes entre plusieurs bases de données ou entre clusters. 
* Une requête exécutée dans le cadre d’une stratégie de mise à jour ne dispose pas d’un accès en lecture aux tables pour lesquelles la [stratégie RestrictedViewAccess](restrictedviewaccesspolicy.md) est activée ou lorsqu’une [stratégie de sécurité au niveau des lignes](rowlevelsecuritypolicy.md) est activée.
* Lorsque vous faites référence à la `Source` table dans la `Query` partie de la stratégie ou dans les fonctions référencées par le `Query` composant :
   * N’utilisez pas le nom qualifié de la table. Utilisez plutôt `TableName`. 
   * N’utilisez pas `database("DatabaseName").TableName` ou `cluster("ClusterName").database("DatabaseName").TableName` .

> [!WARNING]
> La définition d’une requête incorrecte dans la stratégie de mise à jour peut empêcher la réception des données dans la table source.

## <a name="the-update-policy-object"></a>Objet de stratégie de mise à jour

Une table peut être associée à zéro, un ou plusieurs objets de stratégie de mise à jour.
Chaque objet de ce type est représenté sous la forme d’un conteneur de propriétés JSON, avec les propriétés suivantes définies.

|Propriété |Type |Description  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |Indique si la stratégie de mise à jour est activée (true) ou désactivée (false)                                                                                                                               |
|Source                        |`string`|Nom de la table qui déclenche l’appel de la stratégie de mise à jour                                                                                                                                 |
|Requête                         |`string`|Une requête Kusto CSL utilisée pour produire les données pour la mise à jour                                                                                                                           |
|IsTransactional               |`bool`  |Indique si la stratégie de mise à jour est transactionnelle ou non (false par défaut). Échec de l’exécution d’une stratégie de mise à jour transactionnelle les résultats de la table source ne sont pas mis à jour avec les nouvelles données   |
|PropagateIngestionProperties  |`bool`  |Indique si les propriétés d’ingestion (balises d’étendue et heure de création) spécifiées lors de l’ingestion dans la table source doivent également s’appliquer à celles de la table dérivée.                 |

> [!NOTE]
> Les mises à jour en cascade sont autorisées (→ → → `TableA` `TableB` .. `TableC` .).
>
> Toutefois, si les stratégies de mise à jour sont définies sur plusieurs tables de manière circulaire, la chaîne des mises à jour est coupée. Ce problème est détecté au moment de l’exécution. Les données ne sont ingérées qu’une seule fois pour chaque table de la chaîne de tables affectées.

## <a name="update-policy-commands"></a>Mettre à jour les commandes de stratégie

Les commandes de contrôle de la stratégie de mise à jour sont les suivantes :

* [. afficher la table la mise à jour de la stratégie *TableName* ](update-policy.md#show-update-policy) affiche la stratégie de mise à jour actuelle d’une table.
* [. Alter table *TableName* Policy Update](update-policy.md#alter-update-policy) définit la stratégie de mise à jour actuelle d’une table.
* [. Alter-Merge table la mise à jour de la stratégie *TableName* ](update-policy.md#alter-merge-table-tablename-policy-update) ajoute à la stratégie de mise à jour actuelle d’une table.
* [. supprimer la table *nom_table* la mise à jour](update-policy.md#delete-table-tablename-policy-update) de la stratégie ajoute à la stratégie de mise à jour actuelle d’une table.

## <a name="update-policy-is-initiated-following-ingestion"></a>La stratégie de mise à jour est lancée après réception

Les stratégies de mise à jour prennent effet lorsque les données sont ingérées ou déplacées vers (les extensions sont créées dans) une table source définie à l’aide de l’une des commandes suivantes :

* [. ingestion (pull)](../management/data-ingestion/ingest-from-storage.md)
* [. ingestion (Inline)](../management/data-ingestion/ingest-inline.md)
* [. Set |. Append |. set-or-Append |. set-or-Replace](../management/data-ingestion/ingest-from-query.md)
  * Quand la stratégie de mise à jour est appelée dans le cadre d’une `.set-or-replace` commande, le comportement par défaut est que les données de la ou des tables dérivées sont remplacées de la même façon que dans la table source.
* [.move extents](../management/extents-commands.md#move-extents)
* [.replace extents](../management/extents-commands.md#replace-extents)
  * La `PropagateIngestionProperties` commande prend effet uniquement dans les opérations d’ingestion. Quand la stratégie de mise à jour est déclenchée dans le cadre d’une `.move extents` `.replace extents` commande ou, cette option n’a aucun effet.

## <a name="regular-ingestion-using-update-policy"></a>Ingestion normale à l’aide d’une stratégie de mise à jour

La stratégie de mise à jour se comporte comme une ingestion normale quand les conditions suivantes sont remplies :

* La table source est une table de trace à haut débit avec des données intéressantes mises en forme en tant que colonne de texte libre. 
* La table cible sur laquelle la stratégie de mise à jour est définie accepte uniquement des lignes de suivi spécifiques.
* La table possède un schéma bien structuré qui est une transformation des données de texte libre d’origine créées par l' [opérateur d’analyse](../query/parseoperator.md).

## <a name="zero-retention-on-source-table"></a>Rétention de zéro dans la table source

Parfois, les données sont ingérées dans une table source uniquement sous la forme d’un pas à pas dans la table cible, et vous ne souhaitez pas conserver les données brutes dans la table source. Définissez une période de suppression réversible de 0 dans la [stratégie de rétention](retentionpolicy.md)de la table source, puis définissez la stratégie de mise à jour en tant que transaction. Dans ce cas : 

* Les données sources ne sont pas interroger à partir de la table source. 
* Les données sources ne sont pas conservées dans un stockage durable dans le cadre de l’opération d’ingestion. 
* Les performances opérationnelles seront améliorées. 
* Les ressources postérieures à l’ingestion pour les opérations de nettoyage en arrière-plan sont réduites. Ces opérations sont effectuées sur les [étendues](../management/extents-overview.md) de la table source.

## <a name="performance-impact"></a>Impact sur les performances

Les stratégies de mise à jour peuvent affecter les performances d’un cluster Kusto. La stratégie de mise à jour affecte toute ingestion dans la table source. La réception d’un certain nombre d’étendues de données est multipliée par le nombre de tables cibles. Par conséquent, il est important que la `Query` partie de la stratégie de mise à jour soit optimisée pour fonctionner correctement. Vous pouvez tester l’impact supplémentaire sur les performances d’une stratégie de mise à jour sur une opération d’ingestion. Appelez la stratégie sur des extensions spécifiques et déjà existantes, avant de créer ou de modifier la stratégie ou la fonction qu’elle utilise dans sa `Query` partie.

### <a name="evaluate-resource-usage"></a>Évaluer l’utilisation des ressources

Utilisez les [requêtes. Show](../management/queries.md)pour évaluer l’utilisation des ressources (processeur, mémoire, etc.) dans le scénario suivant :
* Le nom de la table source (la `Source` propriété de la stratégie de mise à jour) est `MySourceTable` .
* La `Query` propriété de la stratégie de mise à jour appelle une fonction nommée `MyFunction()` .

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```

## <a name="failures"></a>Échecs

Par défaut, l’exécution de la stratégie de mise à jour n’affecte pas l’ingestion des données dans la table source. Toutefois, si la stratégie de mise à jour est définie comme `IsTransactional` suit : true, l’échec de l’exécution de la stratégie force l’ingestion des données dans la table source à échouer. Dans certains cas, l’ingestion des données dans la table source réussit, mais la stratégie de mise à jour échoue lors de l’ingestion de la table cible.

Les défaillances qui se produisent pendant la mise à jour des stratégies peuvent être récupérées à l’aide de la [commande. afficher les échecs](../management/ingestionfailures.md)d’ingestion.
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

### <a name="treatment-of-failures"></a>Traitement des défaillances

#### <a name="non-transactional-policy"></a>Stratégie non transactionnelle 

L’échec est ignoré par Kusto. Toute nouvelle tentative est la responsabilité du propriétaire du processus d’ingestion de données.  

#### <a name="transactional-policy"></a>Stratégie transactionnelle

L’opération d’ingestion d’origine qui a déclenché la mise à jour échoue également. La table source et la base de données ne seront pas modifiées avec les nouvelles données.
Si la méthode d’ingestion est `pull` (le service de gestion des données de Kusto est impliqué dans le processus d’ingestion), il existe une nouvelle tentative automatisée sur l’ensemble de l’opération d’ingestion, orchestrée par le service gestion des données de Kusto, conformément à la logique suivante :
* Les nouvelles tentatives sont effectuées jusqu’à ce que la valeur la plus ancienne comprise entre `DataImporterMaximumRetryPeriod` (valeur par défaut = 2 jours) et `DataImporterMaximumRetryAttempts` (valeur par défaut = 10) soit atteinte.
* Les deux paramètres ci-dessus peuvent être modifiés dans la configuration du service Gestion des données.
* La période d’interruption commence à 2 minutes et s’agrandit de façon exponentielle (2 > 4 > 8-> 16... maximum

Dans tous les autres cas, toute nouvelle tentative est la responsabilité du propriétaire des données.

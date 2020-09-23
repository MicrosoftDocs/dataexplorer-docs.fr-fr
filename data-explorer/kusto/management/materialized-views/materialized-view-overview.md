---
title: Vues matérialisées-Azure Explorateur de données
description: Cet article décrit les vues matérialisées dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: c1f96fa2fcfd8989936f31ac0c3b608dabdd6830
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057140"
---
# <a name="materialized-views-preview"></a>Vues matérialisées (préversion)

Les [vues matérialisées](../../query/materialized-view-function.md) exposent une requête d' *agrégation* sur une table source. Les vues matérialisées retournent toujours un résultat à jour de la requête d’agrégation (toujours actualisée). L' [interrogation d’une vue matérialisée](#materialized-views-queries), qui est un processus de nettoyage des données à usage unique, est plus performante que l’exécution de l’agrégation directement sur la table source, qui est effectuée à chaque requête.

> [!NOTE]
> Les vues matérialisées présentent des [limitations](#limitations-on-creating-materialized-views)et ne sont pas assurées de fonctionner correctement pour tous les scénarios. Passez en revue les [considérations relatives aux performances](#performance-considerations) avant d’utiliser la fonctionnalité.

Utilisez les commandes suivantes pour gérer les vues matérialisées :
* [. créer une vue matérialisée](materialized-view-create.md)
* [. ALTER MATERIALIZED-VIEW](materialized-view-alter.md)
* [. Drop-vue](materialized-view-drop.md)
* [. Disable |. activer la vue matérialisée](materialized-view-enable-disable.md)
* [. afficher les commandes matérialisées-views](materialized-view-show-commands.md)

## <a name="why-use-materialized-views"></a>Pourquoi utiliser des vues matérialisées ?

En investissant des ressources (stockage de données, cycles d’UC en arrière-plan) pour les vues matérialisées des agrégations couramment utilisées, vous bénéficiez des avantages suivants :

* **Amélioration des performances :** L’interrogation d’une vue matérialisée est généralement plus efficace que l’interrogation de la table source pour la ou les mêmes fonctions d’agrégation.

* **Actualisation :** Une requête de vue matérialisée retourne toujours les résultats les plus récents, indépendamment du moment où la matérialisation a eu lieu. La requête combine la partie matérialisée de la vue avec les enregistrements de la table source, qui n’ont pas encore été matérialisés (la `delta` partie), fournissant toujours les résultats les plus récents.

* **Réduction des coûts :** l' [interrogation d’une vue matérialisée](#materialized-views-queries) consomme moins de ressources du cluster que l’agrégation sur l’intégralité de la table source. La stratégie de rétention de la table source peut être réduite si seule l’agrégation est requise. Cette configuration réduit les coûts de cache à chaud pour la table source.

## <a name="how-materialized-views-work"></a>Fonctionnement des vues matérialisées

Une vue matérialisée est constituée de deux composants :

* Une partie *matérialisée* : table de Explorateur de données Azure contenant des enregistrements agrégés de la table source qui ont déjà été traités.  Cette table contient toujours un enregistrement unique par combinaison Group-by de l’agrégation.
* *Delta* : enregistrements nouvellement ingérés de la table source qui n’ont pas encore été traités.

L’interrogation de la vue matérialisée associe la partie matérialisée à la partie Delta, ce qui fournit un résultat à jour de la requête d’agrégation. Le processus de matérialisation hors connexion ingère les nouveaux enregistrements du *Delta* vers la table matérialisée et remplace les enregistrements existants. Le remplacement est effectué en reconstruisant des étendues qui contiennent des enregistrements à remplacer. Si les enregistrements dans le *Delta* se croisent constamment avec tous les partitions de données de la partie *matérialisée* , chaque cycle de matérialisation nécessitera la reconstruction de l’intégralité de la partie *matérialisée* et peut ne pas suivre le rythme. Le taux d’ingestion sera supérieur au taux de matérialisation. Dans ce cas, la vue devient non intègre et le *Delta* augmente en permanence.

## <a name="create-a-materialized-view"></a>Créer une vue matérialisée

Il existe deux façons de créer une vue matérialisée, notée par l’option *Backfill* dans la [commande CREATE](materialized-view-create.md):
 * **Créez en fonction des enregistrements existants dans la table source :** 
      * La création peut prendre un certain temps, en fonction du nombre d’enregistrements dans la table source. La vue ne sera pas disponible pour les requêtes jusqu’à ce qu’elle soit terminée.
      * Quand vous utilisez cette option, la commande CREATE doit être `async` et l’exécution peut être surveillée à l’aide de la commande [. Show Operations](../operations.md#show-operations) .
    
      > [!IMPORTANT]
      > * L’utilisation de l’option de renvoi n’est pas prise en charge pour les données dans le cache à froid. Augmentez la période de cache à chaud, si nécessaire, pour la création de la vue. Cela peut nécessiter une montée en puissance parallèle.    
      > * L’utilisation de l’option de renvoi peut prendre beaucoup de temps pour les tables sources volumineuses. Si ce processus échoue de façon transitoire lors de son exécution, il ne sera pas automatiquement retenté et une nouvelle exécution de la commande Create est nécessaire.
    
* **Créez la vue matérialisée à partir de maintenant :** 
    * La vue matérialisée est créée vide et n’inclut que les enregistrements ingérés après la création de la vue. La création de ce type est immédiatement retournée, ne nécessite pas `async` , et la vue est immédiatement disponible pour la requête.

### <a name="limitations-on-creating-materialized-views"></a>Limitations relatives à la création de vues matérialisées

* Impossible de créer une vue matérialisée :
    * Au-dessus d’une autre vue matérialisée.
    * Sur les [bases de données de suivi](../../../follower.md). Les bases de données de suivi sont en lecture seule et les vues matérialisées nécessitent des opérations d’écriture.  Les vues matérialisées définies sur les bases de données de leader peuvent être interrogées à partir de leurs abonnés, comme n’importe quelle autre table dans le responsable. 
* La table source d’une vue matérialisée :
    * Doit être une table qui est ingérée directement, soit à l’aide de l’une des [méthodes](../../../ingest-data-overview.md#ingestion-methods-and-tools)d’ingestion, à l’aide d’une [stratégie de mise à jour](../updatepolicy.md), soit en ingestion [des commandes de requête](../data-ingestion/ingest-from-query.md).
        * En particulier, l’utilisation d' [étendues de déplacement](../move-extents.md) d’autres tables dans la table source de la vue matérialisée n’est pas prise en charge. Les étendues de déplacement peuvent échouer avec l’erreur suivante : `Cannot drop/move extents from/to table 'TableName' since Materialized View 'ViewName' is currently processing some of these extents` . 
    * La [stratégie IngestionTime](../ingestiontimepolicy.md) doit être activée (la valeur par défaut est activé).
    * Ne peut pas être activé pour l’ingestion de diffusion en continu.
    * Il ne peut pas s’agir d’une table restreinte ou d’une table pour laquelle la sécurité au niveau des lignes est activée.
* Les [fonctions de curseur](../databasecursor.md#cursor-functions) ne peuvent pas être utilisées sur des vues matérialisées.
* L’exportation continue à partir d’une vue matérialisée n’est pas prise en charge.

### <a name="materialized-view-retention-policy"></a>Stratégie de rétention de vue matérialisée

La vue matérialisée dérive par défaut la stratégie de rétention de la base de données. La stratégie peut être modifiée à l’aide de [commandes de contrôle](../retentionpolicy.md).
   
   * La stratégie de rétention de la vue matérialisée n’est pas liée à la stratégie de rétention de la table source.
   * Si les enregistrements de la table source ne sont pas utilisés par ailleurs, la stratégie de rétention de la table source peut être déplacée au minimum. La vue matérialisée stocke toujours les données en fonction de la stratégie de rétention définie sur la vue. 
   * Bien que les vues matérialisées soient en mode aperçu, il est recommandé d’autoriser au moins sept jours et la capacité de récupération définie sur true. Ce paramètre permet une récupération rapide des erreurs et à des fins de diagnostic.
    
> [!NOTE]
> Aucune stratégie de rétention de la table source n’est actuellement prise en charge.

## <a name="materialized-views-queries"></a>Requêtes de vues matérialisées

Le principal moyen d’interroger une vue matérialisée est son nom, par exemple l’interrogation d’une référence de table. Lorsque la vue matérialisée est interrogée, elle combine la partie matérialisée de la vue avec les enregistrements de la table source qui n’ont pas encore été matérialisés. L’interrogation de la vue matérialisée renverra toujours les résultats les plus récents, en fonction de tous les enregistrements ingérés dans la table source. Pour plus d’informations sur la répartition des parties de vue matérialisée, consultez [fonctionnement des vues matérialisées](#how-materialized-views-work). 

Vous pouvez également interroger la vue à l’aide de la [fonction materialized_view ()](../../query/materialized-view-function.md). Cette option prend en charge l’interrogation de la partie matérialisée de la vue, tout en spécifiant la latence maximale que l’utilisateur est disposé à tolérer. Cette option n’est pas garantie de retourner les enregistrements les plus récents, mais elle doit toujours être plus performante que l’interrogation de la vue entière. Cette fonction est utile pour les scénarios dans lesquels vous êtes disposé à sacrifier une actualisation des performances, par exemple pour les tableaux de bord de télémétrie.

La vue peut participer à des requêtes entre des clusters ou des bases de données croisées, mais elles ne sont pas incluses dans les recherches ou les unions génériques.

### <a name="query-use-cases"></a>Cas d’usage de requête

Voici quelques scénarios courants qui peuvent être traités à l’aide d’une vue matérialisée :

* Interroger le dernier enregistrement par entité à l’aide [de ARG_MAX () (fonction d’agrégation)](../../query/arg-max-aggfunction.md).
* Dédupliquer les enregistrements d’une table à l’aide de [Any () (fonction d’agrégation)](../../query/any-aggfunction.md).
* Réduisez la résolution des données en calculant des statistiques périodiques sur les données brutes. Utilisez différentes [fonctions d’agrégation](materialized-view-create.md#supported-aggregation-functions) par période de temps.
    * Par exemple, utilisez `T | summarize dcount(User) by bin(Timestamp, 1d)` pour conserver un instantané à jour d’utilisateurs distincts par jour.

### <a name="query-examples"></a>Exemples de requêtes

1. Interrogez la vue entière. Les enregistrements les plus récents de la table source sont inclus :
    
    <!-- csl -->
    ```kusto
    ViewName
    ```

1. Interrogez la partie matérialisée de la vue uniquement, quel que soit le moment où elle a été matérialisée pour la dernière fois. 

    <!-- csl -->
    ```kusto
    materialized_view("ViewName")
    ```
    
## <a name="performance-considerations"></a>Considérations relatives aux performances

Les contributeurs principaux qui peuvent avoir un impact sur l’intégrité de la vue matérialisée sont les suivants :

* **Ressources de cluster :** Comme tout autre processus en cours d’exécution sur le cluster, les vues matérialisées consomment des ressources (UC, mémoire) du cluster. Si le cluster est surchargé, l’ajout de vues matérialisées peut entraîner une dégradation des performances du cluster. Surveiller l’intégrité de votre cluster à l’aide des [mesures d’intégrité du cluster](../../../using-metrics.md#cluster-metrics). La mise à l' [échelle automatique optimisée](../../../manage-cluster-horizontal-scaling.md#optimized-autoscale) ne prend actuellement pas en compte l’intégrité des vues matérialisées dans le cadre des règles de mise à l’échelle automatique.

* **Chevauchement avec les données matérialisées :** Lors de la matérialisation, tous les nouveaux enregistrements ingérés dans la table source depuis la dernière matérialisation (le delta) sont traités et matérialisés dans la vue. Plus l’intersection entre les nouveaux enregistrements et les enregistrements déjà matérialisés est élevée, moins les performances de la vue matérialisée sont bonnes. Une vue matérialisée fonctionnera mieux si le nombre d’enregistrements en cours de mise à jour (par exemple, dans `arg_max` la vue) est un petit sous-ensemble de la table source. Si la totalité ou la plupart des enregistrements de vue matérialisée doivent être mis à jour dans chaque cycle de matérialisation, la vue matérialisée ne s’exécutera pas correctement. Utilisez les [Extensions Rebuild Metrics](../../../using-metrics.md#materialized-view-metrics) pour identifier cette situation.

* **Taux** d’ingestion : Il n’existe aucune limite codée en dur sur le volume de données ou le taux d’ingestion dans la table source de la vue matérialisée. Toutefois, le taux d’ingestion recommandé pour les vues matérialisées n’est pas supérieur à 1 Go/s. Des taux d’ingestion plus élevés peuvent toujours fonctionner correctement. Les performances dépendent de la taille du cluster, des ressources disponibles et de la quantité d’intersection avec les données existantes.

* **Nombre de vues matérialisées dans le cluster :** Les considérations ci-dessus s’appliquent à chaque vue matérialisée définie dans le cluster. Chaque vue utilise ses propres ressources, et de nombreuses vues sont en concurrence sur les ressources disponibles. Il n’existe aucune limite codée en dur pour le nombre de vues matérialisées dans un cluster. Toutefois, la recommandation générale consiste à ne pas avoir plus de 10 vues matérialisées sur un cluster. La [stratégie de capacité](../capacitypolicy.md#materialized-views-capacity-policy) peut être ajustée si plusieurs vues matérialisées sont définies dans le cluster.

* **Définition de la vue matérialisée**: la définition de la vue matérialisée doit être définie conformément aux meilleures pratiques de requête pour optimiser les performances des requêtes. Pour plus d’informations, consultez conseils sur les performances de la [commande CREATE](materialized-view-create.md#performance-tips).

## <a name="materialized-views-monitoring"></a>Analyse de vues matérialisées

Surveillez l’intégrité de la vue matérialisée de l’une des manières suivantes :

* Surveiller les [métriques de vue matérialisée](../../../using-metrics.md#materialized-view-metrics) dans le portail Azure.
* Surveillez la `IsHealthy` propriété retournée à partir de la [vue matérialisée-View](materialized-view-show-commands.md#show-materialized-view).
* Vérifiez les échecs à l’aide de l’option [afficher les échecs de vue matérialisée](materialized-view-show-commands.md#show-materialized-view-failures).

> [!NOTE]
> La matérialisation n’ignore jamais les données, même en cas de défaillances constantes. La vue est toujours garantie pour retourner l’instantané le plus à jour de la requête, en fonction de tous les enregistrements de la table source. Les défaillances constantes dégradent considérablement les performances des requêtes, mais ne provoquent pas de résultats incorrects dans les requêtes de vue.

### <a name="track-resource-consumption"></a>Suivre la consommation des ressources

**Vue matérialisée consommation des ressources :** les ressources consommées par le processus de matérialisation des vues matérialisées peuvent être suivies à l’aide de la commande [. Show Commands-and-Queries](../commands-and-queries.md#show-commands-and-queries) . Filtrez les enregistrements pour une vue spécifique à l’aide de la commande suivante (remplacez `DatabaseName` et `ViewName` ) :

<!-- csl -->
```
.show commands-and-queries 
| where Database  == "DatabaseName" and ClientActivityId startswith "DN.MaterializedViews;ViewName;"
```

### <a name="troubleshooting-unhealthy-materialized-views"></a>Résolution des problèmes de vues matérialisées défectueuses

La `MaterializedViewHealth` métrique indique si une vue matérialisée est intègre. Une vue matérialisée peut devenir défectueuse pour une ou plusieurs des raisons suivantes :
* Le processus de matérialisation échoue.
* Le cluster ne dispose pas d’une capacité suffisante pour matérialiser toutes les données entrantes dans le temps. Il n’y a pas d’échecs d’exécution. Toutefois, la vue reste non intègre, car elle est en retard et ne peut pas suivre le taux d’ingestion.

Avant qu’une vue matérialisée ne devienne défectueuse, son âge, noté par la `MaterializedViewAgeMinutes` mesure, augmente progressivement.

### <a name="troubleshooting-examples"></a>Exemples de dépannage

Les exemples suivants peuvent vous aider à diagnostiquer et à corriger les affichages non intègres :

* **Échec :** La table source a été modifiée ou supprimée, la vue n’a pas été définie sur `autoUpdateSchema` , ou la modification de la table source n’est pas prise en charge pour les mises à jour automatiques. <br>
   **Diagnostic :**  Une `MaterializedViewResult` métrique est déclenchée et la `Result` dimension a la valeur `SourceTableSchemaChange` / `SourceTableNotFound` .

* **Échec :** Le processus de matérialisation échoue en raison de ressources de cluster insuffisantes et les limites de requête sont atteintes. <br>
  **Diagnostic :** `MaterializedViewResult` la `Result` dimension de mesure a la valeur `InsufficientResources` . Azure Explorateur de données essaiera de récupérer automatiquement à partir de cet État. cette erreur peut donc être temporaire. Toutefois, si la vue est défectueuse et que cette erreur est émise en permanence, il est possible que la configuration du cluster actuel ne soit pas en mesure de suivre la vitesse d’ingestion et que le cluster doive être mis à l’échelle.

* **Échec :** Le processus de matérialisation échoue en raison d’une raison quelconque (inconnue). <br> 
   **Diagnostic**: `MaterializedViewResult` la métrique `Result` sera `UnknownError` . Si ce problème se produit fréquemment, ouvrez un ticket de support pour l’équipe Azure Explorateur de données pour approfondir les recherches.

En cas d’échec de la matérialisation, la `MaterializedViewResult` métrique est déclenchée à chaque exécution réussie, avec `Result` = `Success` . Une vue matérialisée peut être défectueuse, malgré les exécutions réussies, si elle est en retard ( `Age` est au-dessus du seuil). Cette situation peut se produire dans les circonstances suivantes :
   * La matérialisation est lente, car il y a trop d’étendues à reconstruire dans chaque cycle de matérialisation. Pour en savoir plus sur les raisons pour lesquelles les extensions reconstruites ont un impact sur les performances de la vue, consultez [fonctionnement des vues matérialisées](#how-materialized-views-work). 
   * Si chaque cycle de matérialisation doit être reconstruit près de 100% des étendues de la vue, il se peut que la vue ne continue pas et devienne défectueuse. Le nombre d’extensions reconstruites dans chaque cycle est fourni dans la `MaterializedViewExtentsRebuild` mesure. L’amélioration de l’accès concurrentiel reconstruit dans la [stratégie de capacité vue matérialisée](../capacitypolicy.md#materialized-views-capacity-policy) peut également être utile dans ce cas. 
   * Il existe des vues matérialisées supplémentaires dans le cluster, et le cluster ne dispose pas d’une capacité suffisante pour exécuter toutes les vues. Consultez [stratégie de capacité de vue matérialisée](../capacitypolicy.md#materialized-views-capacity-policy) pour modifier les paramètres par défaut pour le nombre de vues matérialisées exécutées simultanément.

## <a name="next-steps"></a>Étapes suivantes

* [. créer une vue matérialisée](materialized-view-create.md)
* [. ALTER MATERIALIZED-VIEW](materialized-view-alter.md)
* [Les vues matérialisées affichent les commandes](materialized-view-show-commands.md)

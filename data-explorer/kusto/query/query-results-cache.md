---
title: Cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit la fonctionnalité de cache des résultats de requête dans Azure Explorateur de données.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3c1e1eec2bb0ad4d856ca690039dea9aedd78e8f
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943086"
---
# <a name="query-results-cache"></a>Cache des résultats de requête

Kusto comprend un cache de résultats de requête. Vous pouvez indiquer la volonté d’obtenir des résultats mis en cache lors de l’émission d’une requête. Si les résultats de votre requête peuvent être retournés par le cache, vous obtiendrez de meilleures performances de requête et une consommation de ressources inférieure au détriment d’une certaine « obsolescence » dans les résultats.

## <a name="indicating-willingness-to-use-the-cache"></a>Indication de la volonté d’utiliser le cache

Définissez l' `query_results_cache_max_age` option dans le cadre de la requête pour indiquer la volonté d’utiliser le cache des résultats de la requête. Vous pouvez définir cette option dans le texte de la requête ou comme propriété de requête du client. Par exemple :

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

La valeur de l’option est un `timespan` qui indique l’âge maximal du cache des résultats, mesuré à partir de l’heure de début de la requête. Au-delà de la période définie, l’entrée du cache est obsolète et ne sera pas réutilisée. L’affectation de la valeur 0 équivaut à ne pas définir l’option.

## <a name="how-compatibility-between-queries-is-determined"></a>Détermination de la compatibilité entre les requêtes

Le query_results_cache renvoie des résultats uniquement pour les requêtes considérées comme « identiques » à une requête mise en cache antérieure. Deux requêtes sont considérées comme identiques si toutes les conditions suivantes sont remplies :

* Les deux requêtes ont la même représentation (en tant que chaînes UTF-8).

* Les deux requêtes sont faites à la même base de données.

* Les deux requêtes partagent les mêmes [Propriétés de demande client](../api/netfx/request-properties.md). Les propriétés suivantes sont ignorées à des fins de mise en cache :
   * [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)
   * [Application](../api/netfx/request-properties.md#the-application-x-ms-app-named-property)
   * [Utilisateur](../api/netfx/request-properties.md#the-user-x-ms-user-named-property)

## <a name="if-the-query-results-cache-cant-find-a-valid-cache-entry"></a>Si le cache des résultats de la requête ne peut pas trouver une entrée de cache valide

Si un résultat mis en cache satisfaisant aux contraintes de temps est introuvable ou qu’il n’existe pas de résultat mis en cache à partir d’une requête « identique » dans le cache, la requête est exécutée et ses résultats sont mis en cache, à condition que : 

* L’exécution de la requête se termine correctement, et
* La taille des résultats de la requête ne dépasse pas 16 Mo.

## <a name="how-the-service-indicates-that-the-query-results-are-being-served-from-the-cache"></a>Comment le service indique que les résultats de la requête sont servis à partir du cache

Lors de la réponse à une requête, Kusto envoie une table de réponse [ExtendedProperties](../api/rest/response.md) supplémentaire qui comprend une `Key` colonne et une `Value` colonne.
Les résultats de requête mis en cache comporteront une ligne supplémentaire ajoutée à cette table :
* La colonne de la ligne `Key` contient la chaîne`ServerCache`
* La colonne de la ligne `Value` contient un conteneur de propriétés avec deux champs :
   * `OriginalClientRequestId`-Spécifie le [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)de la demande d’origine.
   * `OriginalStartedOn`-Spécifie l’heure de début d’exécution de la demande d’origine.

## <a name="distribution"></a>Distribution

Le cache n’est pas partagé par les nœuds de cluster. Chaque nœud possède un cache dédié dans son propre stockage privé. Si deux requêtes identiques débarquent sur des nœuds différents, la requête est exécutée et mise en cache sur les deux nœuds. Ce processus peut se produire si une [cohérence faible](../concepts/queryconsistency.md) est utilisée.

## <a name="management"></a>Gestion

Les commandes de gestion et d’observation suivantes sont prises en charge :

* [Afficher le cache](../management/show-query-results-cache-command.md).
* [Effacer le cache](../management/clear-query-results-cache-command.md).

## <a name="capacity"></a>Capacité

La capacité du cache est actuellement fixée à 1 Go par nœud de cluster.
La stratégie d’éviction est LRU.

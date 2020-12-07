---
title: Cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit la fonctionnalité de cache des résultats de requête dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 24ab3cb3e423e3ab6b77f09f2c216feb07ae0d0f
ms.sourcegitcommit: f134d51e52504d3ca722bdf6d33baee05118173a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563306"
---
# <a name="query-results-cache"></a>Cache des résultats de requête

Kusto comprend un cache de résultats de requête. Vous pouvez choisir d’obtenir des résultats mis en cache lors de l’émission d’une requête. Vous obtiendrez de meilleures performances de requête et une consommation de ressources inférieure si les résultats de votre requête peuvent être retournés par le cache. Toutefois, cette performance est due à un « obsolescence » dans les résultats.

## <a name="use-the-cache"></a>Utiliser le cache

Définissez l' `query_results_cache_max_age` option dans le cadre de la requête pour utiliser le cache des résultats de la requête. Vous pouvez définir cette option dans le texte de la requête ou comme propriété de requête du client. Par exemple :

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

La valeur de l’option est un `timespan` qui indique l’âge maximal du cache des résultats, mesuré à partir de l’heure de début de la requête. Au-delà de la période définie, l’entrée du cache est obsolète et ne sera pas réutilisée. L’affectation de la valeur 0 équivaut à ne pas définir l’option.

## <a name="compatibility-between-queries"></a>Compatibilité entre les requêtes

### <a name="identical-queries"></a>Requêtes identiques

Le cache des résultats de la requête retourne des résultats uniquement pour les requêtes considérées comme « identiques » à une requête mise en cache antérieure. Deux requêtes sont considérées comme identiques si toutes les conditions suivantes sont remplies :

* Les deux requêtes ont la même représentation (en tant que chaînes UTF-8).
* Les deux requêtes sont faites à la même base de données.
* Les deux requêtes partagent les mêmes [Propriétés de demande client](../api/netfx/request-properties.md). Les propriétés suivantes sont ignorées à des fins de mise en cache :
   * [ClientRequestId](../api/netfx/request-properties.md#clientrequestid-x-ms-client-request-id)
   * [Application](../api/netfx/request-properties.md#application-x-ms-app)
   * [Utilisateur](../api/netfx/request-properties.md#user-x-ms-user)

### <a name="incompatible-queries"></a>Requêtes incompatibles

Les résultats de la requête ne seront pas mis en cache si l’une des conditions suivantes est vraie :
 
* La requête fait référence à une table pour laquelle la stratégie [RestrictedViewAccess](../management/restrictedviewaccesspolicy.md) est activée.
* La requête fait référence à une table pour laquelle la stratégie [RowLevelSecurity](../management/rowlevelsecuritypolicy.md) est activée.
* La requête utilise l’une des fonctions suivantes :
    * [current_principal](current-principalfunction.md)
    * [current_principal_details](current-principal-detailsfunction.md)
    * [current_principal_is_member_of](current-principal-ismemberoffunction.md)
* La requête accède à une [table externe](schema-entities/externaltables.md) ou à des [données externes](externaldata-operator.md).
* La requête utilise l’opérateur de [plug-in Evaluate](evaluateoperator.md) .

## <a name="no-valid-cache-entry"></a>Aucune entrée de cache valide

Si un résultat mis en cache satisfaisant aux contraintes de temps est introuvable ou qu’il n’existe pas de résultat mis en cache à partir d’une requête « identique » dans le cache, la requête est exécutée et ses résultats sont mis en cache, à condition que : 

* L’exécution de la requête se termine correctement, et
* La taille des résultats de la requête ne dépasse pas 16 Mo.

## <a name="results-from-the-cache"></a>Résultats du cache

Comment le service indique-t-il que les résultats de la requête sont servis à partir du cache ?
Lors de la réponse à une requête, Kusto envoie une table de réponse [ExtendedProperties](../api/rest/response.md) supplémentaire qui comprend une `Key` colonne et une `Value` colonne.
Les résultats de requête mis en cache comporteront une ligne supplémentaire ajoutée à cette table :
* La colonne de la ligne `Key` contient la chaîne `ServerCache`
* La colonne de la ligne `Value` contient un conteneur de propriétés avec deux champs :
   * `OriginalClientRequestId` -Spécifie le [ClientRequestId](../api/netfx/request-properties.md#clientrequestid-x-ms-client-request-id)de la demande d’origine.
   * `OriginalStartedOn` -Spécifie l’heure de début d’exécution de la demande d’origine.

## <a name="distribution"></a>Distribution

Le cache n’est pas partagé par les nœuds de cluster. Chaque nœud possède un cache dédié dans son propre stockage privé. Si deux requêtes identiques débarquent sur des nœuds différents, la requête est exécutée et mise en cache sur les deux nœuds. Ce processus peut se produire si une [cohérence faible](../concepts/queryconsistency.md) est utilisée.

## <a name="management"></a>Gestion

Les commandes de gestion et d’observation suivantes sont prises en charge :

* [Afficher le cache](../management/show-query-results-cache-command.md).
* [Effacer le cache](../management/clear-query-results-cache-command.md).

## <a name="capacity"></a>Capacité

La capacité du cache est actuellement fixée à 1 Go par nœud de cluster.
La stratégie d’éviction est LRU.

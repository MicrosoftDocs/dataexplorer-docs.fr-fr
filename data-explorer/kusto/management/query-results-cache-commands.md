---
title: Commandes de cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit le cache des résultats de la requête dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 519560f0b6bb98651dc4a8b6749935ec9843eb9c
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512568"
---
# <a name="query-results-cache-commands"></a>Commandes de cache des résultats de requête

Le cache des résultats de la requête est un cache dédié au stockage des résultats de la requête. Pour plus d’informations, consultez [cache des résultats](../query/query-results-cache.md)de la requête.

**Commandes de cache des résultats de requête**

Kusto fournit deux commandes pour la gestion et l’observation du cache :

* [`Show cache`](show-query-results-cache-command.md): Utilisez cette commande pour afficher les statistiques exposées par le cache des résultats.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Utilisez cette commande pour effacer les résultats mis en cache.

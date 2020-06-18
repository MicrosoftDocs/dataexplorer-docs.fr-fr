---
title: Cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit le cache des résultats de la requête dans Azure Explorateur de données.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 630266fdaaaac51037a99a6a5243db58a232ae48
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943078"
---
# <a name="query-results-cache"></a>Cache des résultats de requête

Le cache des résultats de la requête est un cache dédié au stockage des résultats de la requête. Pour plus d’informations, consultez [cache des résultats](../query/query-results-cache.md)de la requête.

**Commandes de cache des résultats de requête**

Kusto fournit deux commandes pour la gestion et l’observation du cache :

* [`Show cache`](show-query-results-cache-command.md): Utilisez cette commande pour afficher les statistiques exposées par le cache des résultats.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Utilisez cette commande pour effacer les résultats mis en cache.

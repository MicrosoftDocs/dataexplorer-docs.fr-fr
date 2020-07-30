---
title: Cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit le cache des résultats de la requête dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: fa2bf2f6d24162c5bdb1c851ef7d74e4eb39489f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349905"
---
# <a name="query-results-cache"></a>Cache des résultats de requête

Le cache des résultats de la requête est un cache dédié au stockage des résultats de la requête. Pour plus d’informations, consultez [cache des résultats](../query/query-results-cache.md)de la requête.

**Commandes de cache des résultats de requête**

Kusto fournit deux commandes pour la gestion et l’observation du cache :

* [`Show cache`](show-query-results-cache-command.md): Utilisez cette commande pour afficher les statistiques exposées par le cache des résultats.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Utilisez cette commande pour effacer les résultats mis en cache.

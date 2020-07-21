---
title: Afficher le cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit. afficher le cache des résultats de la requête dans Azure Explorateur de données.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 84805cae07049af4ffa8a2fdb82e637261140f8f
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565435"
---
# <a name="show-database-cache-query_results"></a>. afficher le cache de base de données query_results

Retourne une table qui affiche des statistiques relatives au [cache des résultats](../query/query-results-cache.md) de la requête effectuée sur la base de données de contexte.

**Syntaxe**

`.show database query results cache`

**Sortie**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|NodeId|`string`|Identificateur du nœud de cluster.
|Correspondances  |`long`|Nombre d’accès au cache.
|Absences  |`long`|Nombre d’absences dans le cache.
|CacheCapacityInBytes |`long` |Capacité du cache en octets.
|UsedBytes  |`long` |Espace utilisé par le cache.
|Count  |`long`| Nombre de résultats de requête uniques stockés dans le cache.

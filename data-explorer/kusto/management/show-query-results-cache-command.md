---
title: Afficher le cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit. afficher le cache des résultats de la requête dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: e037b6ed01a2eba7c7370d75a4efea0f7cbc1756
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402716"
---
# <a name="show-database-cache-query_results"></a>. afficher le cache de base de données query_results

Retourne une table qui affiche des statistiques relatives au [cache des résultats](../query/query-results-cache.md) de la requête effectuée sur la base de données de contexte.

**Syntaxe**

`.show database cache query_results`

**Sortie**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|NodeId|`string`|Identificateur du nœud de cluster.
|Correspondances  |`long`|Nombre d’accès au cache.
|Absences  |`long`|Nombre d’absences dans le cache.
|CacheCapacityInBytes |`long` |Capacité du cache en octets.
|UsedBytes  |`long` |Espace utilisé par le cache.
|Nombre  |`long`| Nombre de résultats de requête uniques stockés dans le cache.

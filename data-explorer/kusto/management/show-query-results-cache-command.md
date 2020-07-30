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
ms.openlocfilehash: 7b7a96a01a4ec2b6c84609b2f9c518637d174390
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349888"
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

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
ms.openlocfilehash: bcbdb59355ce0461d735cbea902551c219479fd2
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943073"
---
# <a name="show-query-results-cache"></a>. afficher le cache des résultats de la requête

Retourne une table qui affiche les statistiques relatives au [cache des résultats](../query/query-results-cache.md)de la requête.

**Syntaxe**

`.show` `query` `results` `cache`

**Sortie**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|Correspondances  |long |Nombre d’accès au cache.
|Absences  |long |Nombre d’absences dans le cache.
|CacheCapacityInBytes |long |Capacité du cache en octets.
|UsedBytes  |long |Espace utilisé par le cache.
|Count  |long | Nombre de résultats de requête uniques stockés dans le cache.

**Limitations**

* Actuellement, la sortie de la commande reflète uniquement les statistiques de cache collectées par le nœud sur lequel la demande a été débarquée.
* La commande affiche uniquement l’historique « récent ».

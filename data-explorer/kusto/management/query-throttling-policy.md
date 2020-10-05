---
title: Stratégie de limitation des requêtes-Azure Explorateur de données
description: Cet article décrit la stratégie de limitation des requêtes dans Azure Explorateur de données
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 7d6db9ba7cbb7b1fbaaee7325043fed287673178
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734005"
---
# <a name="query-throttling-policy"></a>Stratégie de limitation des requêtes

Définissez la stratégie de limitation des requêtes pour limiter la quantité de requêtes simultanées que le cluster exécute en même temps. La stratégie peut être modifiée au moment de l’exécution et est effectuée immédiatement après la fin de la commande ALTER Policy.

* Utilisez [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) pour afficher la stratégie de limitation actuelle des requêtes d’un cluster.
* Utilisez [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) pour définir la stratégie de limitation actuelle des requêtes d’un cluster.
* Utilisez [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) pour supprimer la stratégie de limitation de requête actuelle d’un cluster.

> [!NOTE]
> Si vous ne définissez pas de stratégie de limitation des requêtes, un nombre élevé de requêtes simultanées peut entraîner une dégradation des performances ou de l’inaccessibilité du cluster.

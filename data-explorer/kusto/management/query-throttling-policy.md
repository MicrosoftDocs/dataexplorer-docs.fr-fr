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
ms.openlocfilehash: 0f50170e3ccfab20d434a90188baf511de056cef
ms.sourcegitcommit: 541164d8745022906b1da4aaca41ec15393f85d5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570563"
---
# <a name="query-throttling-policy"></a>Stratégie de limitation des requêtes

Définissez la stratégie de limitation des requêtes pour limiter le nombre de requêtes simultanées que le cluster peut exécuter en même temps. Cette stratégie empêche la surcharge du cluster avec davantage de requêtes simultanées qu’il ne peut en supporter. La stratégie peut être modifiée au moment de l’exécution et est effectuée immédiatement après la fin de la commande ALTER Policy.

* Utilisez [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) pour afficher la stratégie de limitation actuelle des requêtes d’un cluster.
* Utilisez [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) pour définir la stratégie de limitation actuelle des requêtes d’un cluster.
* Utilisez [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) pour supprimer la stratégie de limitation de requête actuelle d’un cluster.

> [!NOTE]
> Ne modifiez pas la stratégie de limitation des requêtes sans test minutieux. Les modifications peuvent entraîner une dégradation des performances si les ressources du cluster ne sont pas mises à l’échelle en fonction de la charge, et par conséquent, les requêtes peuvent échouer. 

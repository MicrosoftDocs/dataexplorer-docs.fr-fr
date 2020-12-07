---
title: Informations système-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les informations système dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/07/2019
ms.openlocfilehash: 22ad4956d94f445b26e49e1e73ed54e86568d1ad
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321214"
---
# <a name="system-information"></a>Informations système

Cette section résume les commandes disponibles aux `Database Admins` `Database Monitors` rôles et pour explorer l’utilisation, suivre les opérations et examiner les échecs d’ingestion.

* [`.show queries`](queries.md) -affiche des informations sur les requêtes terminées et en cours d’exécution.
* [`.show commands`](commands.md) -affiche des informations sur les commandes terminées et leur utilisation des ressources.
* [`.show commands-and-queries`](commands-and-queries.md) -affiche des informations sur les commandes et les requêtes terminées, ainsi que sur leur utilisation des ressources.
* [`.show journal`](journal.md) -affiche l’historique des opérations de métadonnées.
* [`.show operations`](operations.md) -affiche les opérations d’administration en cours d’exécution et terminées, depuis la dernière élection du nœud admin.
* [`.show failed ingestions`](ingestionfailures.md) -affiche des informations sur les défaillances rencontrées au cours de l’ingestion des données sur le cluster.
---
title: Kusto Ping REST API - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto Ping REST API dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 14a3d3dd641ff435784eac4e813d98bac8c4a552
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502582"
---
# <a name="kusto-ping-rest-api"></a>Kusto Ping REST API

L’API REST ping est fourni pour permettre aux périphériques réseau tels que les équilibreurs de charge de vérifier la simple réactivité réseau des composants frontaux apatrides du cluster. Lorsqu’un verbe GET est appliqué à ce point de terminaison, le cluster répond en retournant une réponse HTTP 200 OK. L’API REST n’est pas athénisée (l’appelant n’a pas besoin d’envoyer un en-tête HTTP). `Authorization`

- Chemin d’accès : `/v1/rest/ping`
- Verbe:`GET`
- Actions : Répondez par un `200 OK` message
- Arguments: Aucun
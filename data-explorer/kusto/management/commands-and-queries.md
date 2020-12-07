---
title: Gestion des commandes et des requêtes-Azure Explorateur de données
description: Cet article décrit la gestion des commandes et des requêtes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 222d04939560342bd849c15f249b1a8a582316a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321027"
---
# <a name="commands-and-queries-management"></a>Gestion des commandes et des requêtes

## <a name="show-commands-and-queries"></a>. afficher les commandes et les requêtes 

`.show``commands-and-queries`retourne une table avec des commandes d’administration et des requêtes qui ont atteint un état final. Ces commandes et requêtes sont disponibles pendant 30 jours.

Les informations présentées dans la sortie de la commande sont similaires aux [ `.show` commandes](commands.md) et aux [ `.show` requêtes](queries.md), mais elles vous permettent essentiellement de joindre les deux jeux de résultats de manière simple.

**Syntaxe**

`.show` `commands-and-queries`
 
**Sortie**
 
Le schéma de sortie est le suivant :

| ColumnName               | ColumnType |
|--------------------------|------------|
| ClientActivityId         | string     |
| CommandType              | string     |
| Texte                     | string     |
| Base de données                 | string     |
| StartedOn                | DATETIME   |
| LastUpdatedOn            | DATETIME   |
| Duration                 | intervalle de temps   |
| État                    | string     |
| FailureReason            | string     |
| RootActivityId           | guid       |
| Utilisateur                     | string     |
| Application              | string     |
| Principal                | string     |
| ClientRequestProperties  | dynamique    |
| TotalCpu                 | intervalle de temps   |
| MemoryPeak               | long       |
| CacheStatistics          | dynamique    |
| ScannedExtentsStatistics | dynamique    |
| ResultSetStatistics      | dynamique    |

> [!NOTE]
> Pour les requêtes, la valeur de `CommandType` est `Query` .

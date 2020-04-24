---
title: Gestion des commandes et des requêtes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des commandes et des requêtes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: f8c01709d69a0b439ce51b4782eb8f747db15d65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521911"
---
# <a name="commands-and-queries-management"></a>Gestion des commandes et des requêtes

## <a name="show-commands-and-queries"></a>.afficher les commandes et les requêtes 

`.show``commands-and-queries` retourne une table avec des commandes d’administration et des requêtes qui ont atteint un état final. Ces commandes et requêtes sont disponibles pour les requêtes pendant 30 jours.

L’information présentée dans la sortie de la commande est similaire à celle présentée par [les commandes .show](commands.md) et les [requêtes .show](queries.md), mais il permet essentiellement d’union les deux résultats ensembles d’une manière simple.

**Syntaxe**

`.show` `commands-and-queries`
 
**Sortie**
 
Le schéma de sortie est le suivant :

| ColumnName               | ColumnType |
|--------------------------|------------|
| ClientActivitéId         | string     |
| CommandType              | string     |
| Texte                     | string     |
| Base de données                 | string     |
| StartedOn                | DATETIME   |
| LastUpdatedOn            | DATETIME   |
| Duration                 | intervalle de temps   |
| State                    | string     |
| ÉchecReason            | string     |
| RootActivityId           | guid       |
| Utilisateur                     | string     |
| Application              | string     |
| Principal                | string     |
| ClientRequestProperties (en)  | dynamique    |
| TotalCpu TotalCpu                 | intervalle de temps   |
| MemoryPeak (en)               | long       |
| CacheStatistics          | dynamique    |
| ExtentsStatistiques numérisés | dynamique    |
| RésultatSetStatistics      | dynamique    |

Notez que pour les `CommandType` requêtes, la valeur de est `Query`.
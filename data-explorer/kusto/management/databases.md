---
title: Gestion des bases de données-Azure Explorateur de données
description: Cet article décrit la gestion des bases de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 9dc2e1bf489123ddd66c9a203d74284e36eda53e
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320942"
---
# <a name="databases-management"></a>Gestion des bases de données

Cette rubrique décrit les commandes de contrôle de base de données suivantes :

|Commande |Description |
|--------|------------|
|[`.show databases`](show-databases.md) |Retourne une table dans laquelle chaque enregistrement correspond à une base de données du cluster à laquelle l’utilisateur a accès.|
|[`.show database`](show-database.md) |Retourne une table qui affiche les propriétés de la base de données de contexte |
|[`.show cluster databases`](show-cluster-database.md) |Retourne une table qui affiche toutes les bases de données attachées au cluster et auxquelles l’utilisateur qui appelle la commande a accès |
|[`.alter database`](alter-database.md) |Modifie le nom convivial d’une base de données |
|[`.show database schema`](show-schema-database.md) |Retourne une liste plate de la structure des bases de données sélectionnées avec toutes leurs tables et colonnes dans une table unique ou un objet JSON |
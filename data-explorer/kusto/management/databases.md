---
title: Gestion des bases de données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des bases de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 78fcba2db059c0115d65032610009ab20bd2d5bd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521282"
---
# <a name="databases-management"></a>Gestion des bases de données

Ce sujet décrit les commandes de contrôle de base de données suivantes :

|Commande |Description |
|--------|------------|
|[.afficher les bases de données](show-databases.md) |Renvoie un tableau dans lequel chaque enregistrement correspond à une base de données dans le cluster auquel l’utilisateur a accès|
|[.afficher la base de données](show-database.md) |Retourne un tableau montrant les propriétés de la base de données contextuelle |
|[.afficher les bases de données cluster](show-cluster-database.md) |Renvoie un tableau montrant toutes les bases de données attachées au cluster et auxquelles l’utilisateur invoquant la commande a accès |
|[.modifier la base de données](alter-database.md) |Modifie le joli nom (amical) d’une base de données |
|[.drop base de données](drop-database.md) |Laisse tomber le joli nom (amical) d’une base de données |
|[.afficher le schéma de base de données](show-schema-database.md) |Retourne une liste plate de la structure des bases de données sélectionnées avec toutes leurs tables et colonnes dans une seule table ou un objet JSON |
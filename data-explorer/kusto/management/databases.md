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
ms.openlocfilehash: 03d21dc76bbffb72275a35c86bb8030942228184
ms.sourcegitcommit: addc4eb50ae65240975d63292e9f6907a74f5dfe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966738"
---
# <a name="databases-management"></a>Gestion des bases de données

Cette rubrique décrit les commandes de contrôle de base de données suivantes :

|Commande |Description |
|--------|------------|
|[. afficher les bases de données](show-databases.md) |Retourne une table dans laquelle chaque enregistrement correspond à une base de données du cluster à laquelle l’utilisateur a accès.|
|[.show database](show-database.md) |Retourne une table qui affiche les propriétés de la base de données de contexte |
|[. afficher les bases de données de cluster](show-cluster-database.md) |Retourne une table qui affiche toutes les bases de données attachées au cluster et auxquelles l’utilisateur qui appelle la commande a accès |
|[. ALTER DATABASE](alter-database.md) |Modifie le nom convivial d’une base de données |
|[.show database schema](show-schema-database.md) |Retourne une liste plate de la structure des bases de données sélectionnées avec toutes leurs tables et colonnes dans une table unique ou un objet JSON |
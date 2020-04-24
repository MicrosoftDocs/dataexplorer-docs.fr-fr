---
title: Autorisation basée sur les rôles à Kusto - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’autorisation basée sur les rôles dans Kusto dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: fe7013e3ee4b842dc2dcb2e251c342897fa4e489
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522625"
---
# <a name="role-based-authorization-in-kusto"></a>Autorisation basée sur les rôles à Kusto



**L’autorisation** est le processus d’autorisation ou d’autorisation d’une autorisation principale de sécurité pour effectuer une action.
Kusto utilise un modèle **de contrôle d’accès basé sur** les rôles, en vertu duquel les directeurs authentifiés sont cartographiés aux **rôles,** et obtenir l’accès en fonction des rôles qui leur sont assignés.

Le service **Kusto Engine** a les rôles suivants :

|Role                       |Autorisations                                                                                                                                                  |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Toutes les bases de données admin        |peut faire "n’importe quoi" dans la portée de n’importe quelle base de données; Peut montrer et modifier certaines stratégies de niveau cluster.                                                           |
|Administrateur de base de données             |Peut faire "n’importe quoi" dans la portée d’une base de données particulière.                                                                                                     |
|Utilisateur de base de données              |Peut lire toutes les données et métadonnées de la base de données; en outre, peut créer des tableaux (devenant ainsi l’administrateur de table pour cette table) et fonctionne dans la base de données.|
|Tous les téléspectateurs de bases de données       |Peut lire toutes les données et métadonnées de n’importe quelle base de données.                                                                                                              |
|Observateur de base de données            |Peut lire toutes les données et métadonnées d’une base de données particulière.                                                                                                     |
|Ingéreur de base de données          |Peut ingérer des données pour toutes les tables existantes dans la base de données, mais pas interroger les données.                                                                              |
|Observateur non restreint de base de données|Peut interroger toutes les tables de la base de données qui ont activé la [politique RestrictedViewAccess.](../restrictedviewaccess-policy.md)                                |
|Moniteur de base de données           |Peut `.show` exécuter des commandes dans le cadre de la base de données et de ses entités enfant.                                                                          |
|Admin fonction             |Peut modifier la fonction, supprimer la fonction ou accorder des autorisations d’administration à un autre directeur.                                                                         |
|Administrateur de table                |Peut effectuer n’importe quelle opération dans la portée d’une table particulière.                                                                                                          |
|Ingéreur de table             |Peut ingérer des données dans la portée d’une table particulière, mais pas interroger les données.                                                                                  |

---
title: Autorisation basée sur les rôles dans Kusto-Azure Explorateur de données
description: Cet article décrit l’autorisation basée sur les rôles dans Kusto dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 8e961d389b365d77c7dddd557a28a158add2e3f3
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329076"
---
# <a name="role-based-authorization-in-kusto"></a>Autorisation basée sur les rôles dans Kusto

L' **autorisation** est le processus qui consiste à autoriser ou non une autorisation du principal de sécurité à effectuer une action.
Kusto utilise un modèle de **contrôle d’accès en fonction du rôle** , sous lequel les principaux authentifiés sont mappés aux **rôles**et accèdent à l’accès en fonction des rôles qui leur sont attribués.

Le service du **moteur Kusto** a les rôles suivants :

|Role                       |Autorisations                                                                                                                                                  |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Administration de toutes les bases de données        |Peut faire tout ce qui se trouve dans l’étendue d’une base de données. Peut afficher et modifier certaines stratégies au niveau du cluster                                                               |
|Administrateur de base de données             |Peut faire tout ce qui se trouve dans l’étendue d’une base de données particulière                                                                                                         |
|Utilisateur de base de données              |Peut lire toutes les données et métadonnées de la base de données. En outre, peut créer des tables et devenir l’administrateur de table pour ces tables, et créer des fonctions dans la base de données.|
|Visionneuse de toutes les bases de données       |Peut lire toutes les données et métadonnées de n’importe quelle base de données                                                                                                               |
|Observateur de base de données            |Peut lire toutes les données et métadonnées d’une base de données particulière                                                                                                       |
|Ingéreur de base de données          |Peut ingérer des données dans toutes les tables existantes de la base de données, mais ne peut pas interroger les données                                                                             |
|Observateur non restreint de base de données|Peut interroger toutes les tables de la base de données pour lesquelles la [stratégie RestrictedViewAccess](../restrictedviewaccess-policy.md) est activée                                |
|Moniteur de base de données           |Peut exécuter des `.show` commandes dans le contexte de la base de données et de ses entités enfants                                                                           |
|Admin de fonction             |Peut modifier la fonction, supprimer la fonction ou accorder des autorisations d’administrateur à un autre principal                                                                         |
|Administrateur de table                |Peut faire tout ce qui se trouve dans l’étendue d’une table particulière                                                                                                           |
|Ingéreur de table             |Peut ingérer des données dans l’étendue d’une table particulière, mais ne peut pas interroger les données                                                                                 |

---
title: Gérer les autorisations de base de données dans Azure Data Explorer
description: Cet article décrit les contrôles d’accès en fonction du rôle pour les bases de données et les tables dans l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 25bdd9c36c63d521cd662ab5c69f3dfec974b1e6
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874764"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Gérer les autorisations de base de données de l’Explorateur de données Azure

L’Explorateur de données Azure vous permet de contrôler l’accès aux bases de données et aux tables à l’aide d’un modèle de *contrôle d’accès en fonction du rôle*. Avec ce modèle, les *principaux* (utilisateurs, groupes et applications) sont mappés aux *rôles*. Les principaux peuvent accéder aux ressources selon les rôles auxquels ils sont affectés.

Cet article décrit les rôles disponibles et comment affecter des principaux à ces rôles à l’aide du portail Azure, ainsi que les commandes de gestion de l’Explorateur de données Azure.

## <a name="roles-and-permissions"></a>Rôles et autorisations

L’Explorateur de données Azure a les rôles suivants :

|Role                       |Autorisations                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Administrateur de base de données             |Peut effectuer n’importe quelle opération dans la portée d’une base de données particulière.|
|Utilisateur de base de données              |Peut lire toutes les données et métadonnées dans la base de données. De plus, il peut créer des tables (devenir l’administrateur de table pour cette table) et des fonctions dans la base de données.|
|Observateur de base de données            |Peut lire toutes les données et métadonnées dans la base de données.|
|Ingéreur de base de données          |Peut ingérer des données pour toutes les tables existantes dans la base de données, mais pas interroger les données.|
|Moniteur de base de données           |Peut exécuter des commandes « .show... » dans le contexte de la base de données et ses entités enfants.|
|Administrateur de table                |Peut effectuer n’importe quelle opération dans la portée d’une table particulière. |
|Ingéreur de table             |Peut ingérer des données dans la portée d’une table particulière, mais pas interroger les données.|

## <a name="manage-permissions-in-the-azure-portal"></a>Gérer les autorisations dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre cluster de l’Explorateur de données Azure.

1. Dans la section **Vue d’ensemble**, sélectionnez la base de données où vous souhaitez gérer les autorisations.

    ![Sélectionner une base de données](media/manage-database-permissions/select-database.png)

1. Sélectionnez **Autorisations**, puis **Ajouter**.

    ![Autorisations de base de données](media/manage-database-permissions/database-permissions.png)

1. Sous **Ajouter des autorisations de base de données**, sélectionnez le rôle auquel vous souhaitez affecter le principal, puis **Select principals** (Sélectionner les principaux).

    ![Ajouter des autorisations de base de données](media/manage-database-permissions/add-permission.png)

1. Recherchez le principal, sélectionnez-le, puis **Sélectionner**.

    ![Gérer les autorisations dans le portail Azure](media/manage-database-permissions/new-principals.png)

1. Sélectionnez **Enregistrer**.

    ![Gérer les autorisations dans le portail Azure](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Gérer les autorisations avec des commandes de gestion

1. Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com) et ajoutez votre cluster s’il n’est pas déjà disponible.

1. Dans le volet gauche, sélectionnez la base de données appropriée.

1. Utilisez la commande `.add` pour affecter des principaux à des rôles : `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Pour ajouter un utilisateur au rôle Utilisateur de base de données, exécutez la commande suivante, en utilisant vos utilisateur et nom de base de données.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    La sortie de la commande affiche la liste des utilisateurs existants et les rôles auxquels ils sont affectés dans la base de données.
    
    Pour obtenir des exemples relatifs à Azure Active Directory et au modèle d’autorisation Kusto, consultez [Principes et fournisseurs d’identité](kusto/management/access-control/principals-and-identity-providers.md)

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes](write-queries.md)

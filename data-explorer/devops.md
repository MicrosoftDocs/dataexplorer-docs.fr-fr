---
title: Tâche Azure DevOps pour Azure Data Explorer
description: Dans cette rubrique, vous allez apprendre à créer et à déployer un pipeline de mise en production
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/05/2019
ms.openlocfilehash: e64d348387e18d9421632d3ebf6a4772fcf2d82a
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554904"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps Task pour Azure Data Explorer

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) fournit des outils de développement collaboratifs tels que des pipelines haute performance, référentiels Git privés gratuits, tableaux Kanban configurables et fonctionnalités de test continu et automatisé. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) est une fonctionnalité Azure DevOps qui vous permet de gérer CI/CD pour déployer votre code avec des pipelines haute performance fonctionnant avec n'importe quel langage, plateforme ou cloud.
[Azure Data Explorer - Commandes d’administration](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) est la tâche Azure Pipelines qui vous permet de créer des pipelines de mise en production et de déployer vos modifications de base de données dans vos bases de données Azure Data Explorer. Il est disponible gratuitement dans [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Ce document décrit un exemple simple d’utilisation de la tâche **Azure Data Explorer – Commandes d'administration** pour déployer vos modifications de schéma dans votre base de données. Pour les pipelines CI/CD, consultez la [documentation Azure DevOps](/azure/devops/user-guide/what-is-azure-devops#vsts).

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Configuration d'un cluster Azure Data Explorer :
    * Un [cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).
    * Créez une application Azure Active Directory (Azure AD) en [approvisionnant une application Azure AD](./provision-azure-ad-app.md).
    * Accordez l’accès à votre application Azure AD sur votre base de données Azure Data Explorer via la [gestion des autorisations de base de données Azure Data Explorer](manage-database-permissions.md).
* Configuration d'Azure DevOps :
    * [S'inscrire pour une organisation gratuite](/azure/devops/user-guide/sign-up-invite-teammates)
    * [Créer une organisation](/azure/devops/organizations/accounts/create-organization)
    * [Créer un projet dans Azure DevOps](/azure/devops/organizations/projects/create-project)
    * [Coder avec Git](/azure/devops/user-guide/code-with-git)

## <a name="create-folders"></a>Créez les dossiers.

Créez les dossiers exemples suivants (*Fonctions*, *Stratégies*, *Tables*) dans votre référentiel Git. Copiez les fichiers disponibles [ici](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) dans les dossiers respectifs, comme illustré ci-dessous, et validez les modifications. Les fichiers exemples sont fournis pour exécuter le workflow suivant.

![Créez les dossiers.](media/devops/create-folders.png)

> [!TIP]
> Lorsque vous créez votre propre workflow, nous vous recommandons de rendre votre code idempotent. Par exemple, utilisez [`.create-merge table`](kusto/management/create-merge-table-command.md) au lieu de [`.create table`](kusto/management/create-table-command.md), et utilisez la fonction [`.create-or-alter`](kusto/management/create-alter-function.md) au lieu de la fonction [`.create`](kusto/management/create-function.md).

## <a name="create-a-release-pipeline"></a>Créer un pipeline de mise en production

1. Connectez-vous à votre [organisation Azure DevOps](https://dev.azure.com/).
1. Sélectionnez **Pipelines** > **Mises en production** dans le menu de gauche, puis **Nouveau pipeline**.

    ![Nouveau pipeline](media/devops/new-pipeline.png)

1. La fenêtre **Nouveau pipeline de mise en production** s'ouvre. Dans l'onglet **Pipelines**, sous le volet **Sélectionner un modèle**, sélectionnez **Projet vide**.

     ![Sélectionner un modèle](media/devops/select-template.png)

1. Sélectionnez le bouton **Phase**. Dans le volet **Phase**, ajoutez le **nom de la phase**. Sélectionnez **Enregistrer** pour enregistrer votre pipeline.

    ![Nommer la phase](media/devops/stage-name.png)

1. Sélectionnez le bouton **Ajouter un artefact**. Dans le volet **Ajouter un artefact**, sélectionnez le référentiel où se trouve votre code, renseignez les informations pertinentes, puis cliquez sur **Ajouter**. Sélectionnez **Enregistrer** pour enregistrer votre pipeline.

    ![Ajouter un artefact](media/devops/add-artifact.png)

1. Dans l'onglet **Variables**, sélectionnez **+ Ajouter** pour créer une variable pour l'**URL de point de terminaison** qui sera utilisée dans la tâche. Notez le **nom** et la **valeur** du point de terminaison. Sélectionnez **Enregistrer** pour enregistrer votre pipeline. 

    ![Créer une variable](media/devops/create-variable.png)

    Pour rechercher votre Endpoint_URL, la page Vue d’ensemble de votre **cluster Azure Data Explorer** dans le portail Azure contient l’URI du cluster Azure Data Explorer. Construisez l’URI au format suivant `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Par exemple, https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![URI du cluster Azure Data Explorer](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Créer des tâches de déploiement

1. Dans l'onglet **Pipeline**, cliquez sur **1 travail, 0 tâche** pour ajouter des tâches. 

    ![Ajouter des tâches](media/devops/add-task.png)

1. Créez trois tâches à déployer **Tables**, **Fonctions** et **Stratégies**, dans cet ordre. 

1. Dans l'onglet **Tâches**, sélectionnez **+** par **Travail d'agent**. Recherchez **Azure Data Explorer**. Dans **Place de marché**, installez l'extension **Azure Data Explorer – Commandes d’administration**. Sélectionnez ensuite **Ajouter** dans **Exécuter la commande Azure Data Explorer**.

     ![Ajouter des commandes d’administration](media/devops/add-admin-commands.png)

1. Cliquez sur **Commande Kusto** à gauche, puis mettez à jour la tâche avec les informations suivantes :
    * **Nom d'affichage** : Nom de la tâche
    * **Chemin d'accès au fichier** : Dans la tâche **Tables**, spécifiez */Tables/* .csl car les fichiers de création de table se trouvent dans le dossier *Table*.
    * **URL de point de terminaison** : entrez la variable `EndPoint URL` créée à l’étape précédente.
    * Sélectionnez **Utiliser un point de terminaison de service** , puis **+ Nouveau**.

    ![Mettre à jour la tâche de la commande Kusto](media/devops/kusto-command-task.png)

1. Complétez les informations suivantes dans la fenêtre **Ajouter une connexion de service Azure Data Explorer** :

    |Paramètre  |Valeur suggérée  |
    |---------|---------|
    |**Nom de connexion**     |    Entrer un nom pour identifier ce point de terminaison de service     |
    |**URL du cluster**    |    La valeur se trouve dans la section Vue d'ensemble de votre cluster Azure Data Explorer dans le portail Azure | 
    |**ID de principal de service**    |    Entrez l’ID d’application AAD (créée en prérequis)     |
    |**Clé d'application du principal de service**     |    Entrez la clé d’application AAD (créée en prérequis)    |
    |**ID de locataire AAD**    |      Entrez votre locataire AAD (par exemple, microsoft.com, contoso.com, etc.)    |

    Sélectionnez **Autoriser tous les pipelines à utiliser cette connexion**. Sélectionnez **OK**.

    ![Ajouter une connexion de service](media/devops/add-service-connection.png)

1. Répétez les étapes 1 à 5 deux fois pour déployer les fichiers à partir des dossiers *Fonctions* et *Stratégies*. Sélectionnez **Enregistrer**. Dans l'onglet **Tâches**, consultez les trois tâches créées : **Déployer des tables**, **Déployer des fonctions** et **Déployer des stratégies**.

    ![Déployer tous les dossiers](media/devops/deploy-all-folders.png)

1. Sélectionnez **+ Mise en production** > **Créer une mise en production** pour créer manuellement une mise en production.

    ![Créer une mise en production](media/devops/create-release.png)

1. Dans l'onglet **Journaux**, vérifiez que le déploiement a réussi.

    ![Le déploiement a réussi](media/devops/deployment-successful.png)

Vous avez créé un pipeline de mise en production afin de déployer trois tâches de préproduction.

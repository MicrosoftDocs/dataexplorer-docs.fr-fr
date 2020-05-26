---
title: Copier des données d’Azure Data Factory vers Azure Data Explorer
description: Cet article décrit comment ingérer (charger) des données dans Azure Data Explorer à l’aide de l’outil de copie d’Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 066f385e53c010f45979ec435e3446d880302e44
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373958"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Copier des données dans Azure Data Explorer à l’aide d’Azure Data Factory 

Azure Data Explorer est un service d’analyse de données rapide et complètement managé. Il effectue une analyse en temps réel de grands volumes de données diffusées en continu à partir de nombreuses sources, telles que des applications, des sites web et des appareils IoT. Avez Azure Data Explorer, vous pouvez explorer les données de façon itérative et identifier des tendances et anomalies afin d’améliorer les produits et les expériences client, surveiller des appareils et optimiser les opérations. Cela vous permet de parcourir les nouvelles questions et de recevoir des réponses en quelques minutes. 

Azure Data Factory est un service informatique d’intégration de données informatique intégralement managé. Vous pouvez l’utiliser pour remplir votre base de données Azure Data Explorer avec les données de votre système existant. Cela peut vous permettre de gagner du temps lorsque vous créez des solutions d’analyse.

Lorsque vous chargez des données dans Azure Data Explorer, Data Factory offre les avantages suivants :

* **Configuration aisée** : accès à assistant intuitif en cinq étapes ne nécessitant pas de script.
* **Prise en charge étendue du magasin de données** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et dans le cloud. Pour une liste détaillée, consultez le tableau [Banques de données prises en charge](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sécurité et conformité** : les données sont transférées via HTTPS ou Azure ExpressRoute. La présence globale du service garantit que vos données ne quittent jamais les limites géographiques.
* **Hautes performances** : la vitesse de chargement des données est de 1 Go par seconde (Go/s) dans Azure Data Explorer. Pour plus d’informations, consultez [Performances de l’activité de copie](/azure/data-factory/copy-activity-performance).

Dans cet article, vous utilisez l’outil de copie de données Data Factory pour charger les données d’Amazon Simple Storage Service (S3) dans Azure Data Explorer. Vous pouvez procéder de façon similaire pour copier des données à partir d’autres banques de données, par exemple :
* [stockage d’objets blob Azure](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Système de fichiers](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).
* Une source de données.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com).

1. Dans le volet de gauche, sélectionnez **Créer une ressource** > **Analytics** > **Data Factory**.

   ![Créer une fabrique de données sur le Portail Azure](media/data-factory-load-data/create-adf.png)

1. Dans le volet **Nouvelle fabrique de données**, fournissez les valeurs des champs dans le tableau suivant :

   ![Volet « Nouvelle fabrique de données »](media/data-factory-load-data/my-new-data-factory.png)  

   | Paramètre  | Valeur à entrer  |
   |---|---|
   | **Nom** | Dans la zone, entrez un nom global unique pour votre fabrique de données. Si l’erreur *Le nom de fabrique de données \"LoadADLSDemo\" n’est pas disponible* apparaît, saisissez un autre nom pour la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, consultez [Data Factory - Règles d’affectation des noms](/azure/data-factory/naming-rules).|
   | **Abonnement** | Dans la liste déroulante, sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. |
   | **Groupe de ressources** | Sélectionnez **Créer**, puis entrez le nom d’un nouveau groupe de ressources. Si vous disposez déjà d’un groupe de ressources, sélectionnez **Utiliser l’existant**. |
   | **Version** | Dans la liste déroulante, sélectionnez **V2**. |    
   | **Lieu** | Dans la liste déroulante, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste. Les magasins de données utilisés par la fabrique de données peuvent résider dans d’autres emplacements ou régions. |

1. Sélectionnez **Create** (Créer).

1. Pour superviser le processus de création, sélectionnez **Notifications** dans la barre d’outils. Une fois que vous avez créé la fabrique de données, sélectionnez-la.
   
   Le volet **Fabrique de données** s’ouvre.

   ![Volet Fabrique de données](media/data-factory-load-data/data-factory-home-page.png)

1. Pour ouvrir l’application dans un onglet séparé, sélectionnez la vignette **Créer et surveiller**.

## <a name="load-data-into-azure-data-explorer"></a>Charger des données dans Azure Data Explorer

Vous pouvez charger des données à partir de nombreux types de [magasins de données](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) dans Azure Data Explorer. Cet article explique comment charger des données à partir d’Amazon S3.

Vous pouvez charger vos données de l’une des manières suivantes :

* Dans l’interface utilisateur d’Azure Data Factory, dans le volet gauche, sélectionnez l’icône **Auteur**. Cela est illustré dans la section « Créer une fabrique de données » de [Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* Dans l’outil Copier des données d’Azure Data Factory, comme l’indique [Utiliser l’outil Copier des données pour copier des données](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Copier des données à partir d’Amazon S3 (source)

1. Dans la page **Prise en main**, sélectionnez **Copier des données** pour ouvrir l’outil Copier des données.

   ![Bouton de l’outil Copier des données](media/data-factory-load-data/copy-data-tool-tile.png)

1. Dans la zone **Nom de la tâche** du volet **Propriétés**, entrez un nom, puis sélectionnez **Suivant**.

    ![Volet Propriétés de Copier des données](media/data-factory-load-data/copy-from-source.png)

1. Dans le volet **Banque de données sources**, sélectionnez **Créer une connexion**.

    ![Le volet « Banque de données sources » de Copier des données](media/data-factory-load-data/source-create-connection.png)

1. Sélectionnez **Amazon S3**, puis **Continuer**.

    ![Volet Nouveau service lié](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Dans le volet **Nouveau service lié (Amazon S3)** , procédez comme suit :

    ![Spécifier le service lié Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Dans la zone **Nom**, entrez le nom de votre nouveau service lié.

    b. Dans la liste déroulante **Se connecter via le runtime d'intégration**, sélectionnez la valeur.

    c. Dans la zone **ID de clé d’accès**, entrez la valeur appropriée.
    
    > [!NOTE]
    > Dans Amazon S3, pour localiser votre clé d’accès, sélectionnez votre nom d’utilisateur Amazon dans la barre de navigation, puis sélectionnez **My Security Credentials** (Mes informations d’identification de sécurité).
    
    d. Dans la zone **Clé d’accès secrète**, entrez une valeur.

    e. Pour tester la connexion au service lié que vous avez créée, sélectionnez **Test Connection** (Tester la connexion).

    f. Sélectionnez **Terminer**.
    
      Votre nouvelle connexion AmazonS31 s’affiche dans le volet **Magasin de données sources**. 

1. Sélectionnez **Suivant**.

   ![Connexion de magasin de données sources créée](media/data-factory-load-data/source-data-store-created-connection.png)

1. Dans le volet **Choisir le fichier ou le dossier d’entrée**, procédez comme suit :

    a. Accédez au fichier ou dossier que vous souhaitez copier, puis sélectionnez-le.

    b. Sélectionnez le comportement de copie souhaité. Assurez-vous que la case à cocher **Copie binaire** est désactivée.

    c. Sélectionnez **Suivant**.

    ![Choisir le fichier ou le dossier d’entrée](media/data-factory-load-data/source-choose-input-file.png)

1. Dans le volet des **paramètres de formats de fichier**, sélectionnez les paramètres appropriés pour votre fichier. Sélectionnez sur **Suivant**.

   ![Volet « Paramètres de format de fichier »](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copier des données dans Azure Data Explorer (destination)

Le nouveau service lié Azure Data Explorer est créé pour copier les données dans la table de destination Azure Data Explorer (récepteur) spécifiée dans cette section.

> [!NOTE]
> Utilisez l’[activité de commande Azure Data Factory pour exécuter des commandes de contrôle d’Azure Data Explorer](data-factory-command-activity.md) et utilisez l’une des [commandes d’ingestion à partir d’une requête](kusto/management/data-ingestion/ingest-from-query.md), par exemple `.set-or-replace`.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Créer le service lié Azure Data Explorer

Pour créer le service lié Azure Data Explorer, procédez comme suit :

1. Pour utiliser une connexion au magasin de données existant ou spécifier un nouveau magasin de données, dans le volet **Banques de données de destination**, sélectionnez **Créer une connexion**.

    ![Volet Banque de données de destination](media/data-factory-load-data/destination-create-connection.png)

1. Dans le volet **Nouveau service lié**, sélectionnez **Azure Data Explorer**, puis sélectionnez **Continuer**.

    ![Volet Nouveau service lié](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Dans le volet **Nouveau service lié (Azure Data Explorer)** , procédez comme suit :

    ![Volet Nouveau service lié dans Azure Data Explorer](media/data-factory-load-data/adx-new-linked-service.png)

   a. Dans la zone **Nom**, entrez le nom du service lié Azure Data Explorer.

   b. Sous **Méthode de sélection du compte**, effectuez une des actions suivantes : 

    * Sélectionnez **À partir d’un abonnement Azure**, puis, dans les listes déroulantes, sélectionnez votre **Abonnement Azure** et votre **Cluster**. 

        > [!NOTE]
        > Le contrôle de liste déroulante **Cluster** répertorie uniquement les clusters associés à votre abonnement.

    * Sélectionnez **Entrer manuellement**, puis entrez votre **Point de terminaison**.

   c. Dans la zone **Locataire**, entrez le nom du locataire.

   d. Dans la zone **ID du principal de service**, entrez l’ID du principal de service.

   e. Sélectionnez **Clé du principal du service**, puis, dans la zone **Clé du principal du service**, entrez la valeur de la clé.

   f. Dans la liste déroulante **Base de données**, sélectionnez le nom de votre base de données. Vous pouvez également activer la case à cocher **Modifier**, puis entrer le nom de la base de données.

   g. Pour tester la connexion au service lié que vous avez créée, sélectionnez **Test Connection** (Tester la connexion). Si vous pouvez vous connecter à votre service lié, le volet affiche une coche verte et le message **Connexion établie**.

   h. Sélectionnez **Finish** (Terminer) pour achever la création du service lié.

    > [!NOTE]
    > Le principal du service est utilisé par Azure Data Factory pour accéder au service Azure Data Explorer. Pour créer le principal du service, accédez à [créer un principal du service Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). N’utilisez pas la méthode Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Configurer la connexion de données Azure Data Explorer

Une fois que vous avez créé la connexion au service lié, le volet **Banque de données de destination** s’ouvre, et cette connexion peut être utilisée. Pour configurer la connexion, procédez comme suit :

1. Sélectionnez **Suivant**.

    ![Volet « Banque de données de destination » dans Azure Data Explorer](media/data-factory-load-data/destination-data-store.png)

1. Dans le volet **Table mapping** (Mappage de table), définissez le nom de la table de destination, puis sélectionnez **Next** (Suivant).

    ![Volet « Mappage de table » du jeu de données de destination](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Dans le volet **Mappage de colonnes**, les mappages suivants ont lieu :

    a. Le premier mappage est effectué par Azure Data Factory conformément au [mappage de schéma Azure Data Factory ](/azure/data-factory/copy-activity-schema-and-type-mapping). Effectuez les actions suivantes :

    * Définissez les **Column mappings** (Mappages de colonnes) pour la table de destination Azure Data Factory. Le mappage par défaut est affiché de la source à la table de destination Azure Data Factory.

    * Annulez la sélection des colonnes dont vous n’avez pas besoin pour définir votre mappage de colonnes.

    b. Le deuxième mappage se produit lorsque ces données tabulaires sont ingérées dans Azure Data Explorer. Le mappage est effectué conformément aux [CSV mapping rules](kusto/management/mappings.md#csv-mapping) (Règles de mappage CSV). Même si les données sources ne sont pas au format CSV, Azure Data Factory les convertit dans un format tabulaire. Par conséquent, le mappage CSV est le seul mappage pertinent à ce niveau. Effectuez les actions suivantes :

    * (Facultatif) Sous **Azure Data Explorer (Kusto) sink properties** (Propriétés du récepteur Azure Data Explorer (Kusto)), ajoutez le **Ingestion mapping name** (Nom de mappage d’ingestion) pertinent afin que ce mappage de colonnes puisse être utilisé.

    * Si le **Ingestion mapping name** (Nom de mappage d’ingestion) n’est pas spécifié, l’ordre de mappage *par nom* défini dans la section **Column mappings** (Mappages de colonnes) est appliqué. Si le mappage *par nom* échoue, Azure Data Explorer tente d’ingérer les données dans un ordre *par position de colonne* (c’est-à-dire, mappe par position par défaut).

    * Sélectionnez **Suivant**.

    ![Volet « Mappage de colonnes » du jeu de données de destination](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Dans le volet **Paramètres**, procédez comme suit :

    a. Sous les **Paramètres de tolérance de panne**, entrez les paramètres appropriés.

    b. Sous **Paramètres de performance**, **Activer le mode de préproduction** ne s’applique pas, et **Paramètres avancés** inclut des considérations relatives aux coûts. Si vous n’avez pas d’exigences spécifiques, ne modifiez pas ces paramètres.

    c. Sélectionnez **Suivant**.

    ![Volet « Copier les paramètres de données »](media/data-factory-load-data/copy-data-settings.png)

1. Dans le volet **Résumé**, vérifiez les paramètres, puis sélectionnez **Suivant**.

    ![Volet « Copier le résumé des données »](media/data-factory-load-data/copy-data-summary.png)

1. Dans le volet **Déploiement terminé**, procédez comme suit :

    a. Pour basculer vers l’onglet **Moniteur** et voir l’état du pipeline (progression, erreurs et flux de données), sélectionnez **Monitor** (Moniteur).

    b. Pour modifier les services liés, les jeux de données et les pipelines, sélectionnez **Edit Pipeline** (Modifier le pipeline).

    c. Sélectionnez **Finish** (Terminer) pour achever la tâche de copie de données.

    ![Volet « Déploiement terminé »](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez le [connecteur Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) dans Azure Data Factory.
* Apprenez-en davantage sur la modification des services liés, jeux de données et pipelines dans l’[interface utilisateur de Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).
* Découvrez les [requêtes Azure Data Explorer](web-query-data.md) pour l’interrogation de données.

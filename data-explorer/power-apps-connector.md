---
title: Créer une application Power Apps pour interroger des données dans Azure Data Explorer
description: Découvrir comment créer une application dans Power Apps basée sur des données figurant dans Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 17ee68efa1f7c43814c4c86f9e5881cd0ba9af61
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349543"
---
# <a name="create-power-apps-application-to-query-data-in-azure-data-explorer-preview"></a>Créer une application Power Apps pour interroger des données dans Azure Data Explorer (préversion)

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour analyser en temps réel de gros volumes de streaming de données provenant des applications, des sites web, des appareils IoT, etc.

Power Apps se compose d’une suite d’applications, de services, de connecteurs et d’une plateforme de données qui fournit un environnement de développement d’applications rapide pour générer des applications personnalisées qui se connectent à vos données métier. Le connecteur Power Apps est particulièrement utile si vous disposez d’une collection de données de streaming volumineuse et en constante évolution dans Azure Data Explorer, et que vous voulez générer une application très fonctionnelle avec peu de code pour utiliser ces données. Dans cet article, vous allez créer une application Power Apps pour interroger des données Azure Data Explorer. Au cours de ce processus, vous verrez les étapes de paramétrage, de récupération et de présentation des données.

## <a name="prerequisites"></a>Prérequis

* Licence Power Platform. Commencer à utiliser [https://powerapps.microsoft.com](https://powerapps.microsoft.com).
* Être familiarisé avec la [suite Power Apps](/powerapps/powerapps-overview).

## <a name="connect-to-azure-data-explorer-connector"></a>Se connecter au connecteur Azure Data Explorer

1. Accédez à l’adresse [https://make.preview.powerapps.com/](https://make.preview.powerapps.com/), puis connectez-vous.

1. Sélectionnez **Connexions** dans le menu de gauche.
1. Sélectionnez **+ Nouvelle connexion**.

    :::image type="content" source="media/power-apps-connector/new-connection.png" alt-text="Créer une connexion dans Power Apps":::

1. Recherchez **Azure Data Explorer** dans la barre de recherche. Sélectionnez **Azure Data Explorer** dans les options qui s’affichent.

    :::image type="content" source="media/power-apps-connector/search-adx.png" alt-text="Rechercher et sélectionner la connexion Azure Data Explorer dans Power Apps":::

1. Sélectionnez **Créer** dans le menu contextuel **Azure Data Explorer**. Fournissez les informations d’identification, en fonction des besoins.

    :::image type="content" source="media/power-apps-connector/create-connector.png" alt-text="Créer un connecteur dans Azure Data Explorer - Fenêtre contextuelle":::

## <a name="create-app"></a>Créer une application

1. Accédez à Power Apps, puis sélectionnez **Applications** dans le menu de gauche.
1. Sélectionnez **+ Nouveau** dans la barre de menus.
1. Sélectionnez **Canevas** dans la liste déroulante qui s’affiche.

    :::image type="content" source="media/power-apps-connector/create-new-app.png" alt-text="Créer une application et un canevas - Connecteur Power Apps dans Azure Data Explorer":::

1. Dans la section **Application vide** , sélectionnez **Disposition de la tablette**.

    :::image type="content" source="media/power-apps-connector/blank-canvas.png" alt-text="Commencer avec un canevas vide dans la disposition de la tablette - Connecteur Power Apps dans Azure Data Explorer":::

### <a name="add-connector"></a>Ajouter un connecteur

1. Cliquez sur l’icône **Données** dans le volet de navigation de gauche. 
1. Développez **Connecteurs**.
1. Sélectionnez **Azure Data Explorer** dans les options qui s’affichent.

    :::image type="content" source="media/power-apps-connector/data-connectors-adx.png" alt-text="Ajouter un connecteur à Azure Data Explorer dans Power Apps":::

Une nouvelle zone appelée **Dans votre application** dans laquelle figure désormais **Azure Data Explorer** s’affiche alors.

   :::image type="content" source="media/power-apps-connector/adx-appears.png" alt-text="Azure Data Explorer figure désormais dans la zone Dans votre application dans Power Apps":::

### <a name="save-your-app"></a>Enregistrer votre application

1. Sélectionnez **Fichier** dans la barre de menus. 
1. Sélectionnez **Enregistrer** dans le volet de navigation de gauche.

    :::image type="content" source="media/power-apps-connector/save-app.png" alt-text="Enregistrer votre application dans Power Apps":::

1. Entrez un nom explicite pour votre application. Cliquez sur le bouton **Enregistrer** situé en bas à droite.

### <a name="advanced-settings"></a>Paramètres avancés

1. Sélectionnez **Paramètres** dans le menu de gauche.
1. Sélectionnez **Paramètres avancés**.
1. Sélectionnez **Schéma dynamique** dans les options qui s’affichent. Activez cette fonctionnalité.

    :::image type="content" source="media/power-apps-connector/dynamic-schema.png" alt-text="Activer le paramètre de schéma dynamique dans Power Apps - Connexion à Azure Data Explorer":::

1. Recherchez le paramètre **Limite de lignes de données pour les requêtes non délégables**. Définissez la limite de vos enregistrements retournés.

    :::image type="content" source="media/power-apps-connector/set-limit.png" alt-text="Définir la limite des résultats à retourner dans Power Apps - Azure Data Explorer":::

    > [!NOTE]
    > La limite par défaut est 500, avec un maximum de 2 000 enregistrements retournés.

> [!IMPORTANT]
> Enregistrez à nouveau votre application et redémarrez si nécessaire.

### <a name="add-dropdown"></a>Ajouter une liste déroulante

1. Sélectionnez **Insérer** dans la barre de menus. 
1. Sélectionnez **Entrée** dans la barre de sous-menus qui s’affiche. 
1. Sélectionnez **Liste déroulante** dans la liste déroulante qui s’affiche.
1. Cliquez sur l’onglet **Avancé** dans la fenêtre contextuelle de droite.
1. Renseignez la zone d’entrée **Éléments** zone d’entrée avec : ["CALIFORNIA","MICHIGAN"]

    :::image type="content" source="media/power-apps-connector/populate-dropdown.png" alt-text="Remplir les éléments dans le menu déroulant" lightbox="media/power-apps-connector/populate-dropdown.png":::

1. Avec l’option de **liste déroulante** toujours sélectionnée, sélectionnez **OnChange** dans la liste déroulante **Propriété** de la barre de formule.

1. Entrez la formule suivante :

    ```kusto
    ClearCollect(
    Results,
    AzureDataExplorer.listKustoResultsPost(
    "https://help.kusto.windows.net",
    "Samples",
    "StormEvents | where State == '" & Dropdown1.SelectedText.Value & "' | take 15"
    ).value
    )
    ```
    
1. Cliquez sur le bouton **Schéma de capture**. Patientez jusqu’à ce que le traitement s’achève.

    :::image type="content" source="media/power-apps-connector/capture-schema.png" alt-text="Sélectionner le bouton de schéma de capture dans le menu déroulant":::

### <a name="add-data-table"></a>Ajouter une table de données

1. Sélectionnez **Insérer** dans la barre de menus. 
1. Sélectionnez **Table de données** dans la barre de sous-menus qui s’affiche.
1. Repositionnez la table de données, puis ajoutez une bordure pour une meilleure visibilité.
1. Sélectionnez l’onglet **Propriétés** dans la fenêtre contextuelle de droite. Sélectionnez Résultats dans la liste déroulante **Source de données**.
1. Sélectionnez le lien **Modifier les champs**. 
1. Sélectionnez **+ Ajouter un champ** dans la fenêtre contextuelle qui s’affiche. 
    
    :::image type="content" source="media/power-apps-connector/insert-data-table-small.png" alt-text="Repositionner la table et ajouter une bordure" lightbox="media/power-apps-connector/insert-data-table.png":::

1. Sélectionnez les champs souhaités, puis cliquez sur le bouton **Ajouter**. Un aperçu de la table de données sélectionnée s’affiche.

    :::image type="content" source="media/power-apps-connector/preview-table.png" alt-text="Aperçu de la table remplie avec des données":::

### <a name="validate"></a>Valider

1. Cliquez sur le bouton **Afficher un aperçu de l’application** situé en haut à droite de l’écran.
1. Essayez la liste déroulante, faites défiler la table de données, puis vérifiez la réussite de l’extraction et de la présentation des données.

    :::image type="content" source="media/power-apps-connector/preview-app.png" alt-text="Afficher un aperçu de la nouvelle application dans Power Apps avec des données provenant d’Azure Data Explorer":::

### <a name="limitations"></a>Limites

* Power Apps a une limite maximale de 2 000 enregistrements de résultats retournés au client. La mémoire totale de ces enregistrements ne peut pas dépasser 64 Mo et un temps d’exécution de 7 minutes.
* Le connecteur ne prend pas en charge les opérateurs [fork](./kusto/query/forkoperator.md) et [facet](./kusto/query/facetoperator.md).
* **Exceptions relatives au délai d’expiration**  : Le connecteur a un délai d’expiration limité à 7 minutes. Pour éviter un problème de délai d’expiration potentiel , améliorez l’efficacité de votre requête afin qu’elle s’exécute plus rapidement, ou divisez-la en blocs. Chaque bloc peut s’exécuter sur une partie différente de la requête. Pour plus d’informations, consultez [Bonnes pratiques relatives aux requêtes](./kusto/query/best-practices.md).

## <a name="next-steps"></a>Étapes suivantes
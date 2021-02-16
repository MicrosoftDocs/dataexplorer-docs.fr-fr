---
title: Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer
description: Ingestion (chargement) simple de données dans une nouvelle table Azure Data Explorer, au moyen de l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 3ac9788eda7a75173778ce0533f59820cfd7e7dd
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528238"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer

> [!div class="op_single_selector"]
> * [Ingérer des données CSV d’un conteneur dans une nouvelle table](one-click-ingestion-new-table.md)
> * [Ingérer des données JSON d’un fichier local dans une table existante](one-click-ingestion-existing-table.md)

L’[ingestion en un clic](ingest-data-one-click.md) vous permet d’ingérer rapidement dans une table des données au format JSON, CSV et dans d’autres formats et de créer facilement des structures de mappage. Les données peuvent être ingérées à partir du stockage, d’un fichier local ou d’un conteneur, en tant que processus d’ingestion unique ou continu.  

Ce document décrit l’utilisation de l’Assistant intuitif Ingestion en un clic dans un cas d’usage spécifique pour ingérer des données **CSV** depuis un **conteneur** dans une **nouvelle table**. Après l’ingestion, vous pouvez [configurer un pipeline d’ingestion Event Grid](#create-continuous-ingestion-for-container) qui écoute les nouveaux fichiers du conteneur source et ingère les données éligibles dans votre nouvelle table. Vous pouvez utiliser le même processus avec de légères adaptations pour couvrir divers cas d’usage.

Pour obtenir une vue d’ensemble de l’ingestion en un clic, et la liste des prérequis, consultez [Ingestion en un clic](ingest-data-one-click.md).
Pour plus d’informations sur l’ingestion de données dans une table existante d’Azure Data Explorer, consultez [Ingestion en un clic dans une table existante](one-click-ingestion-existing-table.md).

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

1. Dans le menu de gauche de l’interface utilisateur web, cliquez avec le bouton droit sur une *base de données*, puis sélectionnez **Ingérer de nouvelles données**.

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="Ingérer de nouvelles données":::

1. Dans la fenêtre **Ingérer de nouvelles données**, l’onglet **Source** est sélectionné. Les champs **Cluster** et **Base de données** sont remplis automatiquement.

    [!INCLUDE [one-click-cluster](includes/one-click-cluster.md)]

1. Sélectionnez **Table** > **Créer** et entrez un nom pour la nouvelle table. Vous pouvez utiliser des caractères alphanumériques, des traits d’union et des traits de soulignement. Les caractères spéciaux ne sont pas pris en charge.

    > [!NOTE]
    > Les noms de table doivent comprendre entre 1 et 1024 caractères.

    :::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="Créer une table à l’aide de l’ingestion en un clic":::

## <a name="select-an-ingestion-type"></a>Sélectionner un type d’ingestion

Sous **Type de source**, effectuez les étapes suivantes :
   
  1. Sélectionnez **À partir du conteneur de blobs** (conteneur de blobs, conteneur ADLS Gen1, conteneur ADLS Gen2). Vous pouvez ingérer jusqu’à 1 000 blobs d’un seul conteneur.
  1. Dans le champ **Lien vers le stockage**, ajoutez l’[URL SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) du conteneur, puis entrez éventuellement la taille de l’échantillon. Pour ingérer les données d’un dossier dans ce conteneur, consultez [Ingérer les données d’un dossier dans un conteneur](#ingest-from-folder-in-a-container).

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="Ingestion en un clic à partir d’un conteneur":::

     > [!TIP] 
     > Pour effectuer une ingestion **à partir d’un fichier**, consultez [Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer](one-click-ingestion-existing-table.md#select-an-ingestion-type).

### <a name="ingest-from-folder-in-a-container"></a>Ingérer les données d’un dossier dans un conteneur

Pour ingérer les données d’un dossier spécifique dans un conteneur, [générez une chaîne au format suivant](kusto/api/connection-strings/storage.md#azure-data-lake-store) :

*container_path*`/`*folder_path*`;`*access_key_1*

Vous utiliserez cette chaîne au lieu de l’URL SAS dans [Sélectionner un type d’ingestion](#select-an-ingestion-type).

1. Accédez au compte de stockage, puis sélectionnez **Explorateur Stockage > Sélectionner des conteneurs d’objets blob**.

    :::image type="content" source="media/one-click-ingestion-new-table/blob-containers.png" alt-text="Capture d’écran de l’accès aux conteneurs d’objets blob dans le compte de stockage Azure":::

1. Accédez au dossier sélectionné, puis sélectionnez **Copier l’URL**. Collez cette valeur dans un fichier temporaire et ajoutez `;` à la fin de cette chaîne.

    :::image type="content" source="media/one-click-ingestion-new-table/copy-url.png" alt-text="Capture d’écran de l’option Copier l’URL dans le dossier du conteneur d’objets blob - Compte de stockage Azure":::

1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Clés d’accès**.

    :::image type="content" source="media/one-click-ingestion-new-table/copy-key-1.png" alt-text="Capture d’écran de la copie de la chaîne de clé dans Compte de stockage - Clés d’accès":::

1. Sous **Clé 1**, copiez la chaîne **Clé**. Collez cette valeur à la fin de la chaîne de l’étape 2. 

### <a name="storage-subscription-error"></a>Erreur concernant l’abonnement de stockage

Si vous recevez le message d’erreur suivant lors de l’ingestion d’un compte de stockage :

> Stockage introuvable sous les abonnements sélectionnés. Ajoutez l’abonnement du compte de stockage *`storage_account_name`* à vos abonnements sélectionnés dans le portail.

1. Sélectionnez l’icône :::image type="icon" source="media/ingest-data-one-click/directory-subscription-icon.png" border="false"::: dans le menu situé en haut à droite. Le volet **Annuaire + abonnement** s’ouvre.

1. Dans la liste déroulante **Tous les abonnements**, ajoutez l’abonnement de votre compte de stockage à la liste sélectionnée. 

    :::image type="content" source="media/ingest-data-one-click/subscription-dropdown.png" alt-text="Capture d’écran du volet Annuaire + abonnement avec la liste déroulante des abonnements encadrée en rouge.":::

## <a name="sample-data"></a>Exemples de données

Un échantillon des données s’affiche. Si vous le souhaitez, filtrez les données pour ingérer uniquement les fichiers qui commencent ou finissent par des caractères spécifiques. Quand vous ajustez les filtres, l’aperçu est mis à jour automatiquement.

Par exemple, filtrez tous les fichiers qui commencent par le mot d’extension *.csv*.

:::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="Filtre de l’ingestion en un clic":::

Le système sélectionne l’un des fichiers au hasard, et le schéma est généré en fonction du **fichier de définition du schéma**. Vous pouvez sélectionner un autre fichier.

## <a name="edit-the-schema"></a>Modifier le schéma

Sélectionnez **Modifier le schéma** pour afficher et modifier la configuration de colonne de votre table.  En examinant le nom de la source, le service identifie automatiquement s’il est compressé ou non.

Sous l’onglet **Schéma** :

   1. Sélectionnez **Format de données**.

        Dans ce cas, le format de données est **CSV**.

        > [!TIP]
        > Si vous souhaitez utiliser des fichiers **JSON**, consultez [Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer](one-click-ingestion-existing-table.md#edit-the-schema).

   1. Vous pouvez cocher la case **Inclure les noms des colonnes** pour ignorer la ligne d’en-tête du fichier.

        :::image type="content" source="media/one-click-ingestion-new-table/non-json-format.png" alt-text="Sélectionner Inclure les noms des colonnes":::

Dans le champ **Nom du mappage**, entrez un nom de mappage. Vous pouvez utiliser des caractères alphanumériques et des traits de soulignement. Les espaces, les caractères spéciaux et les traits d’Union ne sont pas pris en charge.

:::image type="content" source="media/one-click-ingestion-new-table/table-mapping.png" alt-text="Nom du mappage de table - Ingestion en un clic":::

### <a name="edit-the-table"></a>Modifier la table

Lors de l’ingestion dans une nouvelle table, modifiez les différents aspects de la table lors de la création de la table.

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

> [!NOTE]
> Pour les formats tabulaires, vous ne pouvez pas mapper deux fois une même colonne. Pour effectuer un mappage à une colonne existante, commencez par supprimer la nouvelle colonne.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Démarrer l’ingestion

Sélectionnez **Démarrer l’ingestion** pour créer une table et un mappage et pour commencer l’ingestion de données.

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="Démarrer l’ingestion - Ingestion en un clic":::

## <a name="complete-data-ingestion"></a>Terminer l’ingestion des données

Dans la fenêtre **Ingestion de données terminée**, les trois étapes sont signalées par des coches vertes quand l’ingestion des données s’est terminée avec succès.

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="Ingestion en un clic terminée"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="create-continuous-ingestion-for-container"></a>Créer une ingestion continue pour un conteneur

L’ingestion continue vous permet de créer une grille d’événement qui écoute les nouveaux fichiers dans le conteneur source. Tout nouveau fichier qui répond aux critères des paramètres prédéfinis (préfixe, suffixe, etc.) est automatiquement ingéré dans la table de destination. 

1. Sélectionnez **Event Grid** dans la vignette **Ingestion continue** pour ouvrir le portail Azure. La page de connexion des données s’affiche avec le connecteur de données de la grille d’événement ouvert, et les paramètres source et cible déjà entrés (conteneur source, tables et mappages).
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="Bouton de création d’ingestion continue":::

1. Sélectionnez **Créer** pour créer une connexion de données qui écoute toutes les modifications, mises à jour ou nouvelles données dans ce conteneur. 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="Créer une connexion de hub d’événements":::

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)

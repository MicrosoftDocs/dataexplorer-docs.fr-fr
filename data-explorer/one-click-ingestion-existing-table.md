---
title: Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer
description: Ingestion (chargement) simple de données dans une table Azure Data Explorer existante, au moyen de l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 41e0e50b2e91280c79941340ebfc855ef6cab27e
ms.sourcegitcommit: f71801764fdccb061f3cf1e3cfe43ec1557e4e0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93293405"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer


> [!div class="op_single_selector"]
> * [Ingérer des données CSV d’un conteneur dans une nouvelle table](one-click-ingestion-new-table.md)
> * [Ingérer des données JSON d’un fichier local dans une table existante](one-click-ingestion-existing-table.md)

L’[ingestion en un clic](ingest-data-one-click.md) vous permet d’ingérer rapidement dans une table des données au format JSON, CSV et dans d’autres formats et de créer facilement des structures de mappage. Les données peuvent être ingérées à partir du stockage, d’un fichier local ou d’un conteneur, en tant que processus d’ingestion unique ou continu.  

Ce document décrit l’utilisation de l’Assistant intuitif Ingestion en un clic dans un cas d’usage spécifique pour ingérer des données **JSON** depuis un **fichier local** dans une **table existante**. Utilisez le même processus avec de légères adaptations pour couvrir divers cas d’usage.

Pour obtenir une vue d’ensemble de l’ingestion en un clic, et la liste des prérequis, consultez [Ingestion en un clic](ingest-data-one-click.md).
Pour connaître les différents types ou sources de données, consultez [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md).

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

Dans le menu de gauche de l’interface utilisateur web, cliquez avec le bouton droit sur une *base de données* ou une *table* , puis sélectionnez **Ingérer de nouvelles données**.

   :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="Sélectionner l’ingestion en un clic dans l’interface utilisateur web":::
 
## <a name="select-an-ingestion-type"></a>Sélectionner un type d’ingestion

1. Dans la fenêtre **Ingérer de nouvelles données** , l’onglet **Source** est sélectionné.

1. Si le champ **Table** n’est pas renseigné automatiquement, sélectionnez un nom de table existant dans le menu déroulant.

    > [!NOTE]
    > Si vous sélectionnez **Ingérer de nouvelles données** sur une ligne de *table* , le nom de la table sélectionnée apparaît dans les **Détails du projet**.

1. Sous **Type d’ingestion** , effectuez les étapes suivantes :

   1. Sélectionnez **à partir d’un fichier**.  
   1. Sélectionnez **Parcourir** pour localiser le fichier ou faites glisser ce dernier dans le champ.
    
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="Ingestion en un clic à partir d’un fichier":::

 1. Un échantillon des données s’affiche. Filtrez les données pour ingérer uniquement les fichiers qui commencent ou finissent par des caractères spécifiques. 

    >[!NOTE] 
    >Quand vous ajustez les filtres, l’aperçu est mis à jour automatiquement.
  
> [!TIP]
> Pour effectuer une ingestion **à partir d’un conteneur** , consultez [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md#select-an-ingestion-type).

## <a name="edit-the-schema"></a>Modifier le schéma

Sélectionnez **Modifier le schéma** pour afficher et modifier la configuration de colonne de votre table.

### <a name="map-columns"></a>Mapper les colonnes 

1. La boîte de dialogue **Mapper les colonnes** s’ouvre. Attachez un ou plusieurs attributs ou colonnes sources à vos colonnes Azure Data Explorer.
    * Les nouveaux mappages sont définis automatiquement ou utilisent un mappage existant. 
    * Dans les champs **Colonnes sources** , entrez les noms des colonnes à mapper aux **Colonnes cibles**.
    * Pour supprimer une colonne du mappage, sélectionnez l’icône de la corbeille.

      :::image type="content" source="media/one-click-ingestion-existing-table/map-columns.png" alt-text="Fenêtre Mapper les colonnes"::: 
    
1. Sélectionnez **Update**.
1. Sous l’onglet **Schéma**  :
    * Le **type de compression** est sélectionné automatiquement par le nom de fichier source. Dans ce cas, le type de compression est **JSON**.
        
    * Quand vous sélectionnez **JSON** , vous devez également sélectionner **Niveaux JSON** , entre 1 et 10. Les niveaux déterminent la division des données dans les colonnes de la table.

        :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="Sélectionner un des niveaux JSON":::
    
       > [!TIP]
       > Si vous souhaitez utiliser des fichiers **CSV** , consultez [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md#edit-the-schema).

#### <a name="add-nested-json-data"></a>Ajouter des données JSON imbriquées 

Pour ajouter des colonnes de niveaux JSON différents des **niveaux JSON** principaux sélectionnés ci-dessus, effectuez les étapes suivantes :

1. Cliquez sur la flèche en regard d’un nom de colonne, puis sélectionnez **Nouvelle colonne**.

    :::image type="content" source="media/one-click-ingestion-existing-table/new-column.png" alt-text="Capture d’écran : options permettant d’ajouter une nouvelle colonne sous l’onglet Schéma durant le processus d’ingestion en un clic - Azure Data Explorer":::

1. Entrez un nouveau **Nom de colonne** et sélectionnez le **Type de colonne** dans le menu déroulant.
1. Sous **Source** , sélectionnez **Créer**.

    :::image type="content" source="media/one-click-ingestion-existing-table/create-new-source.png" alt-text="Capture d’écran : création d’une source pour ajouter des données JSON imbriquées durant le processus d’ingestion en un clic - Azure Data Explorer":::

1. Entrez la nouvelle source pour cette colonne, puis cliquez sur **OK**. Cette source peut provenir de n’importe quel niveau JSON.

    :::image type="content" source="media/one-click-ingestion-existing-table/name-new-source.png" alt-text="Capture d’écran : fenêtre contextuelle permettant de nommer la nouvelle source de données pour la colonne ajoutée - Ingestion en un clic Azure Data Explorer":::

1. Sélectionnez **Create** (Créer). Votre nouvelle colonne sera ajoutée à la fin de la table.

    :::image type="content" source="media/one-click-ingestion-existing-table/create-new-column.png" alt-text="Capture d’écran : créer une colonne durant l’ingestion en un clic dans Azure Data Explorer":::

### <a name="edit-the-table"></a>Modifier la table 

Lors de l’ingestion de données dans une table existante, vous êtes plus limité dans les modifications que vous pouvez apporter à la table.

Dans le tableau : 
* Sélectionnez les nouveaux en-têtes de colonne pour ajouter une **Nouvelle colonne** , **Supprimer la colonne** , **Trier par ordre croissant** ou **Trier par ordre décroissant**. 
* Sur les colonnes existantes, seul le tri des données est disponible.

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Démarrer l’ingestion

Sélectionnez **Démarrer l’ingestion** pour créer une table et un mappage et pour commencer l’ingestion de données.

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="Démarrer l’ingestion":::

## <a name="complete-data-ingestion"></a>Terminer l’ingestion des données

Dans la fenêtre **Ingestion de données terminée** , les trois étapes sont signalées par des coches vertes quand l’ingestion des données s’est terminée avec succès.

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="Ingestion en un clic terminée":::

> [!IMPORTANT]
> Pour configurer l’ingestion continue à partir d’un conteneur, consultez [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)

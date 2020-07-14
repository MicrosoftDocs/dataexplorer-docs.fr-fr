---
title: Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer
description: Ingestion (chargement) simple de données dans une table Azure Data Explorer existante, au moyen de l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 4e87ab389179f308642de3b9469544fde6139507
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058913"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer

L’[ingestion en un clic](ingest-data-one-click.md) vous permet d’ingérer rapidement dans une table des données au format JSON, CSV et dans d’autres formats et de créer facilement des structures de mappage. Les données peuvent être ingérées à partir du stockage, d’un fichier local ou d’un conteneur, en tant que processus d’ingestion unique ou continu.  

Ce document décrit l’utilisation de l’Assistant intuitif Ingestion en un clic dans un cas d’usage spécifique pour ingérer des données **JSON** depuis un **fichier local** dans une **table existante**. Vous pouvez utiliser le même processus avec de légères adaptations pour couvrir divers cas d’usage.

Pour obtenir une vue d’ensemble de l’ingestion en un clic, et la liste des prérequis, consultez [Ingestion en un clic](ingest-data-one-click.md).
Pour connaître les différents types ou sources de données, consultez [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md).

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

Dans le menu de gauche de l’interface utilisateur web, cliquez avec le bouton droit sur une *base de données* ou une *table*, puis sélectionnez **Ingérer de nouvelles données (préversion)** .

   :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="Sélectionner l’ingestion en un clic dans l’interface utilisateur web":::
 
## <a name="select-an-ingestion-type"></a>Sélectionner un type d’ingestion

1. Dans la fenêtre **Ingérer de nouvelles données (préversion)** , l’onglet **Source** est sélectionné.

1. Si le champ **Table** n’est pas renseigné automatiquement, sélectionnez un nom de table existant dans le menu déroulant.

    > [!NOTE]
    > Si vous sélectionnez **Ingérer de nouvelles données (préversion)** sur une ligne de *table*, le nom de la table sélectionnée s’affiche dans les **Détails du projet**.

1. Sous **Type d’ingestion**, effectuez les étapes suivantes :

   1. Sélectionnez **à partir d’un fichier**.  
   1. Sélectionnez **Parcourir** pour localiser le fichier ou faites glisser ce dernier dans le champ.
    
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="Ingestion en un clic à partir d’un fichier":::

 1. Un échantillon des données s’affiche. Vous pouvez les filtrer pour ingérer uniquement les fichiers qui commencent ou se terminent par des caractères spécifiques. 
   
    >[!NOTE] 
    >Quand vous ajustez les filtres, l’aperçu est mis à jour automatiquement.
  

> [!TIP]
> Pour effectuer une ingestion **à partir d’un conteneur**, consultez [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md#select-an-ingestion-type).

## <a name="edit-the-schema"></a>Modifier le schéma

Sélectionnez **Modifier le schéma** pour afficher et modifier la configuration de colonne de votre table.

### <a name="map-columns"></a>Mapper les colonnes 

1. Dans la boîte de dialogue **Mapper les colonnes** qui s’ouvre, vous pouvez attacher un ou plusieurs attributs ou colonnes sources à vos colonnes Azure Data Explorer.
    * Les nouveaux mappages sont définis automatiquement. Vous pouvez également utiliser un mappage existant. 
    * Dans les champs **Colonnes sources**, entrez les noms des colonnes à mapper aux **Colonnes cibles**.
    * Pour supprimer une colonne du mappage, sélectionnez l’icône de la corbeille.

      :::image type="content" source="media/one-click-ingestion-existing-table/map-columns.png" alt-text="Fenêtre Mapper les colonnes"::: 
    
1. Sélectionnez **Update**.
1. Sous l’onglet **Schéma** :
    * Le **type de compression** est sélectionné automatiquement par le nom de fichier source. Dans ce cas, le type de compression est **JSON**.
        
    * Quand vous sélectionnez **JSON**, vous devez également sélectionner **Niveaux JSON**, entre 1 et 10. Les niveaux déterminent la division des données dans les colonnes de la table.

        :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="Sélectionner un des niveaux JSON":::
    
       > [!TIP]
       > Si vous souhaitez utiliser des fichiers **CSV**, consultez [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md#edit-the-schema).

### <a name="table"></a>Table de charge de travail 

Dans le tableau : 
  * Sélectionnez les nouveaux en-têtes de colonne pour ajouter une **Nouvelle colonne**, **Supprimer la colonne**, **Trier par ordre croissant** ou **Trier par ordre décroissant**. 
 * Sur les colonnes existantes, seul le tri des données est disponible.

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Démarrer l’ingestion

Sélectionnez **Démarrer l’ingestion** pour créer une table et un mappage et pour commencer l’ingestion de données.

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="Démarrer l’ingestion":::

## <a name="data-ingestion-completed"></a>Ingestion de données terminée

Dans la fenêtre **Ingestion de données terminée**, les trois étapes sont signalées par des coches vertes quand l’ingestion des données s’est terminée avec succès.

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="Ingestion en un clic terminée":::

> [!IMPORTANT]
> Pour configurer l’ingestion continue à partir d’un conteneur, consultez [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)

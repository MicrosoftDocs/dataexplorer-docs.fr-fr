---
title: Utiliser l’ingestion en un clic pour ingérer des données dans une table Azure Data Explorer existante
description: Ingestion (chargement) simple de données dans une table Azure Data Explorer existante, au moyen de l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: a26e48520f5cc577a52f877da16996df0d03d2b8
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374359"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-an-existing-table-in-azure-data-explorer"></a>Utiliser l’ingestion en un clic pour ingérer des données dans une table existante d’Azure Data Explorer

L’ingestion en un clic vous permet d’ingérer rapidement dans une table des données au format JSON, CSV et dans d’autres formats. À l’aide de l’interface utilisateur web d’Azure Data Explorer, vous pouvez ingérer des données à partir du stockage, d’un fichier local ou d’un conteneur. 

Ce document décrit l’utilisation de l’Assistant intuitif Ingestion en un clic pour ingérer des données JSON depuis un fichier dans une table existante. Vous pouvez ensuite modifier la table et exécuter des requêtes avec l’interface utilisateur web d’Azure Data Explorer.

L’ingestion en un clic est particulièrement utile lorsque vous procédez à l’ingestion de données pour la première fois, ou lorsque le schéma de vos données ne vous est pas familier. 

Pour obtenir une vue d’ensemble de l’ingestion en un clic, et la liste des prérequis, consultez [Ingestion en un clic](ingest-data-one-click.md).
Pour plus d’informations sur l’ingestion de données dans une nouvelle table d’Azure Data Explorer, consultez [Ingestion en un clic dans une nouvelle table](one-click-ingestion-new-table.md).

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

1. Dans le menu de gauche de l’interface utilisateur web, cliquez avec le bouton droit sur une *base de données* ou une *table*, puis sélectionnez **Ingérer de nouvelles données (préversion)** .

    ![Sélectionner l'ingestion en un clic dans l'interface utilisateur web](media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png)   
 
1. Dans la fenêtre **Ingérer de nouvelles données (préversion)** , l’onglet **Source** est sélectionné.

1. Si le champ **Table** n’est pas renseigné automatiquement, sélectionnez un nom de table existant dans le menu déroulant.
    > [!TIP]
    > Si vous sélectionnez **Ingérer de nouvelles données (préversion)** sur une ligne de *table*, le nom de la table sélectionnée s’affiche dans les **Détails du projet**.

[!INCLUDE [data-explorer-one-click-ingestion-types](includes/data-explorer-one-click-ingestion-types.md)]
    
Sélectionnez **Modifier le schéma** pour afficher et modifier la configuration de colonne de votre table.

## <a name="edit-the-schema"></a>Modifier le schéma

1. La boîte de dialogue **Mapper les colonnes** s’ouvre et vous pouvez mapper les colonnes de données sources aux colonnes de la table cible. 
    * Dans les champs **Colonnes sources**, entrez les noms des colonnes à mapper aux **Colonnes cibles**.
    * Pour supprimer une colonne du mappage, sélectionnez l’icône de la corbeille.

    ![Fenêtre Mapper les colonnes](media/one-click-ingestion-existing-table/map-columns.png)

1. Sélectionnez **Update**.
1. Sous l’onglet **Schéma** :
    1. Le **type de compression** est sélectionné automatiquement par le nom de fichier source.

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](includes/data-explorer-one-click-ingestion-edit-schema.md)]
        
    1. Si vous sélectionnez **JSON**, vous devez également sélectionner **Niveaux JSON**, entre 1 et 10. Les niveaux affectent la représentation des données de colonne de la table.

    ![Sélectionner un des niveaux JSON](media/one-click-ingestion-existing-table/json-levels.png)

    * Pour les formats tabulaires, cochez la case **Inclure les noms des colonnes** pour ignorer la ligne d’en-tête du fichier.
        
    ![Sélectionner Inclure les noms des colonnes](media/one-click-ingestion-existing-table/non-json-format.png)

    > [!Note]
    > Les formats tabulaires peuvent ingérer des données de colonne dans une seule colonne de table Azure Data Explorer. 

    * Les nouveaux mappages sont définis automatiquement, mais vous pouvez modifier cette option pour utiliser un mappage existant. 
    * Sélectionnez le bouton **Mapper les colonnes** pour ouvrir la fenêtre **Mapper les colonnes**. Dans la fenêtre **Mapper les colonnes**, vous pouvez attacher un ou plusieurs attributs ou colonnes sources à vos colonnes Azure Data Explorer.

## <a name="copy-and-paste-queries"></a>Copier et coller des requêtes

1. Au-dessus du volet **Éditeur**, sélectionnez le bouton **v** pour ouvrir l’éditeur. Dans l’éditeur, vous pouvez afficher et copier les commandes automatiques générées à partir de vos entrées. 
1. Dans le tableau : 
    * Sélectionnez les nouveaux en-têtes de colonne pour ajouter une **Nouvelle colonne**, **Supprimer la colonne**, **Trier par ordre croissant** ou **Trier par ordre décroissant**. Sur les colonnes existantes, seul le tri des données est disponible.

    > [!Note]
    > * Vous ne pouvez pas mettre à jour le nom et le type de données des tables existantes.
    > * Les commandes Drop annulent uniquement les modifications apportées par ce flux d’ingestion (nouvelles étendues et colonnes). Aucune autre chose ne sera supprimée.

[![](media/one-click-ingestion-existing-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-existing-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Démarrer l’ingestion

Sélectionnez **Démarrer l’ingestion** pour créer une table et un mappage et pour commencer l’ingestion de données.

![Démarrer l’ingestion](media/one-click-ingestion-existing-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Ingestion de données terminée

Dans la fenêtre **Ingestion de données terminée**, les trois étapes sont signalées par des coches vertes si l'ingestion des données s'est terminée avec succès.
 
![Ingestion de données en un clic terminée](media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)

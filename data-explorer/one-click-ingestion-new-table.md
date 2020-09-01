---
title: Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer
description: Ingestion (chargement) simple de données dans une nouvelle table Azure Data Explorer, au moyen de l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: b9fa22a5cc3831ae2763c18dbfae69e24fd29318
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872843"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer

L’[ingestion en un clic](ingest-data-one-click.md) vous permet d’ingérer rapidement dans une table des données au format JSON, CSV et dans d’autres formats et de créer facilement des structures de mappage. Les données peuvent être ingérées à partir du stockage, d’un fichier local ou d’un conteneur, en tant que processus d’ingestion unique ou continu.  

Ce document décrit l’utilisation de l’Assistant intuitif Ingestion en un clic dans un cas d’usage spécifique pour ingérer des données **CSV** depuis un **conteneur** dans une **nouvelle table**. Vous pouvez utiliser le même processus avec de légères adaptations pour couvrir divers cas d’usage.

Pour obtenir une vue d’ensemble de l’ingestion en un clic, et la liste des prérequis, consultez [Ingestion en un clic](ingest-data-one-click.md).
Pour plus d’informations sur l’ingestion de données dans une table existante d’Azure Data Explorer, consultez [Ingestion en un clic dans une table existante](one-click-ingestion-existing-table.md).

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

1. Dans le menu de gauche de l’interface utilisateur web, cliquez avec le bouton droit sur une *base de données*, puis sélectionnez **Ingérer de nouvelles données (préversion)** .

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="Ingérer de nouvelles données":::
 
1. Dans la fenêtre **Ingérer de nouvelles données (préversion)** , l’onglet **Source** est sélectionné. 

1. Sélectionnez **Créer une table** et entrez un nom pour la nouvelle table. Vous pouvez utiliser des caractères alphanumériques, des traits d’union et des traits de soulignement. Les caractères spéciaux ne sont pas pris en charge.

    > [!NOTE]
    > Les noms de table doivent comprendre entre 1 et 1024 caractères.

    :::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="Créer une table à l’aide de l’ingestion en un clic":::

## <a name="select-an-ingestion-type"></a>Sélectionner un type d’ingestion

Sous **Type d’ingestion**, effectuez les étapes suivantes :
   
  1. Sélectionnez **à partir d’un conteneur**. 
  1. Dans le champ **Lien vers le stockage**, ajoutez l’[URL SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) du conteneur, puis entrez éventuellement la taille de l’échantillon.

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="Ingestion en un clic à partir d’un conteneur":::

     > [!TIP] 
     > Pour effectuer une ingestion **à partir d’un fichier**, consultez [Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer](one-click-ingestion-existing-table.md#select-an-ingestion-type).

Un échantillon des données s’affiche. Si vous le souhaitez, vous pouvez les filtrer pour ingérer uniquement les fichiers qui commencent ou qui se terminent par des caractères spécifiques. Quand vous ajustez les filtres, l’aperçu est mis à jour automatiquement.
  
 * Par exemple, vous pouvez filtrer tous les fichiers qui commencent par le mot *data* (données) et qui se terminent par l’extension  *.csv.gz*.

    :::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="Filtre de l’ingestion en un clic":::
  
## <a name="edit-the-schema"></a>Modifier le schéma

Sélectionnez **Modifier le schéma** pour afficher et modifier la configuration de colonne de votre table. Le système sélectionne un des objets blob au hasard, et le schéma est généré en fonction de cet objet blob. En examinant le nom de la source, le service identifie automatiquement s’il est compressé ou non.

### <a name="schema-tab"></a>Onglet Schéma

1. Sous l’onglet **Schéma** :

    * Sélectionnez **Format de données**.

        Dans ce cas, le format de données est **CSV**.

        > [!TIP]
        > Si vous souhaitez utiliser des fichiers **JSON**, consultez [Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer](one-click-ingestion-existing-table.md#edit-the-schema).

    * Vous pouvez cocher la case **Inclure les noms des colonnes** pour ignorer la ligne d’en-tête du fichier.

        :::image type="content" source="media/one-click-ingestion-new-table/non-json-format.png" alt-text="Sélectionner Inclure les noms des colonnes":::

1. Dans le champ **Nom du mappage**, entrez un nom de mappage. Vous pouvez utiliser des caractères alphanumériques et des traits de soulignement. Les espaces, les caractères spéciaux et les traits d’Union ne sont pas pris en charge.

    :::image type="content" source="media/one-click-ingestion-new-table/table-mapping.png" alt-text="Nom du mappage de table - Ingestion en un clic":::

### <a name="table"></a>Table de charge de travail

Dans le tableau : 
 * Double-cliquez sur le nom de la nouvelle colonne à modifier.
 * Sélectionnez de nouveaux en-têtes de colonnes et effectuez une des actions suivantes :

    [!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

    > [!NOTE]
    > Pour les formats tabulaires, chaque colonne peut être ingérée dans une colonne dans Azure Data Explorer.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Démarrer l’ingestion

Sélectionnez **Démarrer l’ingestion** pour créer une table et un mappage et pour commencer l’ingestion de données.

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="Démarrer l’ingestion - Ingestion en un clic":::

## <a name="data-ingestion-completed"></a>Ingestion de données terminée

Dans la fenêtre **Ingestion de données terminée**, les trois étapes sont signalées par des coches vertes quand l’ingestion des données s’est terminée avec succès.

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="Ingestion en un clic terminée"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="create-continuous-ingestion-for-container"></a>Créer une ingestion continue pour un conteneur

L’ingestion continue vous permet de créer une grille d’événement qui recherche les nouveaux fichiers dans le conteneur source. Tout nouveau fichier qui répond aux critères des paramètres prédéfinis (préfixe, suffixe, etc.) est automatiquement ingéré dans la table de destination. 

1. Sélectionnez le bouton **Créer une ingestion continue** dans le coin inférieur droit pour ouvrir le portail Azure. La page de connexion des données s’affiche avec le connecteur de données de la grille d’événement ouvert, et les paramètres source et cible déjà entrés (conteneur source, tables et mappages).
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="Bouton de création d’ingestion continue":::

1. Sélectionnez **Créer** pour créer une connexion de données qui écoute toutes les modifications, mises à jour ou nouvelles données dans ce conteneur. 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="Créer une connexion de hub d’événements":::

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)

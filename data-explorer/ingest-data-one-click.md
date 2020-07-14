---
title: Utiliser l’ingestion en un clic pour ingérer des données dans Azure Data Explorer
description: Vue d’ensemble de l’ingestion (chargement) simple de données dans Azure Data Explorer, au moyen de l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 98cfbf8b196d0496b4c7e86b03d6d2787ba6919f
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188453"
---
# <a name="what-is-one-click-ingestion"></a>Présentation de l’ingestion en un clic

L’ingestion en un clic rend le processus d’ingestion de données simple, rapide et intuitif. L’ingestion en un clic vous aide à ingérer des données, créer des tables de base de données et mapper des structures rapidement. Sélectionnez des données de différents types de sources dans des formats différents, qu’il s’agisse d’un processus d’ingestion unique ou continu.

Les fonctionnalités suivantes rendent l’ingestion en un clic particulièrement utile :

* Expérience intuitive guidée par l’Assistant Ingestion
* Ingestion des données en quelques minutes
* Ingestion des données de différents types de sources : fichier local, objets blob et conteneurs (jusqu’à 10 000 objets blob)
* Ingestion des données dans divers [formats](#file-formats)
* Ingestion des données dans des tables nouvelles ou existantes
* Schéma et mappage de table suggérés et faciles à changer
* Ingestion continue aisée et rapide à partir d’un conteneur avec Event Grid

L’ingestion en un clic est particulièrement utile lorsque vous procédez à l’ingestion de données pour la première fois, ou lorsque le schéma de vos données ne vous est pas familier.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).
* Connectez-vous à [l’interface utilisateur web Azure Data Explorer](https://dataexplorer.azure.com/) et [ajoutez une connexion à votre cluster](web-query-data.md#add-clusters).

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

L’Assistant Ingestion en un clic vous guide tout au long du processus d’ingestion en un clic.

* Pour accéder à l’Assistant Ingestion en un clic à partir de l’écran d’accueil **Bienvenue dans Azure Data Explorer** de votre cluster, effectuez les deux premières étapes ([création de cluster et création de base de données](#prerequisites)), puis sélectionnez **Ingérer de nouvelles données**.

    :::image type="content" source="media/ingest-data-one-click/welcome-ingestion.png" alt-text="Ingérer de nouvelles données à partir de l’écran de bienvenue dans Azure Data Explorer":::

* Pour accéder à l’Assistant à partir de l’[interface utilisateur web d’Azure Data Explorer](https://dataexplorer.azure.com/), dans le menu de gauche, cliquez avec le bouton droit sur la ligne de **base de données** ou de **table**, puis sélectionnez **Ingérer de nouvelles données (préversion)** .

    :::image type="content" source="media/ingest-data-one-click/one-click-ingestion-in-webui.png" alt-text="Sélectionner l’ingestion en un clic dans l’interface utilisateur web":::

<!-- TODO either change the local file tutorial to blob storage or create another one to show users how to do this-->

## <a name="one-click-ingestion-wizard"></a>Assistant Ingestion en un clic

> [!NOTE]
> Cette section décrit l’Assistant de façon générale. Les options que vous sélectionnez dépendent du format de données que vous ingérez, du type de source de données à partir duquel vous effectuez l’ingestion et de la destination de celle-ci (table nouvelle ou existante).
>
> Pour obtenir des exemples de scénarios, consultez :
> * Ingestion dans [une nouvelle table à partir d’un conteneur au format CSV](one-click-ingestion-new-table.md)
> * Ingestion dans une [table existante à partir d’un fichier local au format JSON](one-click-ingestion-existing-table.md) 

L’Assistant vous guide dans les options suivantes :
   * Ingérer dans une [table existante](one-click-ingestion-existing-table.md)
   * Ingérer dans une [nouvelle table](one-click-ingestion-new-table.md)
   * Ingérer des données depuis un :
      * Stockage d'objets blob
      * [Fichier local](one-click-ingestion-existing-table.md)
      * [Conteneur](one-click-ingestion-new-table.md)


### <a name="schema-mapping"></a>Mappage de schéma

Le service génère automatiquement les propriétés de schéma et d’ingestion, que vous pouvez changer. Vous pouvez utiliser une structure de mappage existante ou en créer une, selon que l’ingestion est destinée à une table existante ou nouvelle.

Sous l’onglet **Schéma**, effectuez les actions suivantes :
   * Vérifier le type de compression généré automatiquement
   * Choisir le [format de vos données](#file-formats) Les formats différents vous permettront d’apporter des modifications supplémentaires.

#### <a name="file-formats"></a>Formats de fichiers

L’ingestion en un clic prend en charge l’ingestion d’une nouvelle table à partir de données sources, dans un des formats suivants :
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

### <a name="editor-window"></a>Fenêtre Éditeur

Dans la fenêtre **Éditeur**, vous pouvez ajuster les colonnes de la table de données, si nécessaire. 

|Type de la table  |Ajustements de colonne disponibles  |
|---------|---------|
|Nouveau     | Nouvelle colonne, Supprimer la colonne, Tri croissant, Tri décroissant  |
|Existing     | Nouvelle colonne, Tri croissant, Tri décroissant  |

>[!NOTE]
> À tout moment, vous pouvez ouvrir l’[éditeur de commande](one-click-ingestion-new-table.md#command-editor) au-dessus du volet **Éditeur**. Dans l’éditeur de commande, vous pouvez afficher et copier les commandes automatiques générées à partir de vos entrées.

### <a name="data-ingestion"></a>Ingestion de données

Une fois que vous avez terminé le mappage de schéma et les manipulations de colonnes, l’Assistant Ingestion démarre le processus d’ingestion de données. 

* Quand l’ingestion de données provient de sources **autres que des conteneurs**, l’ingestion prend effet immédiatement.

* Si la source de données est un **conteneur** :
    * La [stratégie de traitement par lot](kusto/management/batchingpolicy.md) d’Azure Data Explorer agrège vos données. 
    * Après l’ingestion, vous pouvez télécharger le rapport d’ingestion et passer en revue les performances de chaque objet blob qui a été traité. 
    * Vous pouvez sélectionner **Créer une ingestion continue** et configurer l’[ingestion continue à l’aide d’Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container).
 
### <a name="initial-data-exploration"></a>Exploration initiale des données
   
Une fois l’ingestion terminée, vous pouvez effectuer une exploration initiale de vos données à l’aide des **[commandes rapides](one-click-ingestion-existing-table.md#quick-queries-and-tools)** que l’Assistant met à votre disposition.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser l’ingestion en un clic pour ingérer des données JSON à partir d’un fichier local dans une table existante d’Azure Data Explorer](one-click-ingestion-existing-table.md)
* [Utiliser l’ingestion en un clic pour ingérer des données CSV à partir d’un conteneur dans une nouvelle table d’Azure Data Explorer](one-click-ingestion-new-table.md)
* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)

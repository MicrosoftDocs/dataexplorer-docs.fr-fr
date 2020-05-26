---
title: Utiliser l’ingestion en un clic pour ingérer des données dans Azure Data Explorer
description: Vue d’ensemble de l’ingestion (chargement) simple de données dans Azure Data Explorer, au moyen de l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 16db3eb2d925d12ba8eefa605b27566e9b4c1b04
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373815"
---
# <a name="what-is-one-click-ingestion"></a>Présentation de l’ingestion en un clic 

L’ingestion en un clic vous permet d’ingérer rapidement des données et de suggérer automatiquement tables et structures de mappage, en fonction d’une source de données dans Azure Data Explorer. 

À l’aide de l’interface utilisateur web d’Azure Data Explorer, vous pouvez ingérer des données à partir du stockage (fichier d’objets blob), d’un fichier local ou d’un conteneur (jusqu’à 10 000 objets blob). Vous pouvez également définir une grille d’événement sur un conteneur pour l’ingestion continue. Les données peuvent être ingérées dans une table existante ou nouvelle, au format JSON, CSV et dans d’[autres formats](#file-formats). Selon la source de données, l’ingestion en un clic peut proposer une structure pour une nouvelle table et un mappage de table, et fournir une plateforme intuitive permettant d’ajuster le mappage et la structure d’une table nouvelle ou existante. L’ingestion en un clic permet d’ingérer les données dans la table en quelques minutes seulement.

L’ingestion en un clic est particulièrement utile lorsque vous procédez à l’ingestion de données pour la première fois, ou lorsque le schéma de vos données ne vous est pas familier.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).
* Connectez-vous à [l’interface utilisateur web Azure Data Explorer](https://dataexplorer.azure.com/) et [ajoutez une connexion à votre cluster](web-query-data.md#add-clusters).

## <a name="file-formats"></a>Formats de fichiers

L’ingestion en un clic prend en charge l’ingestion d’une nouvelle table à partir de données sources, dans un des formats suivants :
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>Assistant Ingestion en un clic

L’Assistant Ingestion en un clic vous guide tout au long du processus d’ingestion en un clic. 

> [!Note]
> Cette section décrit l’Assistant de façon générale. Les options que vous sélectionnez varient selon que vous procédez à une ingestion dans une table nouvelle ou existante. Pour plus d'informations, consultez les pages suivantes :
    > * Ingérer dans une [nouvelle table](one-click-ingestion-new-table.md)
    > * Ingérer dans une [table existante](one-click-ingestion-existing-table.md) 
    
1. Pour accéder à l’Assistant, dans le menu de gauche de l’interface utilisateur web d’Azure Data Explorer, cliquez avec le bouton droit sur la ligne de *base de données* ou de *table*, puis sélectionnez **Ingérer de nouvelles données (préversion)** .

    ![Sélectionner l'ingestion en un clic dans l'interface utilisateur web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. L’Assistant vous guide dans les options suivantes :
    * Ingérer dans une [table existante](one-click-ingestion-existing-table.md)
    * Ingérer dans une [nouvelle table](one-click-ingestion-new-table.md)
    * Ingérer des données depuis un :
      * Stockage d'objets blob
      * Fichier local
      * Conteneur
    * Entrez la taille de l’échantillon, comprise entre 1 et 10 000 lignes (à partir du conteneur uniquement)
       
1. Une fois la source de données correctement sélectionnée, un aperçu des données s’affiche. 
    Si vous ingérez des données à partir d’un conteneur, vous pouvez filtrer ces données afin que seuls les fichiers avec des préfixes ou des extensions de fichier spécifiques soient ingérés. Par exemple, vous pouvez souhaiter ingérer uniquement les fichiers dont les noms de fichier commencent par le mot *Europe*, ou uniquement les fichiers avec l’extension *.json*. 

1. Cliquez sur **Modifier le schéma**. Passer en revue et ajuster les propriétés de schéma et d’ingestion générées automatiquement (format de données, nom du mappage, etc.)

1. Démarrez le processus d’ingestion des données.

> [!Note]
> Si votre source de données est un conteneur, notez que la stratégie d’agrégation de données (traitement par lot) d’Azure Data Explorer est conçue pour optimiser le processus d’ingestion des données. Par défaut, la stratégie est configurée sur 5 minutes ou 500 Mo de données, ce qui peut entraîner une latence. Consultez la [stratégie de traitement par lot](kusto/management/batchingpolicy.md) pour les options d’agrégation. Lors de l’ingestion de données provenant d’autres sources, l’ingestion prend effet immédiatement.

## <a name="next-steps"></a>Étapes suivantes

* Décider si vous allez utiliser l’ingestion en un clic pour ingérer des données dans [une table existante](one-click-ingestion-existing-table.md) ou [une nouvelle table](one-click-ingestion-new-table.md)
* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)

---
title: Visualiser des données à l’aide du connecteur Azure Data Explorer pour Microsoft Excel
description: Dans cet article, vous allez apprendre à utiliser le connecteur Azure Data Explorer pour Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493669"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualiser des données à l’aide du connecteur Azure Data Explorer pour Excel

Azure Data Explorer offre deux options pour se connecter à des données dans Excel : utiliser le connecteur natif ou importer une requête depuis Azure Data Explorer. Cet article explique comment utiliser le connecteur natif dans Excel et se connecter au cluster Azure Data Explorer pour obtenir et visualiser des données.

Le connecteur natif Azure Data Explorer pour Excel permet d’exporter les résultats d’une requête vers Excel. Vous pouvez également ajouter une requête KQL comme source de données Excel pour des calculs ou des visualisations supplémentaires.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Définir la requête Kusto comme source de données Excel et charger les données dans Excel

1. Ouvrez **Microsoft Excel**.
1. Sous l’onglet **Données**, sélectionnez **Obtenir les données** > **À partir d’Azure** > **À partir d’Azure Data Explorer**.

    ![Obtenir des données auprès d’Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. Dans la fenêtre **Azure Data Explorer (Kusto)** , renseignez les champs suivants et sélectionnez **OK**.

    ![Fenêtre Azure Data Explorer (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Champ   |Description |
    |---------|---------|
    |**Cluster**   |   Nom du cluster (obligatoire)      |    
    |**Sauvegarde de la base de données**     |    Nom de base de données      |    
    |**Nom de la table ou requête Azure Data Explorer**    |     Nom de la table ou requête Azure Data Explorer    | 
    
    **Options avancées :**

     |Champ   |Description |
    |---------|---------|
    |**Limiter le nombre d’enregistrements dans les résultats de requête**     |     Limitez le nombre d’enregistrements chargés dans Excel  |    
    |**Limiter la taille des données en octets dans les résultats de requête**    |    Limitez la taille des données      |   
    |**Désactiver la troncation du jeu de résultats**    |         |      
    |**Instructions SET supplémentaires (séparées par des points-virgules)**    |    Ajoutez des instructions `set` à appliquer à la source de données     |   

1.  Dans le volet **Navigateur**, accédez à la table appropriée. Dans le volet d’aperçu de la table, sélectionnez **Transformer les données** pour apporter des modifications à vos données ou sélectionnez **Charger** pour les charger dans Excel.

![Fenêtre d’aperçu de la table](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Si **Base de données** et/ou **Nom de table ou requête Azure Data Explorer** sont déjà spécifiés, le volet d’aperçu de la table appropriée s’ouvre automatiquement. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analyser et visualiser les données dans Excel

Une fois que les données sont chargées dans Excel et disponibles dans votre feuille Excel, vous pouvez les analyser, les résumer et les visualiser en créant des relations et des visuels. 

1.  Sous l’onglet **Conception de tableau**, sélectionnez **Synthétiser avec un tableau croisé dynamique**. Dans la fenêtre **Créer un tableau croisé dynamique**, sélectionnez le tableau approprié et sélectionnez **OK**.

    ![Créer un tableau croisé dynamique](media/excel-connector/create-pivot-table.png)

1. Dans le volet **Champs de tableau croisé dynamique**, sélectionnez les colonnes de tableau appropriées pour créer des tables de résumé. Dans l’exemple ci-dessous, les champs **EventId** et **State** sont sélectionnés.
    
    ![Sélectionner des champs de tableau croisé dynamique](media/excel-connector/pivot-table-pick-fields.png)

1. Sous l’onglet **Analyse de tableau croisé dynamique**, sélectionnez **Graphique croisé dynamique** pour créer des visuels basés sur le tableau. 

    ![Graphique croisé dynamique](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. Dans l’exemple ci-dessous, utilisez **Event Id**, **StartTime** et **EventType** pour voir des informations supplémentaires sur les événements météorologiques.

    ![Visualiser les données](media/excel-connector/visualize-excel-data.png)

1. Créez des tableaux de bord complets pour surveiller vos données.

## <a name="next-steps"></a>Étapes suivantes

[Visualiser des données à l’aide d’une requête Kusto Azure Data Explorer importée dans Microsoft Excel](excel-blank-query.md)
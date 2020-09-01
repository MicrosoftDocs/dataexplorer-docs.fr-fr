---
title: Visualiser des données à l’aide d’une requête Kusto Azure Data Explorer importée dans Microsoft Excel
description: Dans cet article, vous allez apprendre à importer une requête Kusto Azure Data Explorer dans Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: 0c1900b1feb0af150e25fbc85c231f31a30663b0
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874628"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualiser des données à l’aide d’une requête Kusto Azure Data Explorer importée dans Microsoft Excel

Azure Data Explorer offre deux options pour se connecter à des données dans Excel : utiliser le connecteur natif ou importer une requête depuis Azure Data Explorer. Cet article explique comment importer une requête à partir d’Azure Data Explorer dans Excel pour visualiser des données. Ajoutez une requête Kusto comme source de données Excel pour effectuer des calculs ou des visualisations supplémentaires sur les données.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un compte e-mail professionnel qui est membre d’Azure Active Directory pour pouvoir vous connecter au [cluster help d’Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>or</br>
* Créez [un cluster et une base de données de test](create-cluster-database-portal.md) et connectez-vous à [l’application d’interface utilisateur web d’Azure Data Explorer](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Définir une requête Kusto en tant que source de données Excel

1. Dans l’[interface utilisateur web d’Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples), exécutez la requête et examinez les résultats.

1. Sélectionnez l’onglet **Partager** et **Requête vers Power BI**.

    ![Requête d’interface utilisateur web vers Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Une fenêtre s’affiche avec la notification suivante :

    ![Exporter la requête dans le Presse-papiers](media/excel-blank-query/query-exported-to-clipboard.png)

1. Ouvrez **Microsoft Excel**.

1. Dans l’onglet **Données**, sélectionnez **Obtenir les données** > **À partir d’autres sources** > **Requête vide**.

    ![Obtenir des données et sélectionner une requête vide](media/excel-blank-query/get-data-blank-query.png)

1. La fenêtre **Éditeur Power Query** s’ouvre. Dans cette fenêtre, sélectionnez **Éditeur avancé**.

    ![Fenêtre Éditeur Power Query](media/excel-blank-query/power-query-editor.png)

1. Dans la fenêtre **Éditeur avancé**, collez la requête que vous avez exportée dans le Presse-papiers, puis sélectionnez **Terminé**.

    ![Requête dans l’éditeur avancé](media/excel-blank-query/advanced-editor-query.png)    

1. Pour vous authentifier, sélectionnez **Modifier les informations d’identification**.

    ![Modifier les informations d’identification](media/excel-blank-query/edit-credentials.png)

1. Sélectionnez **Compte professionnel** et **Se connecter**. Terminez le processus de connexion, puis sélectionnez **Connexion**.

    ![Connexion](media/excel-blank-query/complete-sign-in.png)

    Répétez les étapes précédentes pour ajouter d’autres requêtes. Vous pouvez renommer les requêtes avec des noms plus explicites.

1. Sélectionnez le bouton **Fermer et charger** pour faire passer vos données dans Excel.

    ![Sélectionner Fermer et charger](media/excel-blank-query/close-and-load.png)

1. Vos données sont maintenant dans Excel. Sélectionnez le bouton **Actualiser** pour actualiser la requête.

    ![Afficher les données dans Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Étapes suivantes

[Visualiser des données à l’aide du connecteur Azure Data Explorer pour Excel](excel-connector.md)
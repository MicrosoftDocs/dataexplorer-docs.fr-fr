---
title: Utiliser le connecteur ODBC Azure Data Explorer pour visualiser les données de Tableau
description: Dans cet article, vous allez apprendre à utiliser une connexion ODBC (Open Database Connectivity) à Azure Data Explorer pour visualiser les données avec Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5fc98a0bb173bc926dc289b16c1aa8d1a0f5d9de
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494113"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualiser des données Azure Data Explorer dans Tableau

 [Tableau](https://www.tableau.com/) est une plateforme d’analyse visuelle de Business Intelligence. Pour vous connecter à Azure Data Explorer à partir de Tableau et introduire les données d’un exemple de cluster, utilisez le pilote SQL Server ODBC (Open Database Connectivity). 

## <a name="prerequisites"></a>Conditions préalables requises

Vous avez besoin des éléments suivants dans le cadre de cet article :

* [Connectez-vous à Azure Data Explorer avec ODBC](connect-odbc.md) à l’aide du pilote SQL Server ODBC, pour vous connecter à Azure Data Explorer à partir de Tableau. 

* Tableau Desktop, version complète ou d’[évaluation](https://www.tableau.com/products/desktop/download).

* Un cluster qui inclut l’exemple de données StormEvents. Pour plus d’informations, consultez [Créer un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md) et [Ingérer des exemples de données dans l’Explorateur de données Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualiser des données dans Tableau 

Une fois que vous avez terminé la configuration d’ODBC, vous pouvez introduire des exemples de données dans Tableau.

1. Dans Tableau Desktop, dans le menu de gauche, sélectionnez **Other Databases (ODBC) (Autres bases de données (ODBC))** .

    ![Se connecter à ODBC](media/tableau/connect-odbc.png)

1. Pour **DSN**, sélectionnez la source de données que vous avez créée pour ODBC, puis sélectionnez **Sign In (Se connecter)** .

    ![Connexion à ODBC](media/tableau/odbc-sign-in.png)

1. Pour **Database (Base de données)** , sélectionnez la base de données figurant dans votre exemple de cluster, telle que *TestDatabase*. Pour **Schéma**, sélectionnez *dbo* et pour **Table**, sélectionnez l’exemple de table *StormEvents*.

    ![Sélectionner une base de données et une table](media/tableau/select-database-table.png)

1. Tableau affiche maintenant le schéma pour les exemples de données. Sélectionnez **Update Now (Mettre à jour maintenant)** pour introduire les données dans Tableau.

    ![Mettre à jour des données](media/tableau/update-data.png)

    Lorsque les données sont importées, Tableau affiche des lignes de données similaires à l’image suivante.

    ![Jeu de résultats](media/tableau/result-set.png)

1. Vous pouvez désormais créer des visualisations dans Tableau en fonction des données introduites à partir d’Azure Data Explorer. Pour plus d'informations, consultez [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)
---
title: Connexion à Azure Data Explorer avec ODBC
description: Dans cet article, vous allez apprendre à configurer une connexion ODBC (Open Database Connectivity) à Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 156257613195cb53730273ac3a654908feac9601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492725"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Connexion à Azure Data Explorer avec ODBC

L’interface Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) est une interface de programmation d’applications (API) largement acceptée pour l’accès à des bases de données. Utilisez ODBC pour vous connecter à Azure Data Explorer à partir d’applications qui ne sont associées à aucun connecteur dédié.

Dans les coulisses, les applications appellent des fonctions dans l’interface ODBC, qui sont implémentées dans des modules spécifiques à la base de données appelés *pilotes*. Azure Data Explorer Azure prend en charge un sous-ensemble du protocole de communication de SQL Server ([MS-TDS](kusto/api/tds/index.md)), et peut donc utiliser le pilote ODBC pour SQL Server.

La vidéo suivante vous apprend comment créer une connexion ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Vous pouvez également [configurer la source de données ODBC](#configure-the-odbc-data-source) comme indiqué ci-dessous. 

Cet article explique comment utiliser le pilote SDBC de SQL Server pour se connecter à Azure Data Explorer depuis n’importe quelle application prenant ce pilote en charge. 

## <a name="prerequisites"></a>Conditions préalables requises

Vous avez besoin des éléments suivants :

* [Microsoft ODBC Driver for SQL Server version 17.2.0.1 ou ultérieure](/sql/connect/odbc/download-odbc-driver-for-sql-server) pour votre système d’exploitation.

## <a name="configure-the-odbc-data-source"></a>Configurer la source de données ODBC

Suivez ces étapes pour configurer une source de données ODBC à l’aide du pilote ODBC pour SQL Server.

1. Dans Windows, recherchez *Sources de données ODBC*, puis ouvrez l’application de bureau Sources de données ODBC.

1. Sélectionnez **Ajouter**.

    ![Ajouter une source de données](media/connect-odbc/add-data-source.png)

1. Sélectionnez **ODBC Driver 17 for SQL Server** et cliquez sur **Terminer**.

    ![Sélectionner un pilote](media/connect-odbc/select-driver.png)

1. Saisissez un nom et une description pour la connexion et le cluster auquel que vous souhaitez vous connecter, puis sélectionnez **Suivant**. L’URL du cluster doit avoir le format suivant : *\<NomCluster\>.\<Région\>.kusto.windows.net*.

    ![Sélectionner un serveur](media/connect-odbc/select-server.png)

1. Cliquez sur **Intégration Active Directory**, puis sur **Suivant**.

    ![Intégration Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Sélectionnez la base de données avec les exemples de données, puis cliquez sur **Suivant**.

    ![Modifier la base de données par défaut](media/connect-odbc/change-default-database.png)

1. Dans l’écran suivant, conservez toutes les valeurs par défaut des options et sélectionnez **Terminer**.

1. Sélectionnez **Tester la source de données**.

    ![Tester la source de données](media/connect-odbc/test-data-source.png)

1. Vérifiez que le test a réussi, puis sélectionnez **OK**. Si le test a échoué, vérifiez les valeurs que vous avez spécifiées lors des étapes précédentes et assurez-vous que vous disposez des autorisations suffisantes pour vous connecter au cluster.

    ![Test réussi](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à Azure Data Explorer depuis Tableau](tableau.md)
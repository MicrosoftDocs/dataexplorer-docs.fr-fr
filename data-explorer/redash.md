---
title: Visualiser des données Azure Data Explorer avec Redash
description: Dans cet article, vous allez apprendre à visualiser des données dans Azure Data Explorer avec le connecteur natif Redash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: 679e3fd4f9b065229951f570101b931a7cb091ea
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874509"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualiser des données Azure Data Explorer dans Redash

[Redash](https://redash.io/) connecte et interroge vos sources de données, et génère des tableaux de bord pour visualiser les données et les partager avec des pairs. Dans cet article, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Redash, puis à visualiser les données.

## <a name="prerequisites"></a>Prérequis

1. [Créez un cluster et une base de données](create-cluster-database-portal.md).
1. Ingérez des données comme indiqué dans la section [Ingérer des exemples de données dans l’Explorateur de données Azure](ingest-sample-data.md). Pour accéder à davantage d’options d’ingestion, consultez la section de [présentation de l’ingestion](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Créer un connecteur Azure Data Explorer dans Redash 

1. Connectez-vous à [Redash](https://redash.io/). Sélectionnez **Get Started** pour créer un compte.
1. Sous **Let’s get started**, sélectionnez **Connect a Data Source**.

    ![Connexion d’une source de données](media/redash/connect-data-source.png)

1. Dans la fenêtre **Create a New Data Source**, sélectionnez **Azure Data Explorer (Kusto)** , puis **Create**. 

    ![Sélection d’une source de données Azure Data Explorer](media/redash/select-adx-data-source.png)

1. Dans la fenêtre **Azure Data Explorer (Kusto)** , renseignez le formulaire suivant et sélectionnez **Create**.

    ![Fenêtre de paramètres Azure Data Explorer (Kusto)](media/redash/adx-settings-window.png)

1. Dans la fenêtre **Settings**, cliquez sur **Save** et **Test Connection** pour tester la connexion à la source de données **Azure Data Explorer (Kusto)** .

## <a name="create-queries-in-redash"></a>Créer des requêtes dans Redash

1. Sur la partie supérieure gauche de Redash, cliquez sur **Create** > **Query**. Cliquez sur **New Query** et renommez la requête.

    ![Créer une requête](media/redash/create-query.png)

1. Saisissez votre requête dans le volet d’édition supérieur, puis sélectionnez **Save** et **Execute**. Sélectionnez **Publish** pour publier une requête en vue d’une réutilisation ultérieure.

    ![Enregistrer et exécuter une requête](media/redash/save-and-execute-query.png)

    Dans le volet gauche, vous pouvez voir le nom de connexion de la source de données (**connecteur GitHub** dans notre flux) dans le menu déroulant et les tables de la base de données sélectionnée. 

1. Affichez les résultats de la requête dans le volet central inférieur. Créez une visualisation à associer à la requête en sélectionnant le bouton **New Visualization**.

    ![Nouvelle visualisation](media/redash/new-visualization.png)

1. Dans l’écran de la visualisation, sélectionnez **Visualization Type**, ainsi que les champs pertinents, comme **X Column** et **Y Column**. **Enregistrez** la visualisation.

    ![Configurer et enregistrer la visualisation](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Créer une requête à l’aide d’un paramètre

1. Sélectionnez **Create** > **Query** pour créer une requête. Ajoutez-lui un paramètre à l’aide d’accolades {{}}. Sélectionnez **{{}}** pour ouvrir la fenêtre **Add Parameter**. Vous pouvez également sélectionner *l’icône des paramètres* pour modifier les attributs d’un paramètre existant et ouvrir la fenêtre **<nom_paramètre>** . 

    ![Insérer un paramètre](media/redash/insert-parameter.png)

1. Nommez votre paramètre. Sélectionnez **Type** : **Query Based Dropdown List** dans le menu déroulant. Sélectionnez **OK**.

    ![Query Based Dropdown List](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > La requête utilise plusieurs valeurs. Par conséquent, vous devez inclure la syntaxe suivante : `| where Type in ((split('{{Type}}', ',')))`. Pour plus d’informations, voir la section relative à [l’opérateur IN](kusto/query/inoperator.md). Cela aboutit à la création de [plusieurs options de paramètre de requête dans l’application Redash](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io).

## <a name="create-a-dashboard-in-redash"></a>Créer un tableau de bord dans Redash

1. Pour créer votre tableau de bord, cliquez sur **Create** > **Dashboard**. Vous pouvez aussi sélectionner un tableau de bord existant, puis cliquer sur **Dashboards** et sélectionner un tableau de bord dans la liste.

    ![Créer un tableau de bord](media/redash/create-dashboard.png)

1. Dans la fenêtre **New Dashboard**, donnez un nom à votre tableau de bord et cliquez sur **Save**. Dans la fenêtre **<nom_tableau_de_bord>** , sélectionnez **Add Widget** pour créer un widget. 

1. Dans la fenêtre **Add Widget**, sélectionnez un nom de requête, **Choose Visualization** et **Parameters**. Sélectionnez **Add to Dashboard**.

   ![Choisir des visualisations et les ajouter au tableau de bord](media/redash/add-widget-window.png)

1. Sélectionnez **Done Editing** pour terminer la création du tableau de bord.

1.  Dans le mode d’édition du tableau de bord, sélectionnez **Use Dashboard Level Filters** pour utiliser le paramètre **Type** défini précédemment.

    ![Terminer la création du tableau de bord](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)



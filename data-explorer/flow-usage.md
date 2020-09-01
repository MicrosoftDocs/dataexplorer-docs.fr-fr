---
title: Exemples d’utilisation du connecteur Azure Data Explorer vers Power Automate (préversion)
description: Découvrez des exemples d’utilisation courants du connecteur Azure Data Explorer vers Power Automate.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/15/2020
ms.openlocfilehash: a9f2be17e02103a64fa31a10bc6195076addb1fc
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874526"
---
# <a name="usage-examples-for-azure-data-explorer-connector-to-power-automate-preview"></a>Exemples d’utilisation du connecteur Azure Data Explorer vers Power Automate (préversion)

Le connecteur de flux Azure Data Explorer permet à Azure Data Explorer d’utiliser les fonctionnalités de flux de [Microsoft Power Automate](https://flow.microsoft.com/). Vous pouvez exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée. Cet article comprend plusieurs exemples d’utilisation courants du connecteur de flux.

Pour plus d’informations, consultez [Connecteur de flux Azure Data Explorer (préversion)](flow.md).

## <a name="flow-connector-and-your-sql-database"></a>Connecteur de flux et votre base de données SQL

Utilisez le connecteur de flux pour interroger vos données et les agréger dans une base de données SQL.

> [!Note]
> Utilisez le connecteur de flux uniquement pour de petites quantités de données de sortie. L’opération d’insertion SQL est effectuée individuellement pour chaque ligne. 

![Capture d’écran de l’interrogation de données à l’aide du connecteur de flux](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> Dans le champ **Nom du cluster**, entrez l’URL du cluster.

## <a name="push-data-to-a-microsoft-power-bi-dataset"></a>Envoyer par push des données à un jeu de données Microsoft Power BI

Vous pouvez utiliser le connecteur de flux avec le connecteur Power BI pour envoyer par push des données issues de requêtes Kusto vers des jeux de données de streaming Power BI.

1. Créez une action **Exécuter la requête et répertorier les résultats**.
1. Sélectionnez **Nouvelle étape**.
1. Sélectionnez **Ajouter une action**, puis recherchez Power BI.
1. Sélectionnez **Power BI** > **Ajouter des lignes à un jeu de données**. 

    ![Capture d’écran du connecteur Power BI](./media/flow-usage/flow-powerbiconnector.png)

1. Indiquez **l’Espace de travail**, le **Jeu de données** et la **Table** dans lesquels les données seront envoyées.
1. Dans la boîte de dialogue de contenu dynamique, ajoutez une **charge utile** qui contient votre schéma de jeu de données et les résultats de requête Kusto pertinents.

    ![Capture d’écran des champs Power BI](./media/flow-usage/flow-powerbifields.png)

Le flux applique automatiquement l’action Power BI pour chaque ligne de la table des résultats de la requête Kusto. 

![Capture d’écran de l’action Power BI pour chaque ligne](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Requêtes conditionnelles

Vous pouvez utiliser les résultats des requêtes Kusto comme entrée ou conditions pour les actions de flux suivantes.

Dans l’exemple suivant, nous interrogeons Kusto pour les incidents qui se sont produits au cours du dernier jour. Pour chaque incident résolu, un message Slack est publié et une notification Push est créée.
Pour chaque incident toujours actif, nous interrogeons Kusto pour obtenir des informations supplémentaires sur des incidents similaires. Il envoie ces informations sous forme d’e-mail et ouvre une tâche associée dans Azure DevOps Server.

Pour créer un flux similaire, suivez ces instructions :

1. Créez une action **Exécuter la requête et répertorier les résultats**.
1. Sélectionnez **Nouvelle étape** > **Contrôle des conditions**.
1. Dans la fenêtre de contenu dynamique, sélectionnez le paramètre que vous voulez utiliser comme condition pour les actions suivantes.
1. Sélectionnez le type de *Relation* et la *Valeur* souhaités pour définir une condition spécifique sur le paramètre lui-même.

    :::image type="content" source="./media/flow-usage/flow-condition.png" alt-text="Utilisation de conditions de flux en fonction des résultats d’une requête Kusto pour déterminer l’action de flux suivante, Azure Data Explorer" lightbox="./media/flow-usage/flow-condition.png#lightbox":::

    Le flux applique cette condition sur chaque ligne de la table des résultats de requête.
1. Ajoutez des actions à effectuer lorsque la condition a la valeur true ou false.

    :::image type="content" source="./media/flow-usage/flow-conditionactions.png" alt-text="Ajout d’actions quand une condition a la valeur true ou false, conditions de flux basées sur les résultats de la requête Kusto, Azure Data Explorer" lightbox="./media/flow-usage/flow-conditionactions.png#lightbox":::

Vous pouvez utiliser les valeurs de résultat de la requête Kusto comme entrée pour les actions suivantes. Sélectionnez les valeurs de résultat dans la fenêtre de contenu dynamique.
Dans l’exemple suivant, nous ajoutons une action **Slack - Publier un message** et une action **Visual Studio - Créer un élément de travail** contenant des données issues de la requête Kusto.

![Capture d’écran de l’action Slack - Publier un message](./media/flow-usage/flow-slack.png)

![Capture d’écran de l’action Visual Studio](./media/flow-usage/flow-visualstudio.png)

Dans cet exemple, si un incident est toujours actif, réinterrogez Kusto pour obtenir des informations sur la façon dont les incidents de la même source ont été résolus dans le passé.

![Capture d’écran d’une requête de condition de flux](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> Dans le champ **Nom du cluster**, entrez l’URL du cluster.

Visualisez ces informations affichées sous forme de graphique à secteurs, puis envoyez-les par e-mail à l’équipe.

![Capture d’écran d’un e-mail de condition de flux](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Envoyer par e-mail plusieurs graphiques de flux Azure Data Explorer

1. Créez un flux avec le déclencheur de périodicité, puis définissez l’intervalle et la fréquence du flux. 
1. Ajoutez une nouvelle étape, avec une ou plusieurs actions **Kusto - Exécuter une requête et visualiser les résultats**. 

    ![Capture d’écran de l’exécution de plusieurs requêtes dans un flux](./media/flow-usage/flow-severalqueries.png)

1. Pour chaque action **Kusto - Exécuter une requête et visualiser les résultats**, définissez les champs suivants :
    * URL du cluster.
    * Nom de la base de données.
    * Requête et Type de graphique (par exemple, tableau HTML, graphique à secteurs, graphique de temps, graphique à barres ou une valeur personnalisée).

    ![Capture d’écran de la visualisation des résultats avec plusieurs pièces jointes](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Ajoutez une action **Envoyer un e-mail (V2)**  : 
    1. Dans le corps du texte, sélectionnez l’icône de visualisation du code.
    1. Dans le champ **Corps**, insérez le **BodyHtml** nécessaire afin que le résultat visualisé de la requête soit inclus dans le corps de l’e-mail.
    1. Pour ajouter une pièce jointe à l’e-mail, ajoutez le **Nom de la pièce jointe** et le **Contenu de la pièce jointe**.
    
    ![Capture d’écran de l’envoi par e-mail de plusieurs pièces jointes](./media/flow-usage/flow-email-multiple-attachments.png)

    Pour plus d’informations sur la création d’une action de messagerie, consultez [Envoyer par e-mail les résultats de requête Kusto](flow.md#email-kusto-query-results). 

Résultats :

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments.png" alt-text="Résultats de plusieurs pièces jointes, visualisés sous la forme d’un graphique à secteurs et d’un graphique à barres, Azure Data Explorer" lightbox="./media/flow-usage/flow-resultsmultipleattachments.png#lightbox":::

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments2.png" alt-text="Résultats de plusieurs pièces jointes, visualisés sous la forme d’un graphique chronologique, Azure Data Explorer" lightbox="./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez le [connecteur Logic Apps Azure Kusto](kusto/tools/logicapps.md), qui est une autre façon d’exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée.


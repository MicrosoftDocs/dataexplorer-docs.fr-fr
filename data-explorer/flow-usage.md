---
title: Exemples d’utilisation du connecteur Microsoft Azure Data Explorer Flow (préversion)
description: Découvrez quelques exemples courants d’utilisation du connecteur Microsoft Flow.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 05ff0e41347d56cc1e204defa8d5f250a881c84f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493445"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Exemples d’utilisation du connecteur Microsoft Flow (préversion)

Le connecteur Azure Data Explorer Flow permet à Azure Data Explorer d’utiliser les [fonctionnalités Flow de Microsoft Power Automate](https://flow.microsoft.com/) pour exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée. Ce document contient plusieurs exemples courants d’utilisation du connecteur Microsoft Flow.

Pour plus d’informations, consultez [Connecteur Microsoft Flow (préversion)](flow.md).

* [Connecteur Microsoft Flow et SQL](#microsoft-flow-connector-and-sql)
* [Transmettre des données à un jeu de données Power BI](#push-data-to-power-bi-dataset)
* [Requêtes conditionnelles](#conditional-queries)
* [Envoyer par e-mail plusieurs graphiques Azure Data Explorer Flow](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Connecteur Microsoft Flow et SQL

Utilisez le connecteur Microsoft Flow pour interroger vos données et les agréger dans une base de données SQL.

> [!Note]
> L’insertion SQL est effectuée séparément pour chaque ligne. Utilisez uniquement le connecteur Microsoft Flow pour de petites quantités de données de sortie. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> Dans le champ *Nom du cluster*, entrez l’URL du cluster.

## <a name="push-data-to-power-bi-dataset"></a>Transmettre des données à un jeu de données Power BI

Le connecteur Microsoft Flow peut être utilisé conjointement avec le connecteur Power BI pour transmettre des données à partir de requêtes Kusto vers des jeux de données de streaming Power BI.

1. Créez une action Exécuter la requête et affichez la liste des résultats.
1. Sélectionnez **Nouvelle étape**.
1. Sélectionnez **Ajouter une action** et recherchez Power BI.
1. Sélectionnez **Power BI**.
1. Sélectionnez **Ajouter des lignes à un jeu de données**. 

    ![Connecteur Flow et Power BI](./media/flow-usage/flow-powerbiconnector.png)
1. Indiquez **l’Espace de travail**, le **Jeu de données** et la **Table** dans lesquels les données seront envoyées.
1. À partir de la boîte de dialogue de contenu dynamique, ajoutez une charge utile contenant votre schéma de jeu de données et les résultats de requête Kusto pertinents.

    ![Champs Power BI de Flow](./media/flow-usage/flow-powerbifields.png)

Flow applique automatiquement l’action Power BI pour chaque ligne de la table de résultats de la requête Kusto. 

![Action Power BI de Flow pour chaque ligne](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Requêtes conditionnelles

Les résultats des requêtes Kusto peuvent être utilisés comme entrées ou conditions pour les actions de Flow suivantes.

Dans l’exemple suivant, nous interrogeons Kusto pour les incidents qui se sont produits au cours du dernier jour. Pour chaque incident résolu, un message Slack est publié et une notification Push est créée.
Pour chaque incident toujours actif, Kusto est interrogé pour obtenir plus d’informations sur les incidents similaires. Il envoie ces informations sous forme d’e-mail et ouvre une tâche TFS associée.

Pour créer un flux similaire, suivez ces instructions :

1. Créez une action Exécuter la requête et affichez la liste des résultats.
1. Sélectionnez **Nouvelle étape**.
1. Sélectionnez **Contrôle de condition**.
1. Dans la fenêtre de contenu dynamique, sélectionnez le paramètre que vous souhaitez utiliser comme condition pour les actions suivantes.
1. Sélectionnez le type de *Relation* et la *Valeur* pour définir une condition spécifique sur le paramètre donné.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow applique cette condition sur chaque ligne de la table de résultats de la requête.
1. Ajoutez des actions à effectuer lorsque la condition a la valeur true ou false.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Vous pouvez utiliser les valeurs de résultat de la requête Kusto comme entrée pour les actions suivantes. Sélectionnez les valeurs de résultat dans la fenêtre de contenu dynamique.
Dans l’exemple ci-dessous, une action Slack - Publier un message et une action Visual Studio - Créer un nouvel élément de travail contenant les données de la requête Kusto ont été ajoutées.

![Action Slack - Publier un message](./media/flow-usage/flow-slack.png)

![Action Visual Studio](./media/flow-usage/flow-visualstudio.png)

Dans cet exemple, si un incident est toujours actif, interrogez à nouveau Kusto pour obtenir des informations sur la façon dont les incidents de la même source ont été résolus dans le passé.

![Requête de condition Flow](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> Dans le champ *Nom du cluster*, entrez l’URL du cluster.

Visualisez ces informations sous forme de graphique à secteurs et envoyez-les par e-mail à l’équipe.

![E-mail de condition Flow](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Envoyer par e-mail plusieurs graphiques Azure Data Explorer Flow

1. Créez un nouveau Flow avec le déclencheur de récurrence, puis définissez l’intervalle de Flow et la fréquence. 
1. Ajoutez une nouvelle étape avec une ou plusieurs actions Kusto - Exécuter la requête et visualiser les résultats. 

    ![Exécuter plusieurs requêtes dans un Flow](./media/flow-usage/flow-severalqueries.png)
1. Pour chaque requête Kusto - Exécuter et visualiser les résultats, définissez les champs suivants :
    * URL du cluster
    * Nom de la base de données
    * Type de requête et de graphique (tableau HTML, graphique à secteurs, graphique temporel, graphique à barres ou entrer une valeur personnalisée).

    ![Visualiser les résultats avec plusieurs pièces jointes](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Ajoutez une action Envoyer un e-mail (v2) : 
    1. Dans le corps du texte, sélectionnez l’icône de visualisation du code.
    1. Dans le champ **Corps**, insérez le BodyHtml requis afin que le résultat visuel de la requête soit inclus dans le corps de l’e-mail.
    1. Pour ajouter une pièce jointe à l’e-mail, ajoutez le Nom de la pièce jointe et le Contenu de la pièce jointe.
    
    ![Envoyer par e-mail plusieurs pièces jointes](./media/flow-usage/flow-email-multiple-attachments.png)

    Pour obtenir des instructions complètes sur la création d’une action d’e-mail, consultez [Envoyer par e-mail les résultats de requête Kusto](flow.md#email-kusto-query-results). 

Résultats :

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le [connecteur d’application logique Microsoft Azure Explorer](kusto/tools/logicapps.md), qui est une autre façon d’exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée.

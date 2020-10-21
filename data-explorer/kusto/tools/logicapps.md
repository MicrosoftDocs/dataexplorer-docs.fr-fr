---
title: Utiliser Logic Apps pour exécuter automatiquement des requêtes Kusto
description: Découvrez comment utiliser Logic Apps pour exécuter automatiquement des requêtes et des commandes Kusto et les planifier
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 4c918c43f748f97b3bb3f6d0342c660775c1e5c8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342652"
---
# <a name="microsoft-logic-app-and-azure-data-explorer"></a>Application logique Microsoft et Explorateur de données Azure

Le connecteur Azure Kusto Logic App vous permet d’exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée, à l’aide du connecteur [Microsoft Logic App](/azure/logic-apps/logic-apps-what-are-logic-apps) .

L’application logique et l’automate sont basés sur le même connecteur. Par conséquent, les [limitations](../../flow.md#limitations), les [actions](../../flow.md#flow-actions), les exemples [d’authentification](../../flow.md#authentication) et d' [utilisation](../../flow-usage.md) qui s’appliquent à power automate s’appliquent également à Logic Apps, comme indiqué dans la [page de documentation Power automate](../../flow.md).

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Comment créer une application logique avec Azure Explorateur de données

1. Ouvrez le [portail Microsoft Azure](https://ms.portal.azure.com/). 
1. Recherchez et sélectionnez `logic app`.

    :::image type="content" source="./images/logicapps/logicapp-search.png" alt-text="Recherche d’une « application logique » dans le Portail Azure, Azure Explorateur de données" lightbox="./images/logicapps/logicapp-search.png#lightbox":::

1. Sélectionnez **+Ajouter**.

    ![Ajouter une application logique](./Images/logicapps/logicapp-add.png)

1. Entrez les informations requises pour le formulaire :
    * Abonnement
    * Resource group
    * Nom de l’application logique
    * Région ou environnement de service d’intégration
    * Location
    * Analyse des journaux activée ou désactivée
1. Sélectionnez **Revoir + créer**.

    ![Créer une application logique](./Images/logicapps/logicapp-create-new.png)

1. Lorsque l’application logique est créée, sélectionnez **modifier**.

    ![Modifier le concepteur d’applications logiques](./Images/logicapps/logicapp-editdesigner.png "logicapp-editdesigner")

1. Créez une application logique vide.

    ![Modèle vide d’application logique](./Images/logicapps/logicapp-blanktemplate.png "logicapp-BlankTemplate")

1. Ajoutez une action de périodicité et sélectionnez **Azure Kusto**.

    ![Connecteur de Flow Kusto Logic App](./Images/logicapps/logicapp-kustoconnector.png "logicapp-kustoconnector")

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la configuration d’une action de récurrence, consultez la [page de documentation Power automate](../../flow.md)
* Jetez un coup d’œil à certains [exemples d’utilisation](../../flow-usage.md) pour des idées sur la configuration de vos actions d’application logique

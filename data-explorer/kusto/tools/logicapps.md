---
title: Microsoft Logic App et Kusto - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Microsoft Logic App et Kusto dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f7d719ece5df6eb3f6d4060a2fb07e7092902601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523815"
---
# <a name="microsoft-logic-app-and-kusto"></a>Microsoft Logic App et Kusto

Le connecteur Azure Kusto Logic App permet aux utilisateurs d’exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée, à l’aide de [Microsoft Logic App](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).

Les connecteurs Logic App et Flow sont construits au-dessus du même connecteur donc les mêmes [limitations,](flow.md#limitations) [actions](flow.md#azure-kusto-flow-actions), [authentification](flow.md#authentication) et [exemples d’utilisation](flow.md#usage-examples) s’appliquent pour les deux comme mentionné dans la [page de documentation Flow](flow.md).


## <a name="how-to-create-a-logic-app-with-azure-kusto"></a>Comment créer une application Logique avec Azure Kusto

Ouvrez le portail Azure et cliquez sur créer une nouvelle ressource Logic App.
Ajoutez le nom, l’abonnement, le groupe de ressources et l’emplacement souhaités et cliquez sur créer.

![Créer une application logique](./Images/KustoTools-LogicApp/logicapp-createlogicapp.png "logicapp-createlogicapp")

Après la création de l’application Logic, cliquez sur le bouton d’édition

![Modifier le concepteur d’applications logiques](./Images/KustoTools-LogicApp/logicapp-editdesigner.png "logicapp-editdesigner")

Créer une application Logique vierge

![Modèle vide d’application logique](./Images/KustoTools-LogicApp/logicapp-blanktemplate.png "logicapp-blanktemplate")

Ajouter l’action de répétition et ensuite choisir 'Azure Kusto'

![Logic app Kusto Flow connecteur](./Images/KustoTools-LogicApp/logicapp-kustoconnector.png "logicapp-kustoconnector")
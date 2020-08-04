---
title: Ajouter des principaux de base de données pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager
description: Dans cet article, vous apprendrez à ajouter des principaux de base de données pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 5ed9900a33fe90dbb04be3725ddd8e19bfb63b07
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349990"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Ajouter des principaux de base de données pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Modèle Azure Resource Manager](database-principal-resource-manager.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Dans cet article, vous ajouterez des principaux de base de données pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster et une base de données](create-cluster-database-portal.md).

## <a name="azure-resource-manager-template-for-adding-a-database-principal"></a>Modèle Azure Resource Manager pour l’ajout d’un principal de base de données

L’exemple suivant montre un modèle Azure Resource Manager pour l’ajout d’un principal de base de données.  Vous pouvez [modifier et déployer le modèle dans le portail Azure](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) en utilisant le formulaire.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalAssignmentName": {
            "type": "string",
            "defaultValue": "principalAssignment1",
            "metadata": {
                "description": "Specifies the name of the principal assignment"
            }
        },
        "clusterName": {
            "type": "string",
            "defaultValue": "mykustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "mykustodatabase",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "principalIdForDatabase": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForDatabasePrincipal": {
            "type": "string",
            "defaultValue": "Admin",
            "metadata": {
                "description": "Specifies the database principal role. It can be 'Admin', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer'"
            }
        },
        "tenantIdForDatabasePrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the database principal"
            }
        },
        "principalTypeForDatabase": {
            "type": "string",
            "defaultValue": "App",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('databaseName'), '/', parameters('principalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForDatabase')]",
                "role": "[parameters('roleForDatabasePrincipal')]",
                "tenantId": "[parameters('tenantIdForDatabasePrincipal')]",
                "principalType": "[parameters('principalTypeForDatabase')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md)

---
title: Créer un cluster et une base de données Azure Data Explorer avec un modèle Azure Resource Manager
description: Découvrir comment créer un cluster et une base de données Azure Data Explorer avec un modèle Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 7a12f43c5c6685ea474f6f19f6e585b09b194fce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492689"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Créer un cluster et une base de données Azure Data Explorer avec un modèle Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portail](create-cluster-database-portal.md)
> * [INTERFACE DE LIGNE DE COMMANDE](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modèle Azure Resource Manager](create-cluster-database-resource-manager.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. 

Dans cet article, vous créez un cluster et une base de données Azure Data Explorer à l’aide d’un [modèle Azure Resource Manager](/azure/azure-resource-manager/management/overview). L’article montre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins. Pour plus informations sur la création de modèles, consultez [Création de modèles Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates). Pour connaître la syntaxe et les propriétés JSON à utiliser dans un modèle, consultez [Types de ressources Microsoft.Kusto](/azure/templates/microsoft.kusto/allversions).

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Modèle Azure Resource Manager pour la création de clusters et de bases de données

Dans cet article, vous utilisez un [modèle de démarrage rapide existant](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Pour obtenir d’autres exemples de modèles, voir [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Déployer le modèle et vérifier le déploiement du modèle

Vous pouvez déployer le modèle Azure Resource Manager [à l’aide du portail Azure](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) ou [à l’aide de PowerShell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Utiliser le portail Azure pour déployer le modèle et vérifier le déploiement du modèle

1. Pour créer un cluster et une base de données, utilisez le bouton suivant pour commencer le déploiement. Cliquez avec le bouton droit et sélectionnez **Ouvrir dans une nouvelle fenêtre** pour pouvoir suivre le reste des étapes de l’article.

    [![Déployer sur Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Le bouton **Déployer sur Azure** vous amène dans le portail Azure pour remplir un formulaire de déploiement.

    ![Déployer dans Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Vous pouvez [modifier et déployer le modèle dans le portail Azure](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) en utilisant le formulaire.

1. Complétez les sections **INFORMATIONS DE BASE** et **PARAMÈTRES**. Sélectionnez des noms de cluster et de base de données uniques.
La création d’un cluster et d’une base de données Azure Data Explorer prend quelques minutes.

1. Pour vérifier le déploiement, ouvrez le groupe de ressources dans le [portail Azure](https://portal.azure.com) pour rechercher votre nouveau cluster et votre nouvelle base de données. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Utiliser PowerShell pour déployer le modèle et vérifier le déploiement du modèle

#### <a name="deploy-the-template-using-powershell"></a>Déployer le modèle à l’aide de PowerShell

1. Sélectionnez **Essayer** à partir du bloc de code suivant, puis suivez les instructions permettant de vous connecter à Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Sélectionnez **Copier** pour copier le script PowerShell.
1. Cliquez avec le bouton droit sur la console d’interpréteur de commandes, puis sélectionnez **Coller**.
La création d’un cluster et d’une base de données Azure Data Explorer prend quelques minutes.

#### <a name="verify-the-deployment-using-powershell"></a>Vérifier le déploiement à l’aide de PowerShell

Pour vérifier le déploiement, utilisez le script Azure PowerShell suivant.  Si Cloud Shell est toujours ouvert, vous n’avez pas besoin de copier/exécuter la première ligne (Read-Host). Pour plus d’informations sur la gestion des ressources Azure Data Explorer dans PowerShell, consultez [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

[Ingérer des données dans un cluster et une base de données Azure Data Explorer](ingest-data-overview.md)

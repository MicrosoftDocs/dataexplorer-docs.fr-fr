---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: de8a37b1b27dc1bff377c6212c0bfea0a13c7de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492685"
---
## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Nettoyer des ressources à l’aide du portail Azure

Supprimez les ressources dans le portail Azure en suivant les étapes décrites dans [Nettoyer les ressources](../create-cluster-database-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Supprimer des ressources à l’aide de PowerShell

Si Cloud Shell est toujours ouvert, vous n’avez pas besoin de copier/exécuter la première ligne (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```
---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: orspodek
ms.openlocfilehash: d55077b5d1938caf6df49d34e68ece7e32c56f85
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350063"
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
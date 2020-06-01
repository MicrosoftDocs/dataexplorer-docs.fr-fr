---
title: Configurer des clés gérées par le client dans Azure Data Explorer à l’aide du modèle Azure Resource Manager
description: Cet article explique comment configurer le chiffrement de vos données avec des clés gérées par le client dans Azure Data Explorer à l’aide du modèle Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 1d7611537a96cf38ab33ddba30c83f721c90d11c
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257975"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portail](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [INTERFACE DE LIGNE DE COMMANDE](customer-managed-keys-cli.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurer le chiffrement avec les clés gérées par le client

Dans cette section, vous allez configurer des clés gérées par le client à l’aide de modèles Azure Resource Manager. Par défaut, le chiffrement Azure Data Explorer utilise des clés gérées par Microsoft. À cette étape, configurez votre cluster Azure Data Explorer pour utiliser des clés gérées par le client et spécifiez la clé à lui associer.

Vous pouvez déployer le modèle Azure Resource Manager à l’aide du Portail Azure ou à l’aide de PowerShell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
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
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Mettre à jour la version de la clé

Lors de la création de la nouvelle version d’une clé, vous devez mettre à jour le cluster afin qu’il utilise cette nouvelle version. Tout d’abord, appelez la commande `Get-AzKeyVaultKey` pour obtenir la dernière version de la clé. Ensuite, mettez à jour les propriétés du coffre de clés du cluster pour utiliser la nouvelle version de la clé, comme indiqué dans [Configurer le chiffrement avec des clés gérées par le client](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les clusters Azure Data Explorer dans Azure](security.md)
* [Configurer des identités managées pour votre cluster Azure Data Explorer](managed-identities.md)
* [Sécuriser votre cluster dans Azure Data Explorer – Portail Azure](manage-cluster-security.md) en activant le chiffrement au repos.
* [Configurer des clés gérées par le client à l’aide de C#](customer-managed-keys-csharp.md)


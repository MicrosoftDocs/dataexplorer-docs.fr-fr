---
title: Configurer des clés gérées par le client avec PowerShell
description: Cet article explique comment configurer le chiffrement de vos données avec des clés gérées par le client dans Azure Data Explorer avec PowerShell.
author: orspod
ms.author: orspodek
ms.reviewer: roshauli
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/04/2020
ms.openlocfilehash: 5c52253a6d8c4978931aeff3a5a5a73367f14ce5
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871993"
---
# <a name="configure-customer-managed-keys-using-powershell"></a>Configurer des clés gérées par le client avec PowerShell

> [!div class="op_single_selector"]
> * [Portail](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [INTERFACE DE LIGNE DE COMMANDE](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-powershell"></a>Activer le chiffrement avec des clés gérées par le client avec PowerShell

Cet article explique comment activer le chiffrement des clés gérées par le client avec PowerShell. Par défaut, le chiffrement Azure Data Explorer utilise des clés gérées par Microsoft. Configurez votre cluster Azure Data Explorer pour utiliser des clés gérées par le client et spécifiez la clé à lui associer.

1. Exécutez la commande ci-après pour vous connecter à Azure :

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Définissez l’abonnement auprès duquel lequel votre cluster est inscrit.

    ```azurepowershell-interactive
    Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```

1. Exécutez la commande suivante pour définir la nouvelle clé.

    ```azurepowershell-interactive
    Update-AzKustoCluster -ResourceGroupName "mytestrg" -Name "mytestcluster" -KeyVaultPropertyKeyName "<key-name>" -KeyVaultPropertyKeyVaultUri "<key-vault-uri>" -KeyVaultPropertyKeyVersion "<key-version>"
    ```

1. Exécutez la commande suivante et regardez les propriétés « KeyVaultProperties... » pour vérifier que le cluster a été mis à jour.

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name "mytestcluster" -ResourceGroupName "mytestrg" | Format-List
    ```

---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492841"
---
## <a name="create-a-new-key-vault"></a>Créer un coffre de clés

Pour créer un coffre de clés via PowerShell, appelez la commande [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Le coffre de clés que vous utilisez pour stocker des clés gérées par le client pour le chiffrement Azure Data Explorer doit disposer de deux paramètres de protection de clés, **Suppression réversible** et **Ne pas vider**. Remplacez les valeurs d’espace réservé entre crochets par vos propres valeurs dans l’exemple ci-dessous.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurer la stratégie d’accès au coffre de clés

Configurez ensuite la stratégie d’accès au coffre de clés de sorte que le cluster dispose des autorisations nécessaires pour y accéder. À cette étape, vous utiliserez l’identité managée affectée au système que vous avez précédemment attribuée au cluster. Pour définir la stratégie d’accès du coffre de clés, appelez la commande [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Remplacez les valeurs d’espace réservé entre crochets par vos propres valeurs et utilisez les variables définies dans les exemples précédents.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Créer une clé

Créez ensuite une clé dans le coffre de clés. Pour créer une clé, appelez la commande [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Remplacez les valeurs d’espace réservé entre crochets par vos propres valeurs et utilisez les variables définies dans les exemples précédents.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

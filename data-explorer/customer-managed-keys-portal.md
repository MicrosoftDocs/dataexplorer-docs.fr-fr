---
title: Configurer des clés gérées par le client à l’aide du Portail Azure
description: Découvrez comment configurer des clés gérées par le client pour chiffrer des données Azure Data Explorer en utilisant le portail Azure.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 0f77782b5174683d091685064afa7debff7ae777
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933907"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Configurer des clés gérées par le client à l’aide du Portail Azure

> [!div class="op_single_selector"]
> * [Portail](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [INTERFACE DE LIGNE DE COMMANDE](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Activer le chiffrement avec des clés gérées par le client dans le Portail Azure

Cet article explique comment activer le chiffrement des clés gérées par le client à l’aide du Portail Azure. Par défaut, le chiffrement Azure Data Explorer utilise des clés gérées par Microsoft. Configurez votre cluster Azure Data Explorer pour utiliser des clés gérées par le client et spécifiez la clé à lui associer.

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre ressource de [cluster Azure Data Explorer](create-cluster-database-portal.md#create-a-cluster).
1. Sélectionnez **Paramètres** > **Chiffrement** dans le volet gauche du portail.
1. Dans le volet **Chiffrement**, sélectionnez **Activé** pour le paramètre **Clé gérée par le client**.
1. Cliquez sur **Sélectionner une clé**.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-encryption-setting.png" alt-text="Configurer les clés gérées par le client":::

1. Dans la fenêtre **Sélectionner une clé à partir d’Azure Key Vault**, sélectionnez un **Coffre de clés** existant dans la liste déroulante. Si vous sélectionnez **Créer nouveau** pour créer un nouveau [Coffre de clés](/azure/key-vault/quick-create-portal#create-a-vault), vous êtes dirigé vers l’écran **Créer un coffre de clés**.

1. Sélectionner **Clé**.
1. Sélectionner **Version**.
1. Cliquez sur **Sélectionner**.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-key-vault.png" alt-text="Sélectionner une clé dans Azure Key Vault":::

1. Sous **Type d’identité**, sélectionnez **Affecté(e) par le système** ou **Affecté(e) par l’utilisateur**.
1. Si vous sélectionnez **Affecté(e) par l’utilisateur**, choisissez une identité affectée par l’utilisateur dans la liste déroulante.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-select-user-type.png" alt-text="Sélection d’une identité managée":::

1. Dans le volet **Chiffrement** qui contient maintenant votre clé, sélectionnez **Enregistrer**. Lorsque la création de CMK réussit, un message de réussite s’affiche dans **Notifications**.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-before-save.png" alt-text="Enregistrer une clé gérée par le client":::

Si vous sélectionnez une identité affectée par le système lorsque vous activez des clés gérées par le client pour votre cluster Azure Data Explorer, vous allez créer une identité affectée par le système pour le cluster, si celle-ci n’existe pas déjà. En outre, vous devez fournir les autorisations get, wrapKey et unwarpKey requises à votre cluster Azure Data Explorer sur le Key Vault sélectionné et obtenir les propriétés du Key Vault.

> [!NOTE]
> Sélectionnez **Désactivé** pour supprimer la clé gérée par le client après sa création.

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les clusters Azure Data Explorer dans Azure](security.md)
* [Sécuriser votre cluster en utilisant le chiffrement de disque dans Azure Data Explorer – Portail Azure](cluster-disk-encryption.md) en activant le chiffrement au repos.
* [Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Configurer des clés gérées par le client à l’aide de C#](customer-managed-keys-csharp.md)

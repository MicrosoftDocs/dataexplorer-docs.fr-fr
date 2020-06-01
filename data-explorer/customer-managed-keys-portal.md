---
title: Configurer des clés gérées par le client à l’aide du Portail Azure
description: Cet article explique comment configurer le chiffrement de vos données avec des clés gérées par le client dans Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: a75329c6aaad4fa31301104f9407ac36d25c3002
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257890"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Configurer des clés gérées par le client à l’aide du Portail Azure

> [!div class="op_single_selector"]
> * [Portail](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [INTERFACE DE LIGNE DE COMMANDE](customer-managed-keys-cli.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Activer le chiffrement avec des clés gérées par le client dans le Portail Azure

Cet article explique comment activer le chiffrement des clés gérées par le client à l’aide du Portail Azure. Par défaut, le chiffrement Azure Data Explorer utilise des clés gérées par Microsoft. Configurez votre cluster Azure Data Explorer pour utiliser des clés gérées par le client et spécifiez la clé à lui associer.

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à votre ressource de [cluster Azure Data Explorer](create-cluster-database-portal.md#create-a-cluster). 
1. Sélectionnez **Paramètres** > **Chiffrement** dans le volet gauche du portail.
1. Dans le volet **Chiffrement**, sélectionnez **Activé** pour le paramètre **Clé gérée par le client**.
1. Cliquez sur **Sélectionner une clé**.

    ![Configurer les clés gérées par le client](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. Dans la fenêtre **Sélectionner une clé à partir d’Azure Key Vault**, sélectionnez un **Coffre de clés** existant dans la liste déroulante. Si vous sélectionnez **Créer nouveau** pour créer un nouveau [Coffre de clés](/azure/key-vault/quick-create-portal#create-a-vault), vous êtes dirigé vers l’écran **Créer un coffre de clés**.

1. Sélectionner **Clé**.
1. Sélectionner **Version**.
1. Cliquez sur **Sélectionner**.

    ![Sélectionner une clé dans Azure Key Vault](media/customer-managed-keys-portal/cmk-key-vault.png)

1. Dans le volet **Chiffrement** qui contient maintenant votre clé, sélectionnez **Enregistrer**. Lorsque la création de CMK réussit, un message de réussite s’affiche dans **Notifications**.

    ![Enregistrer une clé gérée par le client](media/customer-managed-keys-portal/cmk-encryption-setting.png)

En activant des clés gérées par le client pour votre cluster Azure Data Explorer, vous allez créer une identité affectée par le système pour le cluster, si celle-ci n’existe pas. En outre, vous devez fournir les autorisations get, wrapKey et unwarpKey requises à votre cluster Azure Data Explorer sur le Key Vault sélectionné et obtenir les propriétés du Key Vault. 

> [!NOTE]
> Sélectionnez **Désactivé** pour supprimer la clé gérée par le client après sa création.

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les clusters Azure Data Explorer dans Azure](security.md)
* [Sécuriser votre cluster dans Azure Data Explorer – Portail Azure](manage-cluster-security.md) en activant le chiffrement au repos.
* [Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Configurer des clés gérées par le client à l’aide de C#](customer-managed-keys-csharp.md)




---
title: Configurer des clés gérées par le client avec Azure CLI
description: Cet article explique comment configurer le chiffrement de vos données avec des clés gérées par le client dans Azure Data Explorer à l’aide d’Azure CLI.
author: orspod
ms.author: orspodek
ms.reviewer: astauben
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 570ec818a330074cdf46075571d831c718273e64
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262114"
---
# <a name="configure-customer-managed-keys-using-azure-cli"></a>Configurer des clés gérées par le client avec Azure CLI

> [!div class="op_single_selector"]
> * [Portail](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [INTERFACE DE LIGNE DE COMMANDE](customer-managed-keys-cli.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Activer le chiffrement avec des clés gérées par le client dans le Portail Azure

Cet article explique comment activer le chiffrement des clés gérées par le client à l’aide du client Azure CLI. Par défaut, le chiffrement Azure Data Explorer utilise des clés gérées par Microsoft. Configurez votre cluster Azure Data Explorer pour utiliser des clés gérées par le client et spécifiez la clé à lui associer.

1. Exécutez la commande ci-après pour vous connecter à Azure :

    ```azurecli-interactive
    az login
    ```

1. Définissez l’abonnement auprès duquel lequel votre cluster est inscrit. Remplacez *MyAzureSub* par le nom de l’abonnement Azure que vous voulez utiliser.

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

1. Exécutez la commande suivante pour définir la nouvelle clé.
    ```azurecli-interactive
    az kusto cluster update --cluster-name "mytestcluster" --resource-group "mytestrg" --key-vault-properties key-name="<key-name>" key-version="<key-version>" key-vault-uri="<key-vault-uri>"
    ```
1. Exécutez la commande suivante et examinez la propriété « keyVaultProperties » pour vérifier que le cluster a été mis à jour.

    ```azurecli-interactive
    az kusto cluster show --cluster-name "mytestcluster" --resource-group "mytestrg"
    ```


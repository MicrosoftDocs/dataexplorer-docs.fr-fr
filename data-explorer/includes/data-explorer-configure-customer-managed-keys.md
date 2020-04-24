---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492837"
---
Azure Data Explorer chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour plus de contrôle sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client à utiliser pour le chiffrement des données. 

Les clés gérées par le client doivent être stockées dans [Azure Key Vault](/azure/key-vault/key-vault-overview). Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser une API d’Azure Key Vault pour générer des clés. Le cluster Azure Data Explorer et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour obtenir une explication détaillée sur les clés gérées par le client, consultez [Clés gérées par le client avec Azure Key Vault](/azure/storage/common/storage-service-encryption). 

Cet article vous montre comment configurer des clés gérées par le client.

## <a name="configure-azure-key-vault"></a>Configurer Azure Key Vault

Pour configurer des clés gérées par le client avec Azure Data Explorer, vous devez [définir deux propriétés sur le coffre de clés](/azure/key-vault/key-vault-ovw-soft-delete) : **Suppression réversible** et **Ne pas vider**. Ces propriétés ne sont pas activées par défaut. Pour activer ces propriétés, vous pouvez procéder par **Activation de la suppression réversible** et **Activation de la protection contre le vidage** dans [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) ou [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) sur un coffre de clés nouveau ou existant. Seules les clés RSA de taille de 2048 sont prises en charge. Pour plus d'informations sur les clés, consultez [Clés Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Le chiffrement des données à l’aide de clés gérées par le client n’est pas pris en charge sur les clusters de [responsable de d’abonné](/azure/data-explorer/follower). 

## <a name="assign-an-identity-to-the-cluster"></a>Attribuer une identité au cluster

Pour activer des clés gérées par le client pour votre cluster, attribuez tout d’abord au cluster une identité managée affectée au système. Cette identité managée vous sera utile pour autoriser le cluster à accéder au coffre de clés. Pour configurer des identités managées affectées au système, consultez [Identités managées](/azure/data-explorer/managed-identities).
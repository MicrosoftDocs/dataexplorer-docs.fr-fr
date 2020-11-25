---
title: Définitions de stratégie intégrées pour Azure Data Explorer
description: Liste les définitions de stratégie intégrées d’Azure Policy pour Azure Data Explorer. Ces définitions de stratégie intégrées fournissent des approches courantes pour la gestion de vos ressources Azure.
ms.date: 11/17/2020
ms.topic: reference
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-reference
ms.openlocfilehash: 9f2e98a1be83be659c8f7b85283c868d96f999eb
ms.sourcegitcommit: 0820454feb02ae489f3a86b688690422ae29d788
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94937905"
---
# <a name="azure-policy-built-in-definitions-for-azure-data-explorer"></a>Définitions intégrées d’Azure Policy pour Azure Data Explorer

Cette page constitue un index des définitions de stratégie intégrées [Azure Policy](/azure/governance/policy/overview) pour Azure Data Explorer. Pour obtenir des éléments intégrés supplémentaires d’Azure Policy pour d’autres services, consultez [Définitions intégrées d’Azure Policy](/azure/governance/policy/samples/built-in-policies).

Le nom de chaque définition de stratégie intégrée est un lien vers la définition de la stratégie dans le portail Azure. Utilisez le lien de la colonne **Version** pour voir la source dans le [dépôt GitHub Azure Policy](https://github.com/Azure/azure-policy).

## <a name="azure-data-explorer"></a>Explorateur de données Azure

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le chiffrement au repos Azure Data Explorer doit utiliser une clé gérée par le client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Le fait d’activer le chiffrement au repos à l’aide d’une clé gérée par le client dans votre cluster Azure Data Explorer vous offre un contrôle supplémentaire sur cette clé. Cette fonctionnalité est souvent utilisée par les clients qui ont des exigences particulières au niveau de la conformité. En outre, elle nécessite un coffre de clés pour la gestion des clés. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Le chiffrement de disque doit être activé dans Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Le fait d’activer le chiffrement de disque vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise concernant la sécurité et la conformité. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Le chiffrement double doit être activé dans Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Le fait d’activer le chiffrement double vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise concernant la sécurité et la conformité. Lorsque le chiffrement double est activé, les données d’un compte de stockage sont chiffrées deux fois : une fois au niveau du service et une fois au niveau de l’infrastructure, avec deux algorithmes de chiffrement différents et deux clés différentes. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[L’injection de réseau virtuel doit être activée dans Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Sécurisez votre périmètre réseau à l’aide de l’injection de réseau virtuel qui vous permet d’appliquer des règles de groupe de sécurité réseau, de vous connecter localement et de sécuriser vos sources de connexion de données avec des points de terminaison de service. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |

## <a name="next-steps"></a>Étapes suivantes

- Consultez les définitions intégrées dans le [dépôt Azure Policy de GitHub](https://github.com/Azure/azure-policy).
- Consultez la [Structure de définition Azure Policy](/azure/governance/policy/concepts/definition-structure).
- Consultez la page [Compréhension des effets de Policy](/azure/governance/policy/concepts/effects).

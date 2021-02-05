---
title: Contrôles de conformité réglementaire Azure Policy pour Azure Data Explorer
description: Liste les contrôles de conformité réglementaire Azure Policy disponibles pour Azure Data Explorer. Ces définitions de stratégie intégrées fournissent des approches courantes pour la gestion de la conformité de vos ressources Azure.
ms.date: 02/05/2021
ms.topic: sample
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 9f52935e235dcf64c5236d3fbdd58b95441aa5c9
ms.sourcegitcommit: d1c2433df183d0cfbfae4d3b869ee7f9cbf00fe4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99586386"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-data-explorer"></a>Contrôles de conformité réglementaire Azure Policy pour Azure Data Explorer

La [Conformité réglementaire d’Azure Policy](/azure/governance/policy/concepts/regulatory-compliance) fournit des définitions d’initiatives créées et gérées par Microsoft, qui sont dites _intégrées_, pour les **domaines de conformité** et les **contrôles de sécurité** associés à différents standards de conformité. Cette page liste les **domaines de conformité** et les **contrôles de sécurité** pour Azure Data Explorer. Vous pouvez affecter les composants intégrés pour un **contrôle de sécurité** individuellement, afin de rendre vos ressources Azure conformes au standard spécifique.

Le titre de chaque définition de stratégie intégrée est un lien vers la définition de la stratégie dans le portail Azure. Utilisez le lien de la colonne **Version de la stratégie** pour voir la source dans le [dépôt GitHub Azure Policy](https://github.com/Azure/azure-policy).

> [!IMPORTANT]
> Chaque contrôle ci-dessous est associé à une ou plusieurs définitions [Azure Policy](/azure/governance/policy/overview). Ces stratégies peuvent vous aider à [évaluer la conformité](/azure/governance/policy/how-to/get-compliance-data) avec le contrôle ; toutefois, il n’existe pas souvent de correspondance un-à-un ou parfaite entre un contrôle et une ou plusieurs stratégies. Ainsi, la **conformité** dans Azure Policy fait uniquement référence aux stratégies elles-mêmes ; cela ne garantit pas que vous êtes entièrement conforme à toutes les exigences d’un contrôle. En outre, la norme de conformité comprend des contrôles qui ne sont traités par aucune définition Azure Policy pour l’instant. Par conséquent, la conformité dans Azure Policy n’est qu’une vue partielle de l’état de conformité global. Les associations entre les contrôles et les définitions de conformité réglementaire Azure Policy pour ces normes de conformité peuvent changer au fil du temps.

## <a name="cmmc-level-3"></a>CMMC niveau 3

Pour voir comment les composants intégrés Azure Policy de tous les services Azure répondent à ce standard de conformité, consultez [Conformité réglementaire Azure Policy – CMMC niveau 3](/azure/governance/policy/samples/cmmc-l3). Pour plus d’informations sur ce standard de conformité, consultez [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domain |ID du contrôle |Titre du contrôle |Policy<br /><sub>(Portail Azure)</sub> |Version de la stratégie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Protection du système et des communications |SC.3.177 |Utiliser le chiffrement validé FIPS quand il est utilisé pour protéger la confidentialité d’informations CUI. |[Le chiffrement au repos Azure Data Explorer doit utiliser une clé gérée par le client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|Protection du système et des communications |SC.3.177 |Utiliser le chiffrement validé FIPS quand il est utilisé pour protéger la confidentialité d’informations CUI. |[Le chiffrement de disque doit être activé dans Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|Protection du système et des communications |SC.3.177 |Utiliser le chiffrement validé FIPS quand il est utilisé pour protéger la confidentialité d’informations CUI. |[Le chiffrement double doit être activé dans Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|Protection du système et des communications |SC.3.191 |Protéger la confidentialité de CUI au repos. |[Le chiffrement de disque doit être activé dans Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|Protection du système et des communications |SC.3.191 |Protéger la confidentialité de CUI au repos. |[Le chiffrement double doit être activé dans Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [Conformité réglementaire d’Azure Policy](/azure/governance/policy/concepts/regulatory-compliance).
- Consultez les définitions intégrées dans le [dépôt Azure Policy de GitHub](https://github.com/Azure/azure-policy).

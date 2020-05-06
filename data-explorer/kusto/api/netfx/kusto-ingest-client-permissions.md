---
title: Informations de référence sur Kusto. ingestion-autorisations d’ingestion-Azure Explorateur de données
description: Cet article décrit les autorisations d’ingestion de référence Kusto. ad dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e60eb6642a66fac81ce373f8f4d62de4f7217a91
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799694"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Informations de référence sur Kusto. ingestion

Cet article explique les autorisations à configurer sur votre service, pour `Native` que l’ingestion fonctionne.

## <a name="prerequisites"></a>Prérequis

* Pour afficher et modifier les paramètres d’autorisation sur les services et bases de données Kusto, consultez [commandes de contrôle Kusto](../../management/security-roles.md).

## <a name="references"></a>References

* Azure AD les applications utilisées comme exemples de principaux dans les exemples ci-dessous.
    * Tester l’application AAD (2a904276-1234-5678-9012-66fc53add60b ; microsoft.com)
    * Application AAD d’ingestion interne Kusto (76263cdb-1234-5678-9012-545644e9c404 ; microsoft.com)

## <a name="ingestion-permission-mode-for-queued-ingestion"></a>Mode d’autorisation ingestion pour l’ingestion en attente

Le mode d’autorisation ingestion est défini dans [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient). Ce mode limite la dépendance du code client sur le service Azure Explorateur de données. L’ingestion est effectuée en publiant un message d’ingestion Kusto dans une file d’attente Azure. La file d’attente, également appelée service d’ingestion, provient du service Azure Explorateur de données. Les artefacts de stockage intermédiaires seront créés par le client de réception à l’aide des ressources allouées par le service Azure Explorateur de données.

Le diagramme présente l’interaction du client d’ingestion en attente avec Kusto.

:::image type="content" source="../images/queued-ingest.jpg" alt-text="en attente-réception":::

### <a name="permissions-on-the-engine-service"></a>Autorisations sur le service de moteur

Pour pouvoir bénéficier de l’ingestion de `T1` données dans `DB1`une table de base de données, le principal qui exécute l’opération de réception doit avoir l’autorisation.
Les niveaux d’autorisation minimum `Database Ingestor` requis `Table Ingestor` sont et qui peuvent ingérer des données dans toutes les tables existantes d’une base de données ou dans une table existante spécifique.
Si la création de la table `Database User` est requise, ou si un rôle d’accès supérieur doit également être affecté.


|Rôle                 |PrincipalType        |PrincipalDisplayName
|---------------------|---------------------|------------
|`Database Ingestor`  |Application Azure AD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor`     |Application Azure AD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`le principal, l’application d’ingestion interne Kusto, est immutably mappé au `Cluster Admin` rôle. Il est donc autorisé à recevoir des données dans n’importe quelle table. C’est ce qui se passe sur les pipelines d’ingestion gérés par Kusto.

L’octroi des autorisations requises `DB1` sur la `T1` base de `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` données ou la table à Azure ad app se présente comme suit :

```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```

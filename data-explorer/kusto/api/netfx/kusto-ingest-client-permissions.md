---
title: Kusto. réception des autorisations d’ingestion-Azure Explorateur de données
description: Cet article décrit les autorisations Kusto. ingestion-ingestion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3fd516b7201c5e857417ca13bade668f32f25161
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226157"
---
# <a name="kustoingest---ingestion-permissions"></a>Kusto. deréception-autorisations d’ingestion

Cet article explique les autorisations à configurer sur votre service, pour que l’ingestion `Native` fonctionne.

## <a name="prerequisites"></a>Prérequis

* Pour afficher et modifier les paramètres d’autorisation sur les services et bases de données Kusto, consultez [commandes de contrôle Kusto](../../management/security-roles.md).

* Les applications Azure Active Directory (Azure AD) utilisées comme exemples de principaux dans les exemples suivants :
    * Azure AD App de test (2a904276-1234-5678-9012-66fc53add60b ; microsoft.com)
    * Azure AD App d’ingestion interne Kusto (76263cdb-1234-5678-9012-545644e9c404 ; microsoft.com)

## <a name="ingestion-permission-mode-for-queued-ingestion"></a>Mode d’autorisation ingestion pour l’ingestion en attente

Le mode d’autorisation ingestion est défini dans [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient). Ce mode limite la dépendance du code client sur le service Azure Explorateur de données. L’ingestion est effectuée en publiant un message d’ingestion Kusto dans une file d’attente Azure. La file d’attente, également appelée service d’ingestion, provient du service Azure Explorateur de données. Les artefacts de stockage intermédiaires seront créés par le client de réception à l’aide des ressources allouées par le service Azure Explorateur de données.

Le diagramme présente l’interaction du client d’ingestion en attente avec Kusto.

:::image type="content" source="../images/queued-ingest.jpg" alt-text="en attente-réception":::

### <a name="permissions-on-the-engine-service"></a>Autorisations sur le service de moteur

Pour pouvoir bénéficier de l’ingestion de données dans une table de `T1` base de données `DB1` , le principal qui exécute l’opération de réception doit avoir l’autorisation.
Les niveaux d’autorisation minimum requis sont `Database Ingestor` et qui peuvent ingérer des `Table Ingestor` données dans toutes les tables existantes d’une base de données ou dans une table existante spécifique.
Si la création de la table est requise, `Database User` ou si un rôle d’accès supérieur doit également être affecté.


|Role                 |PrincipalType        |PrincipalDisplayName
|---------------------|---------------------|------------
|`Database Ingestor`  |Application Azure AD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor`     |Application Azure AD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion Azure AD App (76263cdb-1234-5678-9012-545644e9c404)`le principal, l’application d’ingestion interne Kusto, est immutably mappé au `Cluster Admin` rôle. Il est donc autorisé à recevoir des données dans n’importe quelle table. C’est ce qui se passe sur les pipelines d’ingestion gérés par Kusto.

L’octroi des autorisations requises sur la base de données `DB1` ou `T1` la table à Azure ad app se `Test App (2a904276-1234-5678-9012-66fc53add60b in Azure AD tenant microsoft.com)` présente comme suit :

```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
```

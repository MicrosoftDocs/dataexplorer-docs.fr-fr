---
title: Informations de référence sur Kusto. ingestion-autorisations d’ingestion-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les autorisations d’ingestion de référence Kusto. ad dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ec3b91056a4cfc584c0d7168549864b2b50ef5ae
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617916"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Informations de référence sur Kusto. ingestion
Cet article explique les autorisations à configurer sur votre service, pour `Native` que l’ingestion fonctionne.


## <a name="prerequisites"></a>Prérequis
* Pour afficher et modifier les paramètres d’autorisation sur les services et bases de données Kusto, consultez [commandes de contrôle Kusto](../../management/security-roles.md) 

## <a name="references"></a>References
* Les applications AAD suivantes sont utilisées comme exemples de principaux dans les exemples ci-dessous :
    * Tester l’application AAD (2a904276-1234-5678-9012-66fc53add60b ; microsoft.com)
    * Application AAD d’ingestion interne Kusto (76263cdb-1234-5678-9012-545644e9c404 ; microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>Modèle d’autorisation d’ingestion pour la réception en file d’attente
Ce mode, défini dans [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), limite la dépendance du code client sur le service Azure Explorateur de données. L’ingestion est effectuée en publiant un message d’ingestion Kusto dans une file d’attente Azure. La file d’attente est acquise auprès du service Azure Explorateur de données. It’ss également connu sous le nom de service d’ingestion.  Tous les artefacts de stockage intermédiaires seront créés par le client de réception à l’aide des ressources allouées par le service Azure Explorateur de données.

Le diagramme suivant présente l’interaction du client d’ingestion en attente avec Kusto :<BR>

:::image type="content" source="../images/queued-ingest.jpg" alt-text="en attente-réception":::

### <a name="permissions-on-the-engine-service"></a>Autorisations sur le service de moteur
Pour pouvoir bénéficier de l’ingestion de `T1` données dans `DB1`une table de base de données, le principal qui exécute l’opération de réception doit avoir l’autorisation.
Les niveaux d’autorisation minimum `Database Ingestor` requis `Table Ingestor` sont et qui peuvent ingérer des données dans toutes les tables existantes d’une base de données ou dans une table existante spécifique.
Si la création de la table `Database User` est requise, ou si un rôle d’accès supérieur doit également être affecté.


|Role |PrincipalType    |PrincipalDisplayName
|--------|------------|------------
|`Database Ingestor` |Application AAD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor` |Application AAD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`le principal, l’application d’ingestion interne Kusto, est immutably mappé au `Cluster Admin` rôle et donc autorisé à recevoir des données dans n’importe quelle table. C’est ce qui se passe sur les pipelines d’ingestion gérés par Kusto.

L’octroi des autorisations requises `DB1` sur la `T1` base de données `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` ou la table à l’application AAD ressemble à ceci :
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```

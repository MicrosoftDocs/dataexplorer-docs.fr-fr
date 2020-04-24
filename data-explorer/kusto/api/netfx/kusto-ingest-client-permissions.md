---
title: Kusto.Ingest Reference - Autorisations d’ingestion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto.Ingest Reference - Ingestion Permissions in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 198d562f880b74f6df4c6318f72213ee865f8f28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502888"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Référence Kusto.Ingest - Autorisations d’ingestion
Cet article explique quelles autorisations doivent être mises `Native` en place sur votre service afin que l’ingestion fonctionne.



## <a name="prerequisites"></a>Prérequis
* Cet article indique comment utiliser les [commandes de contrôle Kusto](../../management/security-roles.md) pour afficher et modifier les paramètres d’autorisation sur les services et bases de données Kusto
* Les applications AAD suivantes sont utilisées comme directeurs d’échantillons dans des exemples ci-dessous :
    * Test AAD App (2a904276-1234-5678-9012-66fc53add60b;microsoft.com)
    * Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404;microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>Modèle d’autorisation d’ingestion pour l’ingestion en file d’attente
Défini dans [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), ce mode limite la dépendance au code client vis-à-vis du service Kusto. L’ingestion est effectuée en affichant un message d’ingestion Kusto à une file d’attente Azure, qui, à son tour, est acquise de Kusto Data Management (alias. Service d’ingestion). Tous les artefacts de stockage intermédiaires seront créés par le client ingérer à l’aide des ressources allouées par le service de gestion des données Kusto.<BR>

Le diagramme suivant décrit l’interaction avec kusto :<BR>

![texte de remplacement](../images/queued-ingest.jpg "file d’attente-ingest")

### <a name="permissions-on-the-engine-service"></a>Autorisations sur le service moteur
Pour être admissible à l’ingestion de données dans la base `T1` de données sur la base de données, `DB1` le principal effectuant l’opération ingérer doit être autorisé pour cela.
Les niveaux d’autorisation minimaux requis sont `Database Ingestor` et `Table Ingestor` peuvent ingérer des données dans toutes les tables existantes dans une base de données ou dans un tableau existant spécifique, en conséquence.
Si la création `Database User` de table est nécessaire, ou un rôle d’accès plus élevé doit également être attribué.


|Role |PrincipalType (principalType)    |PrincipalDisplayName (en)
|--------|------------|------------
|Base de données - Ingesteur |Demande AAD |App de test (id app: 2a904276-1234-5678-9012-66fc53add60b)
|Tableau - Ingesteur |Demande AAD |App de test (id app: 2a904276-1234-5678-9012-66fc53add60b)

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`principal (Kusto interne Ingestion App) est immuablement cartographié pour le `Cluster Admin` rôle et donc autorisé à ingérer des données à n’importe quel tableau (c’est ce qui se passe sur les pipelines d’ingestion gérés par Kusto).

L’octroi des `DB1` autorisations `T1` requises `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` sur la base de données ou la table à aAD App serait le suivant :
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```


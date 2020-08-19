---
title: Échecs d’ingestion-Azure Explorateur de données
description: Cet article décrit les échecs d’ingestion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: a7a2dcaea2ef982edc8286b83a042d2f21460986
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610438"
---
# <a name="ingestion-failures"></a>Échecs d’ingestion

## <a name="show-ingestion-failures"></a>. afficher les échecs d’ingestion


Cette commande retourne un jeu de résultats qui comprend les échecs d’ingestion qui se produisent lors de l’exécution des [commandes de contrôle d’ingestion de données](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) .


> [!NOTE]
> Les échecs d’ingestion qui se produisent pendant les autres parties du processus d’ingestion n’apparaissent pas dans le jeu de résultats de cette commande. Un tel échec peut se produire, par exemple, avant que les commandes de contrôle d’ingestion de données soient envoyées au service de moteur de données Kusto.

> Pour plus d’informations sur la surveillance des échecs qui se produisent dans les flux qui impliquent la [réception en file d’attente](../api/netfx/about-kusto-ingest.md#queued-ingestion), consultez [ce guide](../api/netfx/kusto-ingest-client-status.md).

**Syntaxe**

|Option de syntaxe|Description|
|---|---| 
|`.show` `ingestion` `failures`                                       |Retourne tous les échecs d’ingestion enregistrés  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |Retourne un ensemble filtré d’échecs d’ingestion
|`.show``ingestion` `failures` `with(OperationId="` *OperationId*`")` |Retourne les échecs d’ingestion pour un ID d’opération spécifique

**Résultats**
 
|Paramètre de sortie           |Type     |Description                                                                              |
|---------------------------|---------|-----------------------------------------------------------------------------------------|
|OperationId                |String   |Identificateur de l’opération qui peut être utilisé pour afficher des détails supplémentaires sur l’opération via le <br> [. Show Operations](operations.md) (commande) </br> 
|Base de données                   |String   |Base de données sur laquelle la défaillance s’est produite
|Table de charge de travail                      |String   |Table sur laquelle la défaillance s’est produite
|FailedOn                   |DateTime |Date/heure (au format UTC) de l’enregistrement de l’échec 
|IngestionSourcePath        |String   |Identifie la source d’ingestion (généralement, un URI d’objet BLOB Azure) 
|Détails                    |String   |Détails de l’échec. Fournit des informations sur la cause initiale de l’échec de l’ingestion réelle
|FailureKind                |String   |Type de l’échec (permanent/temporaire)
|RootActivityId             |String   |ID de l’activité racine.
|OperationKind              |String   |Type d’opération d’ingestion (phase) au cours de laquelle la défaillance a été inscrite
|OriginatesFromUpdatePolicy |Boolean | Indique si l’échec a été enregistré lors de l’exécution d’une [stratégie de mise à jour](update-policy.md)
 
**Exemple**
 
|OperationId |Base de données |Table de charge de travail |FailedOn |IngestionSourcePath |Détails |FailureKind |RootActivityId |OperationKind |OriginatesFromUpdatePolicy
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22:25:03.1147331 |... URL... |Le flux avec l’ID' * * * * *. csv’a un format CSV incorrect * |Permanent |3c883942-e446-4999-9b00-d4c664f06ef6 |DataIngestPull | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |... URL... |Le flux avec l’ID' * * * * *. csv’a un format CSV incorrect * |Permanent |48571bdb-b714-4f32-8ddc-4001838a956c |DataIngestPull | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |... URL... |Le flux avec l’ID' * * * * *. csv’a un format CSV incorrect * |Permanent |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |DataIngestPull | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |... URL... |Une erreur inconnue s’est produite : une exception de type’System. Exception’a été levée |Temporaire |9b7bb017-471e-48f6-9c96-d16fcf938d2a |DataIngestPull | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |... URL... |Échec du téléchargement de l’objet BLOB : le client n’a pas pu terminer l’opération dans le délai spécifié |Permanent |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |DataIngestPull | 0

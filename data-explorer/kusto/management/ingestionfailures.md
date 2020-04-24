---
title: Échecs d’ingestion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les échecs d’ingestion dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 699fd01e9a284179bf2749b58581392d2170f0ca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520942"
---
# <a name="ingestion-failures"></a>Échecs d’ingestion

## <a name="show-ingestion-failures"></a>.montrer les échecs d’ingestion

Cette commande renvoie un ensemble de résultats qui inclut toutes les défaillances d’ingestion qui ont été rencontrées lors de l’exécution des commandes de [contrôle d’ingestion](data-ingestion/index.md)de données .

*Remarques*: 
- Les défaillances d’ingestion qui sont rencontrées pendant d’autres parties du flux d’ingestion (p. ex., avant que les commandes de contrôle d’ingestion de données ne soient envoyées au service de moteur de données de Kusto) n’apparaîtront pas dans l’ensemble de résultat de cette commande.
- Pour les défaillances de surveillance qui se produisent dans les flux qui impliquent [l’ingestion en file d’attente](../api/netfx/about-kusto-ingest.md#queued-ingestion), il est recommandé de suivre [ce guide](../api/netfx/kusto-ingest-client-status.md).

**Syntaxe**

|||
|---|---| 
|`.show` `ingestion` `failures`                                       |Retourne toutes les défaillances d’ingestion enregistrées  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |Retourne un ensemble filtré d’échecs d’ingestion
|`.show``ingestion` `failures` *OperationId* OpérationId `with(OperationId="``")` |Retours d’ingestion pour une opération ID spécifique

**Résultats**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|OpérationId |String |Opération Identifier. Ce paramètre peut être utilisé pour afficher les détails de fonctionnement supplémentaires en exécutant la commande [des opérations .show](operations.md) 
|Base de données |String |Base de données sur laquelle l’échec a été rencontré
|Table de charge de travail |String |Tableau sur lequel l’échec a été rencontré
|ÉchecOn |DateTime |Date/heure (dans UTC) lorsque la défaillance a été enregistrée 
|IngestionSourcePath |String |Identifie la source d’ingestion (généralement, un Azure Blob URI) 
|Détails |String |Détails de l’échec. Fournit un aperçu de la cause profonde réelle de l’échec d’ingestion
|ÉchecKind |String |Type de l’échec (Permanent/Transitoire)
|RootActivityId |String |ID d’activité racine.
|OpérationKind |String |Le type d’opération d’ingestion (phase) au cours duquel la défaillance a été enregistrée
|OriginesDeUpdatePolicy |Boolean | Indique si l’échec a été enregistré lors de l’exécution d’une [politique de mise à jour](update-policy.md)
 
**Exemple**
 
|OpérationId |Base de données |Table de charge de travail |ÉchecOn |IngestionSourcePath |Détails |ÉchecKind |RootActivityId |OpérationKind |OriginesDeUpdatePolicy
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22:25:03.1147331 |... Url... |Stream avec id 'csv' a un format Csv mal formé |Permanent |3c883942-e446-4999-9b00-d4c664f06ef6 |DataIngestPull (en anglais) | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |... Url... |Stream avec id 'csv' a un format Csv mal formé |Permanent |48571bdb-b714-4f32-8ddc-4001838a956c |DataIngestPull (en anglais) | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |... Url... |Stream avec id 'csv' a un format Csv mal formé |Permanent |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |DataIngestPull (en anglais) | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |... Url... |Erreur inconnue s’est produite : l’exception du type 'System.Exception' a été lancée |Temporaire |9b7bb017-471e-48f6-9c96-d16fcf938d2a |DataIngestPull (en anglais) | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |... Url... |Échec du téléchargement blob: Le client ne pouvait pas terminer l’opération dans un délai d’attente spécifié |Permanent |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |DataIngestPull (en anglais) | 0
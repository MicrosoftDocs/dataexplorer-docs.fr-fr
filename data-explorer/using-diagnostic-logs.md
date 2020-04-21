---
title: Superviser les opérations d’ingestion d’Azure Data Explorer à l’aide des journaux de diagnostic
description: Découvrez comment configurer les journaux de diagnostic d’Azure Data Explorer pour superviser les opérations d’ingestion.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: c4c7114dd53b8f77150f88cf3da5d26ca73bb93b
ms.sourcegitcommit: 2baa6a1c25ba25ef02a53b4f8d023ea821bd04ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81527586"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs"></a>Superviser les opérations d’ingestion d’Azure Data Explorer à l’aide des journaux de diagnostic

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une table d’une base de données pour pouvoir exécuter des requêtes dessus. Les [journaux de diagnostic Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) fournissent des données sur les opérations effectuées sur des ressources Azure. Azure Data Explorer utilise les journaux de diagnostic pour obtenir des insights sur les réussites et les échecs d’ingestion. Vous pouvez exporter les journaux des opérations vers Stockage Azure, Event Hub ou Log Analytics afin de superviser l’état de l’ingestion. Les journaux de Stockage Azure et d’Azure Event Hub peuvent être routés vers une table de votre cluster Azure Data Explorer pour une analyse plus poussée.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas encore d’abonnement Azure, [créez un compte Azure gratuit](https://azure.microsoft.com/free/)
* Créez [un cluster et une base de données](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Configurer les journaux de diagnostic pour un cluster Azure Data Explorer

Vous pouvez utiliser les journaux de diagnostic pour configurer la collecte des données de journal suivantes :
* Opérations d’ingestion réussies : Ces journaux contiennent des informations sur les opérations d’ingestion terminées avec succès.
* Opérations d’ingestion ayant échoué : Ces journaux contiennent des informations détaillées sur les échecs d’opérations d’ingestion, notamment les détails des erreurs. 

Les données sont ensuite archivées dans un compte de stockage, envoyées en streaming à un hub d’événements ou envoyées à Log Analytics, conformément à vos spécifications.

### <a name="enable-diagnostic-logs"></a>Activer les journaux de diagnostic

Les journaux de diagnostic sont désactivés par défaut. Pour activer les journaux de diagnostic, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez la ressource de cluster Azure Data Explorer à superviser.
1. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.
  
    ![Ajouter des journaux de diagnostic](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Sélectionnez **Ajouter le paramètre de diagnostic**.
1. Dans la fenêtre **Paramètres de diagnostic** :
 
    ![Configuration des paramètres de diagnostic](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Indiquez le **Nom** de votre paramètre de diagnostic.
    1. Sélectionnez une ou plusieurs cibles : compte de stockage, Event Hub ou Log Analytics.
    1. Sélectionnez les journaux à collecter : `SucceededIngestion` ou `FailedIngestion`.
    1. Sélectionnez les [métriques](using-metrics.md#supported-azure-data-explorer-metrics) à collecter (facultatif).  
    1. Sélectionnez **Enregistrer** pour enregistrer les nouveaux paramètres et métriques des journaux de diagnostic.

Les nouveaux paramètres sont définis en quelques minutes. Les journaux apparaissent alors dans la cible d’archivage configurée (compte de stockage, Event Hub ou Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic

Tous [les journaux de diagnostic Azure Monitor partagent un schéma de niveau supérieur commun](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Data Explorer a des propriétés uniques pour leurs propres événements. Tous les journaux sont stockés dans un format JSON.

### <a name="ingestion-logs-schema"></a>Schéma des journaux d’ingestion

Les chaînes JSON des journaux incluent les éléments listés dans le tableau suivant :

|Nom               |Description
|---                |---
|time               |Heure du rapport
|resourceId         |ID de ressource Azure Resource Manager
|operationName      |Nom de l’opération : 'MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION'
|operationVersion   |Version du schéma : '1.0' 
|catégorie           |Catégorie de l’opération. `SucceededIngestion` ou `FailedIngestion`. Les propriétés sont différentes pour une [opération réussie](#successful-ingestion-operation-log) ou pour une [opération ayant échoué](#failed-ingestion-operation-log).
|properties         |Informations détaillées sur l’opération.

#### <a name="successful-ingestion-operation-log"></a>Journal des opérations d’ingestion réussies

**Exemple :**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Propriétés d’un journal de diagnostic d’une opération réussie**

|Nom               |Description
|---                |---
|succeededOn        |Heure d’achèvement de l’ingestion
|operationId        |ID d’opération de l’ingestion Azure Data Explorer
|database           |Nom de la base de données cible
|table              |Nom de la table cible
|ingestionSourceId  |ID de la source de données d’ingestion
|ingestionSourcePath|Chemin de l’URI d’objet blob ou de la source de données d’ingestion
|rootActivityId     |ID d’activité

#### <a name="failed-ingestion-operation-log"></a>Journal des opérations d’ingestion ayant échoué

**Exemple :**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Propriétés d’un journal de diagnostic d’une opération ayant échoué**

|Nom               |Description
|---                |---
|failedOn           |Heure d’achèvement de l’ingestion
|operationId        |ID d’opération de l’ingestion Azure Data Explorer
|database           |Nom de la base de données cible
|table              |Nom de la table cible
|ingestionSourceId  |ID de la source de données d’ingestion
|ingestionSourcePath|Chemin de l’URI d’objet blob ou de la source de données d’ingestion
|rootActivityId     |ID d’activité
|details            |Description détaillée de l’échec et du message d’erreur
|errorCode          |Code d'erreur 
|failureStatus      |`Permanent` ou `Transient`. Une nouvelle tentative après un échec passager peut réussir.
|originatesFromUpdatePolicy|True si l’échec provient d’une stratégie de mise à jour
|shouldRetry        |True si une nouvelle tentative peut réussir

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Ingérer et interroger des données de supervision dans Azure Data Explorer](ingest-data-no-code.md)
* [Utiliser des métriques pour superviser l’intégrité du cluster](using-metrics.md)


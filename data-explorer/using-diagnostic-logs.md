---
title: Superviser l’ingestion, les commandes et les requêtes Azure Data Explorer à l’aide des journaux de diagnostic
description: Découvrez comment configurer les journaux de diagnostic d’Azure Data Explorer pour superviser les commandes d’ingestion et les opérations de requête.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/07/2021
ms.openlocfilehash: 5142b6abfb5a7898fe58cd6264251e57dc95ae81
ms.sourcegitcommit: e37f52d6a4f6e782471b44ce21f978e2d83ffc28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98068702"
---
# <a name="monitor-azure-data-explorer-ingestion-commands-queries-and-tables-using-diagnostic-logs"></a>Superviser l’ingestion, les commandes, les requêtes et les tables Azure Data Explorer à l’aide des journaux de diagnostic

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Les [journaux de diagnostic Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) fournissent des données sur les opérations effectuées sur des ressources Azure. Azure Data Explorer utilise les journaux de diagnostic pour obtenir des insights sur l’ingestion, les commandes, les requêtes et les tables. Vous pouvez exporter les journaux des opérations vers Stockage Azure, Event Hub ou Log Analytics afin de superviser l’ingestion, les commandes et l’état des requêtes. Les journaux de Stockage Azure et d’Azure Event Hub peuvent être routés vers une table de votre cluster Azure Data Explorer pour une analyse plus poussée.

> [!IMPORTANT] 
> Les données des journaux de diagnostic peuvent contenir des données sensibles. Limitez les autorisations de la destination des journaux en fonction de vos besoins de supervision. 

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas encore d’abonnement Azure, [créez un compte Azure gratuit](https://azure.microsoft.com/free/)
* Connectez-vous au [portail Azure](https://portal.azure.com/).
* Créez [un cluster et une base de données](create-cluster-database-portal.md).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Configurer les journaux de diagnostic pour un cluster Azure Data Explorer

Vous pouvez utiliser les journaux de diagnostic pour configurer la collecte des données de journal suivantes :

# <a name="ingestion"></a>[Ingestion](#tab/ingestion)

> [!NOTE]
> Les journaux d’ingestion sont pris en charge pour l’ingestion en file d’attente sur le point de terminaison d’ingestion à l’aide de kits SDK, de connexions de données et de connecteurs.
>
> Les journaux d’ingestion ne sont pas pris en charge pour l’ingestion en streaming, l’ingestion directe vers le moteur, l’ingestion à partir d’une requête ou les commandes set-or-append.

> [!NOTE]
> L’échec des journaux d’ingestion est signalé uniquement pour l’état final d’une opération d’ingestion, contrairement à la métrique [Résultat de l’ingestion](using-metrics.md#ingestion-metrics), qui est émise pour les échecs temporaires retentés en interne.

* **Opérations d’ingestion réussies** : Ces journaux contiennent des informations sur les opérations d’ingestion terminées avec succès.
* **Opérations d’ingestion ayant échoué** : Ces journaux contiennent des informations détaillées sur les échecs d’opérations d’ingestion, notamment les détails des erreurs. 
* **Opérations de traitement par lot de l’ingestion** : Ces journaux comprennent des statistiques détaillées sur les lots prêts à être ingérés (durée, taille du lot et nombre d’objets blob).

# <a name="commands-and-queries"></a>[Commandes et requêtes](#tab/commands-and-queries)

* **Commandes** : ces journaux contiennent des informations sur les commandes d’administration qui ont atteint un état final.
* **Requêtes** : ces journaux contiennent des informations détaillées sur les requêtes qui ont atteint un état final. 

    > [!NOTE]
    > Les données des journaux de requêtes ne contiennent pas le texte de la requête.
    
# <a name="tables"></a>[Tables](#tab/tables)

* **TableUsageStatistics** : ces journaux contiennent des informations détaillées sur l’utilisation des commandes et les requêtes qui ont atteint un état final.

    > [!NOTE]
    > Les données des journaux `TableUsageStatistics` ne contiennent pas la commande ni le texte de la requête.

* **TableDetails** : ces journaux contiennent des informations détaillées sur les tables du cluster.

---

Les données sont ensuite archivées dans un compte de stockage, envoyées en streaming à un hub d’événements ou envoyées à Log Analytics, conformément à vos spécifications.

### <a name="enable-diagnostic-logs"></a>Activer les journaux de diagnostic

Les journaux de diagnostic sont désactivés par défaut. Pour activer les journaux de diagnostic, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez la ressource de cluster Azure Data Explorer à superviser.
1. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.
  
    ![Ajouter des journaux de diagnostic](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Sélectionnez **Ajouter le paramètre de diagnostic**.
1. Dans la fenêtre **Paramètres de diagnostic** :

    :::image type="content" source="media/using-diagnostic-logs/configure-diagnostics-settings.png" alt-text="Configurer les paramètres de diagnostic":::

    1. Entrez un nom dans **Nom du paramètre de diagnostic**.
    1. Sélectionnez une ou plusieurs cibles : un espace de travail Log Analytics, un compte de stockage ou un hub d’événements.
    1. Sélectionnez les journaux à collecter : `SucceededIngestion`, `FailedIngestion`, `IngestionBatching`, `Command`, ou `Query`, `TableUsageStatistics`, ou `TableDetails`.
    1. Sélectionnez les [métriques](using-metrics.md#supported-azure-data-explorer-metrics) à collecter (facultatif).  
    1. Sélectionnez **Enregistrer** pour enregistrer les nouveaux paramètres et métriques des journaux de diagnostic.

Les nouveaux paramètres sont définis en quelques minutes. Les journaux apparaissent alors dans la cible d’archivage configurée (compte de stockage, Event Hub ou Log Analytics). 

> [!NOTE]
> Si vous envoyez des journaux à Log Analytics, les journaux `SucceededIngestion`, `FailedIngestion`, `IngestionBatching`, `Command`, `Query`, `TableUsageStatistics` et `TableDetails` seront stockés dans des tables Log Analytics appelées `SucceededIngestion`, `FailedIngestion`, `ADXIngestionBatching`, `ADXCommand`, `ADXQuery`, `ADXTableUsageStatistics` et `ADXTableDetails`, respectivement.

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic

Tous [les journaux de diagnostic Azure Monitor partagent un schéma de niveau supérieur commun](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Data Explorer a des propriétés uniques pour leurs propres événements. Tous les journaux sont stockés dans un format JSON.

# <a name="ingestion"></a>[Ingestion](#tab/ingestion)

### <a name="ingestion-logs-schema"></a>Schéma des journaux d’ingestion

Les chaînes JSON des journaux incluent les éléments listés dans le tableau suivant :

|Nom               |Description
|---                |---
|time               |Heure du rapport
|resourceId         |ID de ressource Azure Resource Manager
|operationName      |Nom de l’opération : 'MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION'
|operationVersion   |Version du schéma : '1.0' 
|catégorie           |Catégorie de l’opération. `SucceededIngestion`, `FailedIngestion` ou `IngestionBatching`. Les propriétés sont différentes pour une [opération réussie](#successful-ingestion-operation-log), une [opération ayant échoué](#failed-ingestion-operation-log) et une [opération de traitement par lot](#ingestion-batching-operation-log).
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
        "SucceededOn": "2019-05-27 07:55:05.3693628",
        "OperationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "Database": "Samples",
        "Table": "StormEvents",
        "IngestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "IngestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Propriétés d’un journal de diagnostic d’une opération réussie**

|Nom               |Description
|---                |---
|SucceededOn        |Heure d’achèvement de l’ingestion
|OperationId        |ID d’opération de l’ingestion Azure Data Explorer
|Base de données           |Nom de la base de données cible
|Table de charge de travail              |Nom de la table cible
|IngestionSourceId  |ID de la source de données d’ingestion
|IngestionSourcePath|Chemin de l’URI d’objet blob ou de la source de données d’ingestion
|RootActivityId     |ID d’activité

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
|FailedOn           |Heure d’achèvement de l’ingestion
|OperationId        |ID d’opération de l’ingestion Azure Data Explorer
|Base de données           |Nom de la base de données cible
|Table de charge de travail              |Nom de la table cible
|IngestionSourceId  |ID de la source de données d’ingestion
|IngestionSourcePath|Chemin de l’URI d’objet blob ou de la source de données d’ingestion
|RootActivityId     |ID d’activité
|Détails            |Description détaillée de l’échec et du message d’erreur
|ErrorCode          |Code d'erreur 
|FailureStatus      |`Permanent` ou `Transient`. Une nouvelle tentative après un échec passager peut réussir.
|OriginatesFromUpdatePolicy|True si l’échec provient d’une stratégie de mise à jour
|ShouldRetry        |True si une nouvelle tentative peut réussir

#### <a name="ingestion-batching-operation-log"></a>Journal des opérations de traitement par lot de l’ingestion

**Exemple :**

```json
{
  "resourceId": "/SUBSCRIPTIONS/12534EB3-8109-4D84-83AD-576C0D5E1D06/RESOURCEGROUPS/KEREN/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KERENEUS",
  "time": "2020-05-27T07:55:05.3693628Z",
  "operationVersion": "1.0",
  "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGESTIONBATCHING/ACTION",
  "category": "IngestionBatching",
  "correlationId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735",
  "properties": {
    "Database": "Samples",
    "Table": "StormEvents",
    "BatchingType": "Size",
    "SourceCreationTime": "2020-05-27 07:52:04.9623640",
    "BatchTimeSeconds": 215.5,
    "BatchSizeBytes": 2356425,
    "DataSourcesInBatch": 4,
    "RootActivityId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735"
  }
}

```
**Propriétés du journal de diagnostic des opérations de traitement par lot d’une ingestion**

|Name               |Description
|---                   |---
| TimeGenerated        | Heure (UTC) à laquelle cet événement a été généré |
| Base de données             | Nom de la base de données contenant la table cible |
| Table de charge de travail                | Nom de la table cible dans laquelle les données sont ingérées |
| BatchingType         | Type de traitement par lot : indique si le lot a atteint la limite concernant le temps de traitement par lot, la taille des données ou le nombre de fichiers qui est définie par la stratégie de traitement par lot |
| SourceCreationTime   | Heure minimale (UTC) à laquelle les objets blob de ce lot ont été créés |
| BatchTimeSeconds     | Temps total de traitement de ce lot (en secondes) |
| BatchSizeBytes       | Taille totale des données de ce lot, après décompression (en octets) |
| DataSourcesInBatch   | Nombre de sources de données de ce lot |
| RootActivityId       | ID d’activité de l’opération |


# <a name="commands-and-queries"></a>[Commandes et requêtes](#tab/commands-and-queries)

### <a name="commands-and-queries-logs-schema"></a>Schéma des journaux de commandes et de requêtes

Les chaînes JSON des journaux incluent les éléments listés dans le tableau suivant :

|Nom               |Description
|---                |---
|time               |Heure du rapport
|resourceId         |ID de ressource Azure Resource Manager
|operationName      |Nom de l’opération : ’MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION’ ou "MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION". Les propriétés diffèrent pour les journaux de commandes et de requêtes.
|operationVersion   |Version du schéma : '1.0' 
|catégorie           |Catégorie de l’opération : `Command` ou `Query`. Les propriétés diffèrent pour les journaux de commandes et de requêtes.
|properties         |Informations détaillées sur l’opération.

#### <a name="command-log"></a>Journal des commandes

**Exemple :**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION",
    "operationVersion": "1.0",
    "category": "Command",
    "properties":
    {
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d",
        "StartedOn": "2020-09-12T18:06:04.6898353Z",
        "LastUpdatedOn": "2020-09-12T18:06:04.6898353Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "XXX",
        "TotalCpu": "00:01:30.1562500",
        "CommandType": "ExtentsDrop",
        "Application": "Kusto.WinSvc.DM.Svc",
        "ResourceUtilization": "{\"CacheStatistics\":{\"Memory\":{\"Hits\":0,\"Misses\":0},\"Disk\":{\"Hits\":0,\"Misses\":0},\"Shards\":{\"Hot\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"Cold\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"BypassBytes\":0}},\"TotalCpu\":\"00:00:00\",\"MemoryPeak\":0,\"ScannedExtentsStatistics\":{\"MinDataScannedTime\":null,\"MaxDataScannedTime\":null,\"TotalExtentsCount\":0,\"ScannedExtentsCount\":0,\"TotalRowsCount\":0,\"ScannedRowsCount\":0}}",
        "Duration": "00:03:30.1562500",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a3b4136b53;5c443533-c927-4410-a5d6-4d6a5443b64f"
    }
}
```
**Propriétés d’un journal de diagnostic des commandes**

|Nom               |Description
|---                |---
|RootActivityId |ID de l’activité racine
|StartedOn        |Heure (UTC) à laquelle cette commande a démarré
|LastUpdatedOn        |Heure (UTC) à laquelle cette commande s’est terminée
|Base de données          |Nom de la base de données sur laquelle la commande a été exécutée
|State              |État avec lequel la commande s’est terminée
|FailureReason  |Raison de l’échec
|TotalCpu |Durée processeur totale
|CommandType     |Type de commande
|Application     |Nom de l’application qui a appelé la commande
|ResourceUtilization     |Utilisation de la ressource de commande
|Duration     |Durée de la commande
|Utilisateur     |Utilisateur ayant appelé la requête
|Principal     |Principal ayant appelé la requête

#### <a name="query-log"></a>Journal des requêtes

**Exemple :**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION",
    "operationVersion": "1.0",
    "category": "Query",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-09-04T13:45:45.331925Z",
        "LastUpdatedOn": "2020-09-04T13:45:45.3484372Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "[none]",
        "TotalCpu": "00:00:00",
        "ApplicationName": "MyApp",
        "MemoryPeak": 0,
        "Duration": "00:00:00.0165122",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf",
        "ScannedExtentsStatistics": {
            "MinDataScannedTime": "2020-07-27T08:34:35.3299941",
            "MaxDataScannedTime": "2020-07-27T08:34:41.991661",
            "TotalExtentsCount": 64,
            "ScannedExtentsCount": 64,
            "TotalRowsCount": 320,
            "ScannedRowsCount": 320
        },
        "CacheStatistics": {
            "Memory": {
                "Hits": 192,
                "Misses": 0
          },
            "Disk": {
                "Hits": 0,
                "Misses": 0
      },
            "Shards": {
                "Hot": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
                "Cold": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
            "BypassBytes": 0
      }
    },
    "ResultSetStatistics": {
        "TableCount": 1,
        "TablesStatistics": [
        {
          "RowCount": 1,
          "TableSize": 9
        }
      ]
    }
  }
}
```

**Propriétés d’un journal de diagnostic des requêtes**

|Nom               |Description
|---                |---
|RootActivityId |ID de l’activité racine
|StartedOn        |Heure (UTC) à laquelle cette commande a démarré
|LastUpdatedOn           |Heure (UTC) à laquelle cette commande s’est terminée
|Base de données              |Nom de la base de données sur laquelle la commande a été exécutée
|State  |État avec lequel la commande s’est terminée
|FailureReason|Raison de l’échec
|TotalCpu     |Durée processeur totale
|ApplicationName            |Nom de l’application ayant appelé la requête
|MemoryPeak          |Pic de mémoire
|Duration      |Durée de la commande
|Utilisateur|Utilisateur ayant appelé la requête
|Principal        |Principal ayant appelé la requête
|ScannedExtentsStatistics        | Contient des statistiques sur les étendues analysées
|MinDataScannedTime        |Durée minimale d’analyse des données
|MaxDataScannedTime        |Durée maximale d’analyse des données
|TotalExtentsCount        |Nombre total d’étendues
|ScannedExtentsCount        |Nombre d’étendues analysées
|TotalRowsCount        |Nombre total de lignes
|ScannedRowsCount        |Nombre de lignes analysées
|CacheStatistics        |Contient des statistiques sur le cache
|Mémoire        |Contient des statistiques sur la mémoire cache
|Correspondances        |Correspondances dans le cache mémoire
|Absences        |Absences dans le cache mémoire
|Disque        |Contient des statistiques sur le cache de disque
|Correspondances        |Correspondances dans le cache de disque
|Absences        |Absences dans le cache de disque
|Partitions        |Contient des statistiques sur le cache des partitions à chaud et à froid
|À chaud        |Contient des statistiques sur le cache des partitions à chaud
|HitBytes        |Correspondances dans le cache des partitions à chaud
|MissBytes        |Absences dans le cache des partitions à chaud
|RetrieveBytes        | Octets récupérés dans le cache des partitions à chaud
|Froid        |Contient des statistiques sur le cache des partitions à froid
|HitBytes        |Correspondances dans le cache des partitions à froid
|MissBytes        |Absences dans le cache des partitions à froid
|RetrieveBytes        |Octets récupérés dans le cache des partitions à froid
|BypassBytes        |Octets de contournement du cache des partitions à froid
|ResultSetStatistics        |Contient des statistiques sur les jeux de résultats
|TableCount        |Nombre de tables du jeu de résultats
|TablesStatistics        |Contient des statistiques sur les tables des jeux de résultats
|RowCount        | Nombre de lignes de la table du jeu de résultats
|TableSize        |Nombre de lignes de la table du jeu de résultats


# <a name="tables"></a>[Tables](#tab/tables)

### <a name="tableusagestatistics-and-tabledetails-logs-schema"></a>Schéma des journaux TableUsageStatistics et TableDetails

Les chaînes JSON des journaux incluent les éléments listés dans le tableau suivant :

|Nom               |Description
|---                |---
|time               |Heure du rapport
|resourceId         |ID de ressource Azure Resource Manager
|operationName      |Nom de l’opération : 'MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ'. Les propriétés sont les mêmes pour TableUsageStatistics et TableDetails.
|operationVersion   |Version du schéma : '1.0' 
|properties         |Informations détaillées sur l’opération

#### <a name="tableusagestatistics-log"></a>Journal TableUsageStatistics

**Exemple :**

```json
{
    "resourceId": "/SUBSCRIPTIONS/0571b364-eeeb-4f28-ba74-90a8b4132b53/RESOURCEGROUPS/MYRG/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/MYKUSTOCLUSTER",
    "time": "08-04-2020 16:42:29",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ",
    "correlationId": "MyApp.Kusto.DM.MYKUSTOCLUSTER.ShowTableUsageStatistics.e10fe80b-6f4d-4b7e-9756-b87720f88901",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-08-19T11:51:41.1258308Z",
        "Database": "MyDB",
        "Table": "MyTable",
        "MinCreatedOn": "2020-07-20T09:16:00.9906347Z",
        "MaxCreatedOn": "2020-08-19T11:50:37.1233374Z",
        "Application": "MyApp",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf"
    }
}
```

**Propriétés d’un journal de diagnostic TableUsageStatistics**

|Nom               |Description
|---                |---
|RootActivityId |ID de l’activité racine
|StartedOn        |Heure (UTC) à laquelle cette commande a démarré
|Base de données          |Le nom de la base de données,
|TableName              |Le nom de la table
|MinCreatedOn  |Heure de la plus ancienne extension de la table
|MaxCreatedOn |Heure de l’extension la plus récente de la table
|ApplicationName     |Nom de l’application qui a appelé la commande
|Utilisateur     |Utilisateur ayant appelé la requête
|Principal     |Principal ayant appelé la requête

#### <a name="tabledetails-log"></a>Journal TableDetails

**Exemple :**

```json
{
    "resourceId": "/SUBSCRIPTIONS/0571b364-eeeb-4f28-ba74-90a8b4132b53/RESOURCEGROUPS/MYRG/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/MYKUSTOCLUSTER",
    "time": "08-04-2020 16:42:29",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ",
    "correlationId": "MyApp.Kusto.DM.MYKUSTOCLUSTER.ShowTableUsageStatistics.e10fe80b-6f4d-4b7e-9756-b87720f88901",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "TableName": "MyTable",
        "DatabaseName": "MyDB",
        "TotalExtentSize": 9632.0,
        "TotalOriginalSize": 4143.0,
        "HotExtentSize": 0.0,
        "RetentionPolicyOrigin": "table",
        "RetentionPolicy": "{\"SoftDeletePeriod\":\"90.00:00:00\",\"Recoverability\":\"Disabled\"}",
        "CachingPolicyOrigin": "database",
        "CachingPolicy": "{\"DataHotSpan\":\"7.00:00:00\",\"IndexHotSpan\":\"7.00:00:00\",\"ColumnOverrides\":[]}",
        "MaxExtentsCreationTime": "2020-08-30T02:44:43.9824696Z",
        "MinExtentsCreationTime": "2020-08-30T02:38:42.3031288Z",
        "TotalExtentCount": 1164,
        "TotalRowCount": 223325,
        "HotExtentCount": 29,
        "HotOriginalSize": 1388213,
        "HotRowCount": 5117
  }
}
```

**Propriétés d’un journal de diagnostic TableDetails**

|Nom               |Description
|---                |---
|RootActivityId |ID de l’activité racine
|TableName        |Le nom de la table
|nom_base_de_données           |Nom de la base de données
|TotalExtentSize              |Taille totale d’origine des données dans la table (en octets)
|HotExtentSize  |Taille totale, en octets, des extensions (taille compressée et taille d’index) de la table, stockées dans le cache chaud.
|RetentionPolicyOrigin |Entité d’origine de la stratégie de conservation (table/base de données/cluster)
|RetentionPolicy     |Stratégie de conservation d’entité effective de la table, sérialisée au format JSON
|CachingPolicyOrigin            |Entité d’origine de la stratégie de mise en cache (table/base de données/cluster)
|CachingPolicy          |Stratégie de mise en cache d’entité effective de la table, sérialisée au format JSON
|MaxExtentsCreationTime      |Heure de création maximale d’une extension dans la table (ou null, en l’absence d’extension)
|MinExtentsCreationTime |Heure de création minimale d’une extension dans la table (ou null, en l’absence d’extension)
|TotalExtentCount        |Nombre total d’extensions dans la table
|TotalRowCount        |Nombre total de lignes dans la table
|MinDataScannedTime        |Durée minimale d’analyse des données
|MaxDataScannedTime        |Durée maximale d’analyse des données
|TotalExtentsCount        |Nombre total d’étendues
|ScannedExtentsCount        |Nombre d’étendues analysées
|TotalRowsCount        |Nombre total de lignes
|HotExtentCount        |Nombre total d’extensions dans la table, stockées dans le cache chaud
|HotOriginalSize        |Taille totale d’origine, en octets, des données dans la table, stockées dans le cache chaud
|HotRowCount        |Nombre total de lignes dans la table, stockées dans le cache chaud

---

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des métriques pour superviser l’intégrité du cluster](using-metrics.md)
* [Tutoriel : Ingérer et interroger des données de supervision dans Azure Data Explorer](ingest-data-no-code.md) pour les journaux de diagnostic d’ingestion

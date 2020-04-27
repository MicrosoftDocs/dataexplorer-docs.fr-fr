---
title: 'Tutoriel : Ingérer des données de supervision dans Azure Data Explorer sans code'
description: Dans ce tutoriel, vous allez apprendre à ingérer des données de supervision dans Azure Data Explorer sans une seule ligne de code et découvrir comment interroger ces données.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 59a42c2a3e4efa8c8642bccf96b0040767753e65
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108335"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Tutoriel : Ingérer et interroger des données de supervision dans Azure Data Explorer 

Ce tutoriel va vous apprendre à ingérer des données de journaux de diagnostic et d’activité dans un cluster Azure Data Explorer sans écrire de code. Avec cette méthode d’ingestion simple, vous pouvez commencer rapidement à interroger Azure Data Explorer pour l’analyse de données.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer des tables et un mappage d’ingestion dans une base de données Azure Data Explorer
> * Mettre en forme les données ingérées à l’aide d’une stratégie de mise à jour
> * Créer un [hub d’événements](/azure/event-hubs/event-hubs-about) et le connecter à Azure Data Explorer
> * Envoyer en streaming des données vers un hub d’événements à partir de [métriques et journaux de diagnostic](/azure/azure-monitor/platform/diagnostic-settings) et de [journaux d’activité](/azure/azure-monitor/platform/activity-logs-overview) Azure Monitor.
> * Interroger les données ingérées à l’aide d’Azure Data Explorer

> [!NOTE]
> Créez toutes les ressources dans le même emplacement ou la même région Azure. 

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md). Dans ce tutoriel, le nom de la base de données est *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Fournisseur de données Azure Monitor : métriques de diagnostic, journaux de diagnostic et journaux d’activité

Visualisez et interprétez les données fournies par les métriques de diagnostic, les journaux de diagnostic et les journaux d’activité Azure Monitor ci-dessous. Vous allez créer un pipeline d’ingestion basé sur ces schémas de données. Notez que chaque événement dans un journal a un tableau d’enregistrements. Ce tableau d’enregistrements sera fractionné plus tard dans le tutoriel.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Exemples de métriques de diagnostics, de journaux de diagnostics et de journaux d’activité

Les métriques et journaux de diagnostic Azure sont émis par un service Azure et fournissent des données sur le fonctionnement de ce service. 

# <a name="diagnostic-metrics"></a>[Métriques de diagnostic](#tab/diagnostic-metrics)
#### <a name="example"></a>Exemple

Les métriques de diagnostic sont agrégées avec un fragment de temps de 1 minute. Voici un exemple de schéma d’événement de métrique Azure Data Explorer pour la durée d’une requête :

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[Journaux de diagnostic](#tab/diagnostic-logs)
#### <a name="example"></a>Exemple

Voici un exemple de [journal d’ingestion des diagnostics](using-diagnostic-logs.md#diagnostic-logs-schema) Azure Data Explorer :

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[Journaux d’activité](#tab/activity-logs)
#### <a name="example"></a>Exemple

Les journaux d’activité Azure sont des journaux de niveau d’abonnement qui fournissent un insight des opérations effectuées sur les ressources de votre abonnement. Voici un exemple d’événement de journal d’activité pour une opération de vérification d’accès :

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Configurer un pipeline d’ingestion dans Azure Data Explorer

La configuration d’un pipeline Azure Data Explorer implique plusieurs étapes, comme la [création de tables et l’ingestion de données](/azure/data-explorer/ingest-sample-data#ingest-data). Vous pouvez également manipuler, mapper et mettre à jour les données.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Se connecter à l’interface utilisateur web d’Azure Data Explorer

Dans votre base de données Azure Data Explorer *TestDatabase*, sélectionnez **Requête** pour ouvrir l’interface utilisateur web d’Azure Data Explorer.

![Page de requête](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Créer les tables cibles

Les journaux Azure Monitor n’ont pas une structure tabulaire. Vous allez manipuler les données et développer chaque événement vers un ou plusieurs enregistrements. Les données brutes seront ingérées dans une table intermédiaire nommée *ActivityLogsRawRecords* pour les journaux d’activité et *DiagnosticRawRecords* pour les métriques et journaux de diagnostic. À ce moment-là, les données vont être manipulées et développées. Quand vous utilisez une stratégie de mise à jour, les données étendues sont ensuite ingérées dans la table *ActivityLogs* pour les journaux d’activité, dans la table *DiagnosticMetrics* pour les métriques de diagnostic et dans la table *DiagnosticLogs* pour les journaux de diagnostic. Vous devez donc créer deux tables distinctes pour l’ingestion des journaux d’activité et trois tables distinctes pour l’ingestion des métriques et journaux de diagnostic.

Utilisez l’interface utilisateur web d’Azure Data Explorer pour créer les tables cibles dans la base de données Azure Data Explorer.

# <a name="diagnostic-metrics"></a>[Métriques de diagnostic](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Créer des tables pour les métriques de diagnostic

1. Dans la base de données *TestDatabase*, créez une table nommée *DiagnosticMetrics* pour stocker les enregistrements des métriques de diagnostic. Utilisez la commande de contrôle `.create table` suivante :

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Sélectionnez **Exécuter** pour créer la table.

    ![Exécuter une requête](media/ingest-data-no-code/run-query.png)

1. Créez la table de données intermédiaire nommée *DiagnosticRawRecords* dans la base de données *TestDatabase* pour la manipulation des données à l’aide de la requête suivante. Sélectionnez **Exécuter** pour créer la table.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Ne définissez aucune [stratégie de conservation](kusto/management/retention-policy.md) pour la table intermédiaire :

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Journaux de diagnostic](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Créer des tables pour les journaux de diagnostic 

1. Dans la base de données *TestDatabase*, créez une table nommée *DiagnosticLogs* pour stocker les enregistrements des journaux de diagnostic. Utilisez la commande de contrôle `.create table` suivante :

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Sélectionnez **Exécuter** pour créer la table.

1. Créez la table de données intermédiaire nommée *DiagnosticRawRecords* dans la base de données *TestDatabase* pour la manipulation des données à l’aide de la requête suivante. Sélectionnez **Exécuter** pour créer la table.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Ne définissez aucune [stratégie de conservation](kusto/management/retention-policy.md) pour la table intermédiaire :

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Journaux d’activité](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Créer des tables pour les journaux d’activité 

1. Créez une table nommée *ActivityLogs* dans la base de données *TestDatabase* pour recevoir les enregistrements des journaux d’activité. Pour créer la table, exécutez la requête Azure Data Explorer suivante :

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Créez la table de données intermédiaire nommée *ActivityLogsRawRecords* dans la base de données *TestDatabase* pour la manipulation des données :

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Ne définissez aucune [stratégie de conservation](kusto/management/retention-policy.md) pour la table intermédiaire :

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Créer des mappages de table

 Comme le format des données est `json`, un mappage de données est nécessaire. Le mappage `json` mappe chaque chemin JSON à un nom de colonne de table.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Métriques de diagnostic/Journaux de diagnostic](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Mapper des métriques et journaux de diagnostics à la table

Pour mapper les données des métriques et journaux de diagnostic à la table, utilisez la requête suivante :

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[Journaux d’activité](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Mapper des journaux d’activité à la table

Pour mapper les données des journaux d’activité à la table, utilisez la requête suivante :

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Créer la stratégie de mise à jour pour les données des métriques et journaux

# <a name="diagnostic-metrics"></a>[Métriques de diagnostic](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Créer une stratégie de mise à jour des données pour les métriques de diagnostic

1. Créez une [fonction](kusto/management/functions.md) qui développe la collection des enregistrements de métriques de diagnostic pour que chaque valeur de la collection reçoive une ligne distincte. Utilisez l’opérateur [`mv-expand`](kusto/query/mvexpandoperator.md) :
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Ajoutez la [stratégie de mise à jour](kusto/management/updatepolicy.md) à la table cible. Cette stratégie exécute automatiquement la requête sur toutes les nouvelles données ingérées dans la table de données intermédiaire *DiagnosticRawRecords*, et ingère ses résultats dans la table *DiagnosticMetrics* :

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Journaux de diagnostic](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Créer une stratégie de mise à jour des données pour les journaux de diagnostic

1. Créez une [fonction](kusto/management/functions.md) qui développe la collection des enregistrements de journaux de diagnostic pour que chaque valeur de la collection reçoive une ligne distincte. Vous allez activer les journaux d’ingestion sur un cluster Azure Data Explorer et utiliser un [schéma des journaux d’ingestion](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema). Vous allez créer une table pour les opérations d’ingestion réussies et ayant échoué, alors que certains champs seront vides pour l’ingestion réussie (ErrorCode, par exemple). Utilisez l’opérateur [`mv-expand`](kusto/query/mvexpandoperator.md) :

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Ajoutez la [stratégie de mise à jour](kusto/management/updatepolicy.md) à la table cible. Cette stratégie exécute automatiquement la requête sur toutes les nouvelles données ingérées dans la table de données intermédiaire *DiagnosticRawRecords*, et ingère ses résultats dans la table *DiagnosticLogs* :

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Journaux d’activité](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Créer une stratégie de mise à jour des données pour les journaux d’activité

1. Créez une [fonction](kusto/management/functions.md) qui développe la collection des enregistrements de journaux d’activité pour que chaque valeur de la collection reçoive une ligne distincte. Utilisez l’opérateur [`mv-expand`](kusto/query/mvexpandoperator.md) :

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Ajoutez la [stratégie de mise à jour](kusto/management/updatepolicy.md) à la table cible. Cette stratégie exécute automatiquement la requête sur toutes les données récemment ingérées dans la table de données intermédiaire *ActivityLogsRawRecords*, et ingère ses résultats dans la table *ActivityLogs* :

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Créer un espace de noms Azure Event Hubs

Les paramètres de diagnostic Azure permettent d’exporter des métriques et des journaux vers un compte de stockage ou vers un hub d’événements. Dans ce tutoriel, nous allons router les métriques et les journaux par le biais d’un hub d’événements. Dans les étapes suivantes, vous allez créer un espace de noms Event Hubs ainsi qu’un hub d’événements pour les métriques et journaux de diagnostic. Azure Monitor va créer le hub d’événements *insights-operational-logs* pour les journaux d’activité.

1. Créez un hub d’événements à l’aide d’un modèle Azure Resource Manager dans le portail Azure. Pour suivre le reste des étapes de l’article, cliquez avec le bouton droit sur le bouton **Déployer sur Azure**, puis sélectionnez **Ouvrir dans une nouvelle fenêtre**. Le bouton **Déployer sur Azure** vous permet d’accéder au Portail Azure.

    [![Bouton Déployer sur Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Créez un espace de noms Event Hubs ainsi qu’un hub d’événements pour les journaux de diagnostic.

    ![Création d’un hub d’événements](media/ingest-data-no-code/event-hub.png)

1. Renseignez le formulaire avec les informations suivantes. Pour tous les paramètres non listés dans le tableau suivant, utilisez les valeurs par défaut.

    **Paramètre** | **Valeur suggérée** | **Description**
    |---|---|---|
    | **Abonnement** | *Votre abonnement* | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre hub d’événements.|
    | **Groupe de ressources** | *test-resource-group* | Créez un groupe de ressources. |
    | **Lieu** | Sélectionnez la région qui répond le mieux à vos besoins. | Créez l’espace de noms Event Hubs au même emplacement que les autres ressources.
    | **Nom de l’espace de noms** | *AzureMonitoringData* | Choisissez un nom unique qui identifie votre espace de noms.
    | **Nom du hub d’événements** | *DiagnosticData* | Le hub d’événements se trouve sous l’espace de noms, qui fournit un conteneur d’étendue unique. |
    | **Nom du groupe de consommateurs** | *adxpipeline* | Créez un nom de groupe de consommateurs. Les groupes de consommateurs permettent que chacune des applications de consommation ait une vue distincte du flux d’événements. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Connecter des métriques et journaux Azure Monitor à votre hub d’événements

Vous devez maintenant connecter vos métriques et journaux de diagnostic et vos journaux d’activité au hub d’événements.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Métriques de diagnostic/Journaux de diagnostic](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Connecter des métriques et journaux de diagnostic à votre hub d’événements

Sélectionnez une ressource à partir de laquelle exporter les métriques. Plusieurs types de ressources prennent en charge l’exportation des données de diagnostic, notamment l’espace de noms Event Hubs, Azure Key Vault, Azure IoT Hub et les clusters Azure Data Explorer. Dans ce tutoriel, nous allons utiliser un cluster Azure Data Explorer comme ressource. Nous allons également examiner les métriques de performances des requêtes et les journaux de résultats de l’ingestion.

1. Sélectionnez votre cluster Kusto dans le Portail Azure.
1. Sélectionnez **Paramètres de diagnostic**, puis le lien **Activer les diagnostics**. 

    ![Paramètres de diagnostic](media/ingest-data-no-code/diagnostic-settings.png)

1. Le volet **Paramètres de diagnostic** s’ouvre. Procédez comme suit :
   1. Donnez à vos données de journaux de diagnostic le nom *ADXExportedData*.
   1. Sous **JOURNAL**, cochez les deux cases **SucceededIngestion** et **FailedIngestion**.
   1. Sous **MÉTRIQUE**, cochez la case **Performances de requête**.
   1. Cochez la case **Diffuser vers Event Hub**.
   1. Sélectionnez **Configurer**.

      ![Volet Paramètres de diagnostic](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Dans le volet **Sélectionner un hub d’événements** , configurez la façon d’exporter des données de journaux de diagnostic vers le hub d’événements que vous avez créé :
    1. Dans la liste **Sélectionner l’espace de noms de Event Hub**, sélectionnez *AzureMonitoringData*.
    1. Dans la liste **Sélectionner un nom d’Event Hub**, sélectionnez *DiagnosticData*.
    1. Dans la liste **Sélectionner un nom de stratégie de hub d’événements**, sélectionnez **RootManagerSharedAccessKey**.
    1. Sélectionnez **OK**.

1. Sélectionnez **Enregistrer**.

# <a name="activity-logs"></a>[Journaux d’activité](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Connecter des journaux d’activité à votre hub d’événements

1. Dans le menu de gauche du portail Azure, sélectionnez **Journal d’activité**.
1. La fenêtre **Journal d’activité** s’ouvre. Sélectionnez **Exporter vers Event Hub**.

    ![Fenêtre Journal d’activité](media/ingest-data-no-code/activity-log.png)

1. La fenêtre **Exporter le journal d’activité** s’ouvre :
 
    ![Fenêtre Exporter le journal d’activité](media/ingest-data-no-code/export-activity-log.png)

1. Dans la fenêtre **Exporter le journal d’activité**, effectuez les étapes suivantes :
      1. Sélectionnez votre abonnement.
      1. Dans la liste **Régions**, choisissez **Sélectionner tout**.
      1. Cochez la case **Exporter vers un hub d’événements**.
      1. Choisissez **Sélectionnez un espace de noms Service Bus** pour ouvrir le volet **Sélectionner un hub d’événements**.
      1. Dans le volet **Sélectionner un hub d’événements** , sélectionnez votre abonnement.
      1. Dans la liste **Sélectionner l’espace de noms de Event Hub**, sélectionnez *AzureMonitoringData*.
      1. Dans la liste **Sélectionner un nom de stratégie de hub d’événements**, sélectionnez le nom de stratégie de hub d’événements par défaut.
      1. Sélectionnez **OK**.
      1. Dans l’angle supérieur gauche de la fenêtre, sélectionnez **Enregistrer**.
   Un hub d’événements nommé *insights-operational-logs* est créé.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Visualiser le flux des données vers vos hubs d’événements

1. Attendez quelques minutes jusqu’à ce que la connexion soit définie et que l’exportation des journaux d’activité vers le hub d’événements soit terminée. Accédez à votre espace de noms Event Hubs pour voir les hubs d’événements que vous avez créés.

    ![Hubs d’événements créés](media/ingest-data-no-code/event-hubs-created.png)

1. Visualisez le flux des données vers votre hub d’événements :

    ![Données du hub d’événements](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Connecter un hub d’événements à Azure Data Explorer

Vous devez maintenant créer les connexions de données pour vos métriques et journaux de diagnostic et pour vos journaux d’activité.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Créer les connexions de données pour les métriques et journaux de diagnostic et pour les journaux d’activité

1. Dans votre cluster Azure Data Explorer nommé *kustodocs*, sélectionnez **Bases de données** dans le menu de gauche.
1. Dans la fenêtre **Bases de données**, sélectionnez votre base de données *TestDatabase*.
1. Dans le menu de gauche, sélectionnez **Ingestion des données**.
1. Dans la fenêtre **Ingestion des données**, cliquez sur **+ Ajouter la connexion de données**.
1. Dans la fenêtre **Connexion aux données**, entrez les informations suivantes :

    ![Connexion des données du hub d’événements](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Métriques de diagnostic/Journaux de diagnostic](#tab/diagnostic-metrics+diagnostic-logs) 

1. Utilisez les paramètres suivants dans la fenêtre **Connexion de données** :

    Source de données :

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | **Nom de la connexion de données** | *DiagnosticsLogsConnection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure.|
    | **Espace de noms du hub d’événements** | *AzureMonitoringData* | Nom choisi précédemment qui identifie votre espace de noms. |
    | **Hub d’événements** | *DiagnosticData* | Hub d’événements que vous avez créé. |
    | **Groupe de consommateurs** | *adxpipeline* | Groupe de consommateurs défini dans le hub d’événements que vous avez créé. |
    | | |

    Table cible :

    Deux options sont disponibles pour le routage : *statique* et *dynamique*. Pour ce tutoriel, vous allez utiliser le routage statique (valeur par défaut), où vous spécifiez le nom de table, le format des données et le mappage. Ne sélectionnez pas **My data includes routing info** (Mes données incluent des informations de routage).

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | **Table** | *DiagnosticRawRecords* | Table que vous avez créée dans la base de données *TestDatabase*. |
    | **Format de données** | *JSON* | Format utilisé dans la table. |
    | **Mappage de colonnes** | *DiagnosticRawRecordsMapping* | Mappage que vous avez créé dans la base de données *TestDatabase*. Il mappe les données JSON entrantes aux noms de colonnes et aux types de données de la table *DiagnosticRawRecords*.|
    | | |

1. Sélectionnez **Create** (Créer).  

# <a name="activity-logs"></a>[Journaux d’activité](#tab/activity-logs)

1. Utilisez les paramètres suivants dans la fenêtre **Connexion de données** :

    Source de données :

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | **Nom de la connexion de données** | *ActivityLogsConnection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure.|
    | **Espace de noms du hub d’événements** | *AzureMonitoringData* | Nom choisi précédemment qui identifie votre espace de noms. |
    | **Hub d’événements** | *insights-operational-logs* | Hub d’événements que vous avez créé. |
    | **Groupe de consommateurs** | *$Default* | Groupe de consommateurs par défaut. Si nécessaire, vous pouvez créer un autre groupe de consommateurs. |
    | | |

    Table cible :

    Deux options sont disponibles pour le routage : *statique* et *dynamique*. Pour ce tutoriel, vous allez utiliser le routage statique (valeur par défaut), où vous spécifiez le nom de table, le format des données et le mappage. Ne sélectionnez pas **My data includes routing info** (Mes données incluent des informations de routage).

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | **Table** | *ActivityLogsRawRecords* | Table que vous avez créée dans la base de données *TestDatabase*. |
    | **Format de données** | *JSON* | Format utilisé dans la table. |
    | **Mappage de colonnes** | *ActivityLogsRawRecordsMapping* | Mappage que vous avez créé dans la base de données *TestDatabase*. Il mappe les données JSON entrantes aux noms de colonnes et aux types de données de la table *ActivityLogsRawRecords*.|
    | | |

1. Sélectionnez **Create** (Créer).  
---

## <a name="query-the-new-tables"></a>Interroger les nouvelles tables

Vous avez maintenant un pipeline avec un flux de données. Par défaut, l’ingestion via le cluster prend 5 minutes. Vous devez donc laisser les données circuler quelques minutes avant de commencer l’exécution de requêtes.

# <a name="diagnostic-metrics"></a>[Métriques de diagnostic](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Interroger la table de métriques de diagnostic

La requête suivante analyse les données de durée de requête à partir des enregistrements de métriques dans Azure Data Explorer :

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Résultats de la requête :

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logs"></a>[Journaux de diagnostic](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Interroger la table des journaux de diagnostic

Ce pipeline produit des ingestions par le biais d’un hub d’événements. Vous allez examiner les résultats de ces ingestions.
La requête suivante analyse le nombre d’ingestions cumulées en une minute, avec un exemple de `Database`, `Table` et `IngestionSourcePath` pour chaque intervalle :

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Résultats de la requête :

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | `https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip`
| | |

# <a name="activity-logs"></a>[Journaux d’activité](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Interroger la table des journaux d’activité

La requête suivante analyse les données à partir des enregistrements de journaux d’activité dans Azure Data Explorer :

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Résultats de la requête :

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>Étapes suivantes

* Consultez l’article [Écrire des requêtes pour Azure Data Explorer](write-queries.md) afin d’apprendre à écrire de nombreuses autres requêtes sur les données extraites à partir d’Azure Data Explorer.
* [Superviser les opérations d’ingestion d’Azure Data Explorer à l’aide des journaux de diagnostic](using-diagnostic-logs.md)
* [Utiliser des métriques pour superviser l’intégrité du cluster](using-metrics.md)

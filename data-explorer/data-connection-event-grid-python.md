---
title: Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de Python
description: Dans cet article, vous allez apprendre à créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de Python.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: 1c81f68c8892df9a066fd22d3c5ddae2f30c9e1b
ms.sourcegitcommit: 3a2d2def8d6bf395bbbb3b84935bc58adae055b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98635971"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de Python

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
Dans cet article, vous créez une connexion de données à Event Grid pour Azure Data Explorer à l’aide de Python.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Python 3.4+](https://www.python.org/downloads/).
* [Un cluster et une base de données](create-cluster-database-python.md).
* [Un mappage des tables et des colonnes](./net-sdk-ingest-data.md#create-a-table-on-your-test-cluster).
* [Des stratégies de base de données et de table](database-table-policies-csharp.md) (facultatif).
* [Un compte de stockage avec un abonnement Event Grid](ingest-data-event-grid.md).

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Ajouter une connexion de données à Event Grid

L’exemple suivant montre comment ajouter par programmation une connexion de données à Event Grid. Consultez [Créer une connexion de données à Event Grid dans Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) pour ajouter une connexion de données à Event Grid à l’aide du Portail Azure.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
blob_storage_event_type = "Microsoft.Storage.BlobCreated"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format,
                                                                                blob_storage_event_type=blob_storage_event_type))
```
|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| client_secret | *xxxxxxxxxxxxxx* | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire. |
| resource_group_name | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| nom_cluster | *mykustocluster* | Nom de votre cluster.|
| database_name | *mykustodatabase* | Nom de la base de données cible dans votre cluster.|
| data_connection_name | *myeventhubconnect* | Nom souhaité de votre connexion de données.|
| table_name | *StormEvents* | Nom de la table cible dans la base de données cible.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nom de votre mappage de colonnes associé à la table cible.|
| data_format | *csv* | Format de données du message.|
| event_hub_resource_id | *ID de ressource* | ID de ressource de votre hub d’événements où la grille d’événements est configurée pour envoyer des événements. |
| storage_account_resource_id | *ID de ressource* | ID de ressource de votre compte de stockage qui contient les données à des fins d’ingestion. |
| consumer_group | *$Default* | Groupe de consommateurs de votre hub d’événements.|
| location | *USA Centre* | Emplacement de la ressource de connexion de données.|
| blob_storage_event_type | *Microsoft.Storage.BlobCreated* | Type d’événement qui déclenche l’ingestion. Les événements pris en charge sont les suivants : Microsoft.Storage.BlobCreated ou Microsoft.Storage.BlobRenamed. Le renommage d’objets blob est pris en charge uniquement pour le stockage ADLSv2.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]

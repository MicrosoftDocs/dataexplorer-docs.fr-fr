---
title: Ingestion d’objets blob de bout en bout dans Azure Data Explorer par le biais de Python
description: Dans cet article, vous allez apprendre à ingérer des objets blob dans Azure Data Explorer à l’aide d’un exemple de bout en bout qui utilise Python.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 520b0821eaa9955916bd8212970f73e1d5cce95e
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874611"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-python"></a>Ingestion d’objets blob de bout en bout dans Azure Data Explorer par le biais de Python

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Data Explorer est un service d’exploration de données rapide et évolutif pour les données de journal et de télémétrie. Cet article vous donne un exemple de bout en bout sur la façon d’ingérer des données à partir de Stockage Blob Azure dans Azure Data Explorer. 

Vous allez apprendre à créer par programmation un groupe de ressources, un compte de stockage et un conteneur, un Event Hub et un cluster et une base de données Azure Data Explorer. Vous apprendrez également à configurer Azure Data Explorer de manière programmatique pour ingérer des données à partir du nouveau compte de stockage.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="install-the-python-package"></a>Installer le package Python

Pour installer le package Python pour Azure Data Explorer (Kusto), ouvrez une invite de commandes dont le chemin contient Python. Exécutez ces commandes :

```
pip install azure-common
pip install azure-mgmt-resource
pip install azure-mgmt-kusto
pip install azure-mgmt-eventgrid
pip install azure-kusto-data
pip install azure-storage-blob
```
[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Exemple de code 

L’exemple de code suivant fournit un processus pas à pas qui entraîne l’ingestion de données dans Azure Data Explorer. 

Commencez par créer un groupe de ressources. Vous pouvez également créer des ressources Azure, telles qu’un compte de stockage et un conteneur, un Event Hub et un cluster et une base de données Azure Data Explorer, et ajouter des principaux. Ensuite, créez un abonnement Azure Event Grid, ainsi qu’un mappage de table et de colonne, dans la base de données Azure Data Explorer. Enfin, créez la connexion de données afin de configurer Azure Data Explorer pour ingérer des données à partir du nouveau compte de stockage.

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.resource.resources.models import DeploymentMode
import os.path
import json
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.mgmt.eventgrid import EventGridManagementClient
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
location = "West Europe"
location_small_case = "westeurope"
#Path to the Azure Resource Manager template JSON from the previous section
azure_resource_template_path = "xxxxxxxxx/template.json";

deployment_name = 'e2eexample'
resource_group_name = deployment_name + "resourcegroup"
event_hub_name = deployment_name + "eventhub"
event_hub_namespace_name = event_hub_name + "ns"
storage_account_name = deployment_name + "storage"
storage_container_name = deployment_name + "storagecontainer"
event_grid_subscription_name = deployment_name + "eventgrid"
kusto_cluster_name = deployment_name + "kustocluster"
kusto_database_name = deployment_name + "kustodatabase"
kusto_table_name = "Events"
kusto_column_mapping_name = "Events_CSV_Mapping"
kusto_data_connection_name = deployment_name + "kustoeventgridconnection"

#principals
principal_id_for_cluster = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
role_for_cluster_principal = "AllDatabasesAdmin";
tenant_id_for_cluster_principal = tenant_id;
principal_type_for_cluster = "App";
principal_id_for_database = "xxxxxxxx@xxxxxxxx.com";//User Email
role_for_database_principal = "Admin";
tenant_id_for_database_principal = tenant_id;
principal_type_for_database = "User";


credentials = ServicePrincipalCredentials(
    client_id=client_id,
    secret=client_secret,
    tenant=tenant_id
)
resource_client = ResourceManagementClient(credentials, subscription_id)

print('Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.')
resource_client.resource_groups.create_or_update(
    resource_group_name,
    {
        'location': location_small_case
    }
)

print('Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, database, and add principals by using an Azure Resource Manager template.')
#Read the Azure Resource Manager template
with open(azure_resource_template_path, 'r') as template_file_fd:
    template = json.load(template_file_fd)

parameters = {
    'eventHubNamespaceName': event_hub_namespace_name,
    'eventHubName': event_hub_name,
    'storageAccountName': storage_account_name,
    'containerName': storage_container_name,
    'kustoClusterName': kusto_cluster_name,
    'kustoDatabaseName': kusto_database_name,
    'principalIdForCluster': principal_id_for_cluster,
    'roleForClusterPrincipal': role_for_cluster_principal,
    'tenantIdForClusterPrincipal': tenant_id_for_cluster_principal,
    'principalTypeForCluster': principal_type_for_cluster,
    'principalIdForDatabase': principal_id_for_database,
    'roleForDatabasePrincipal': role_for_database_principal,
    'tenantIdForDatabasePrincipal': tenant_id_for_database_principal,
    'principalTypeForDatabase': principal_type_for_database
}
parameters = {k: {'value': v} for k, v in parameters.items()}
deployment_properties = {
    'mode': DeploymentMode.incremental,
    'template': template,
    'parameters': parameters
}

#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = resource_client.deployments.create_or_update(
    resource_group_name,
    deployment_name,
    deployment_properties
)
poller.wait()

print('Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.')
event_client = EventGridManagementClient(credentials, subscription_id)
storage_resource_id = '/subscriptions/{}/resourceGroups/{}/providers/Microsoft.Storage/storageAccounts/{}'.format(subscription_id, resource_group_name, storage_account_name)
event_hub_resource_id = '/subscriptions/{}/resourceGroups/{}/providers/Microsoft.EventHub/namespaces/{}/eventhubs/{}'.format(subscription_id, resource_group_name, event_hub_namespace_name, event_hub_name)
event_client.event_subscriptions.create_or_update(storage_resource_id, event_grid_subscription_name, {
    'destination': {
        'endpointType': 'EventHub',
        'properties': {
            'resourceId': event_hub_resource_id
        }
    },
    "filter": {
        "subjectBeginsWith": "/blobServices/default/containers/{}".format(storage_container_name),
        "includedEventTypes": ["Microsoft.Storage.BlobCreated"],
        "advancedFilters": []
    }
})


print('Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.')
kusto_uri = "https://{}.{}.kusto.windows.net".format(kusto_cluster_name, location_small_case)
database_name = kusto_database_name
kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)
kusto_client = KustoClient(kusto_connection_string_builder)
create_table_command = ".create table " + kusto_table_name + " (EventTime: datetime, EventId: int, EventSummary: string)"
kusto_client.execute_mgmt(database_name, create_table_command)

create_column_mapping_command = ".create table " + kusto_table_name + " ingestion csv mapping '" + kusto_column_mapping_name \
                                + """' '[{"Name":"EventTime","datatype":"datetime","Ordinal":0},{"Name":"EventId","datatype":"int","Ordinal":1},{"Name":"EventSummary","datatype":"string","Ordinal":2}]'"""
kusto_client.execute_mgmt(database_name, create_column_mapping_command)


print('Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.')
kusto_management_client = KustoManagementClient(credentials, subscription_id)
data_connections = kusto_management_client.data_connections
#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=kusto_cluster_name, database_name=kusto_database_name, data_connection_name=kusto_data_connection_name,
                                           parameters=EventGridDataConnection(storage_account_resource_id=storage_resource_id,
                                                                              event_hub_resource_id=event_hub_resource_id, consumer_group="$Default", location=location, table_name=kusto_table_name, mapping_rule_name=kusto_column_mapping_name, data_format="csv"))
poller.wait()
```
|**Paramètre** | **Description du champ**|
|---|---|---|
| tenant_id | Votre ID de client. Il est également appelé ID de répertoire.|
| subscription_id | ID d’abonnement que vous utilisez pour la création de ressources.|
| client_id | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| client_secret | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire. |

## <a name="test-the-code-example"></a>Tester l’exemple de code

1. Chargez un fichier dans le compte de stockage.

    ```python
    account_key = "xxxxxxxxxxxxxx"
    block_blob_service = BlockBlobService(account_name=storage_account_name, account_key=account_key)
    blob_name = "test.csv"
    blob_content = """2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm"""
    block_blob_service.create_blob_from_text(container_name=storage_container_name, blob_name=blob_name, text=blob_content)
    ```
    |**Paramètre** | **Description du champ**|
    |---|---|---|
    | account_key | Clé d’accès du compte de stockage créé de manière programmatique.|

2. Exécutez une requête de test dans Azure Data Explorer.

    ```python
    kusto_uri = "https://{}.{}.kusto.windows.net".format(kusto_cluster_name, location_small_case)
    kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)
    kusto_client = KustoClient(kusto_connection_string_builder)
    query = "{} | take 10".format(kusto_table_name)
    response = kusto_client.execute_query(kusto_database_name, query)
    print(response.primary_results[0].rows_count)
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer le groupe de ressources et les ressources de nettoyage, exécutez la commande suivante :

```python
#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = resource_client.resource_groups.delete(resource_group_name=resource_group_name)
poller.wait()
```

## <a name="next-steps"></a>Étapes suivantes

*  Pour en savoir plus sur les autres méthodes de création d’un cluster et d’une base de données, voir [Créez un cluster et une base de données Azure Data Explorer](create-cluster-database-python.md).
* Consultez la section [Ingestion des données Azure Data Explorer](ingest-data-overview.md) pour en savoir plus sur les méthodes d’ingestion.
* Pour en savoir plus sur l’application web, voir [Démarrage rapide : Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md).
* [Écrire des requêtes](write-queries.md) avec le langage de requête Kusto.
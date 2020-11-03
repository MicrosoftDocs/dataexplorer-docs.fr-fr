---
title: Créer un cluster et une base de données Azure Data Explorer avec Python
description: Découvrir comment créer un cluster et une base de données Azure Data Explorer en utilisant Python
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 91d0f6e399cc2b392e62a202cb5df16edb732f92
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92902529"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Créer un cluster et une base de données Azure Data Explorer en utilisant Python

> [!div class="op_single_selector"]
> * [Portail](create-cluster-database-portal.md)
> * [INTERFACE DE LIGNE DE COMMANDE](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [Modèle ARM](create-cluster-database-resource-manager.md)

Dans cet article, vous allez créer un cluster et une base de données Azure Data Explorer en utilisant Python. Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster, puis une ou plusieurs bases de données dans celui-ci. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir envoyer des requêtes à celle-ci.

## <a name="prerequisites"></a>Conditions préalables requises

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Une application Azure AD et un principal de service ayant accès aux ressources](/azure/active-directory/develop/howto-create-service-principal-portal). Obtenez les valeurs de `Directory (tenant) ID`, `Application ID` et `Client Secret`.

## <a name="install-python-package"></a>Installer le package Python

Pour installer le package Python pour Azure Data Explorer (Kusto), ouvrez une invite de commandes dont le chemin contient Python. Exécutez cette commande :

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Authentication
Pour exécuter les exemples de cet article, nous avons besoin d’une application Azure AD et d’un principal de service pouvant accéder aux ressources. Consultez [Créer une application Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) pour créer une application Azure AD gratuite et ajouter une attribution de rôle à l’étendue de l’abonnement. Cela montre également comment obtenir `Directory (tenant) ID`, `Application ID` et `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Créer le cluster Azure Data Explorer

1. Créez votre cluster avec la commande suivante :

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kusto_management_client = KustoManagementClient(credentials, subscription_id)

    cluster_operations = kusto_management_client.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    poller.wait()
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | nom_cluster | *mykustocluster* | Nom souhaité de votre cluster.|
   | sku_name | *Standard_D13_v2* | Référence SKU utilisée pour votre cluster. |
   | Niveau | *Standard* | Niveau de référence SKU. |
   | capacité | *number* | Nombre d’instances du cluster. |
   | resource_group_name | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |

    > [!NOTE]
    > **Créer un cluster** est une opération de longue durée. La méthode **create_or_update** retourne une instance de LROPoller. Consultez [Classe LROPoller](/python/api/msrest/msrest.polling.lropoller?view=azure-python) pour obtenir plus d’informations.

1. Exécutez la commande suivante pour vérifier si votre cluster a bien été créé :

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= cluster_name, custom_headers=None, raw=False)
    ```

Si le résultat contient `provisioningState` avec la valeur `Succeeded`, alors le cluster a correctement été créé.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Créer la base de données dans le cluster Azure Data Explorer

1. Créez votre base de données avec la commande suivante :

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.kusto.models import ReadWriteDatabase
    from datetime import timedelta

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
    
    location = 'Central US'
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    soft_delete_period = timedelta(days=3650)
    hot_cache_period = timedelta(days=3650)
    database_name = "mykustodatabase"

    kusto_management_client = KustoManagementClient(credentials, subscription_id)
    
    database_operations = kusto_management_client.databases
    database = ReadWriteDatabase(location=location,
                        soft_delete_period=soft_delete_period,
                        hot_cache_period=hot_cache_period)
    
    poller = database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = cluster_name, database_name = database_name, parameters = database)
    poller.wait()
    ```

    > [!NOTE]
    > Si vous utilisez Python version 0.4.0 ou antérieure, utilisez Database au lieu de ReadWriteDatabase.

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | nom_cluster | *mykustocluster* | Nom du cluster dans lequel la base de données est créée.|
   | database_name | *mykustodatabase* | Nom de votre base de données.|
   | resource_group_name | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |
   | soft_delete_period | *3650 days, 0:00:00* | Durée pendant laquelle les données restent disponibles pour les requêtes. |
   | hot_cache_period | *3650 days, 0:00:00* | Durée pendant laquelle les données sont conservées dans le cache. |

1. Exécutez la commande suivante pour voir la base de données que vous avez créée :

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = cluster_name, database_name = database_name)
    ```

Vous disposez maintenant d’un cluster et d’une base de données.

## <a name="clean-up-resources"></a>Nettoyer les ressources

* Si vous envisagez de suivre nos autres articles, conservez les ressources que vous avez créées.
* Pour nettoyer les ressources, supprimez le cluster. Lorsque vous supprimez un cluster, cela supprime également toutes les bases de données qu’il contient. Utilisez la commande suivante pour supprimer votre cluster :

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = cluster_name)
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des données à l'aide de la bibliothèque Python d'Azure Data Explorer](python-ingest-data.md)
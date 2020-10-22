---
title: Utiliser la fonctionnalité de base de données follower pour joindre des bases de données dans Azure Data Explorer
description: Découvrez comment joindre des bases de données dans Azure Data Explorer à l’aide de la fonctionnalité de base de données follower.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/06/2020
ms.openlocfilehash: d07dc282ba3996113903bd1b7c5ab08672d46543
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003058"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Utiliser une base de données follower pour joindre des bases de données dans Azure Data Explorer

La fonctionnalité de **base de données follower** vous permet de joindre une base de données située dans un cluster différent à votre cluster Azure Data Explorer. La **base de données follower** est jointe en mode *lecture seule*, ce qui permet d’afficher les données et d’exécuter des requêtes sur les données ingérées dans la **base de données leader**. La base de données follower synchronise les modifications apportées aux bases de données leader. En raison de la synchronisation, on constate un décalage de données de quelques secondes à quelques minutes au niveau de la disponibilité des données. La durée du décalage dépend de la taille globale des métadonnées de la base de données leader. Les bases de données leader et follower utilisent le même compte de stockage pour extraire les données. Le stockage appartient à la base de données leader. La base de données follower affiche les données sans qu’il soit nécessaire de les ingérer. Étant donné que la base de données jointe est une base de données en lecture seule, les données, les tables et les stratégies de la base de données ne peuvent pas être modifiées, à l’exception de la [stratégie de mise en cache](#configure-caching-policy), des [principaux](#manage-principals) et des [autorisations](#manage-permissions). Les bases de données jointes ne peuvent pas être supprimées. Elles doivent être détachées par le leader ou le follower avant de pouvoir être supprimées. 

L’attachement d’une base de données à un autre cluster à l’aide de la fonctionnalité de follower est utilisé en tant qu’infrastructure pour partager des données entre les organisations et les équipes. La fonctionnalité est utile pour séparer les ressources de calcul afin de protéger un environnement de production contre les cas d’utilisation hors production. Le follower peut également être utilisé pour associer le coût du cluster Azure Data Explorer au tiers qui exécute des requêtes sur les données.

## <a name="which-databases-are-followed"></a>Quelles sont les bases de données suivies ?

* Un cluster peut suivre une base de données, plusieurs bases de données ou toutes les bases de données d’un cluster leader. 
* Un cluster unique peut suivre des bases de données à partir de plusieurs clusters leader. 
* Un cluster peut contenir à la fois des bases de données follower et des bases de données leader

## <a name="prerequisites"></a>Prérequis

1. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
1. [Créez un cluster et une base de données](create-cluster-database-portal.md) pour le leader et le follower.
1. [Ingérez des données](ingest-sample-data.md) dans la base de données leader à l’aide de l’une des différentes méthodes présentées dans [Vue d’ensemble de l’ingestion](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Attacher une base de données

Vous pouvez utiliser différentes méthodes pour joindre une base de données. Dans cet article, nous traitons de l’attachement d’une base de données en utilisant C#, Python, PowerShell ou un modèle Azure Resource Manager. Pour attacher une base de données, vous devez disposer d’un utilisateur, d’un groupe, d’un principal du service ou d’une identité managée avec au moins le rôle de contributeur sur le cluster leader et le cluster follower. Vous pouvez ajouter ou supprimer des attributions de rôles avec le [portail Azure](/azure/role-based-access-control/role-assignments-portal), [PowerShell](/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](/azure/role-based-access-control/role-assignments-cli) et un [modèle ARM](/azure/role-based-access-control/role-assignments-template). Vous pouvez en savoir plus sur le [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](/azure/role-based-access-control/overview) et les [différents rôles](/azure/role-based-access-control/rbac-and-directory-admin-roles). 


# <a name="c"></a>[C#](#tab/csharp)

### <a name="attach-a-database-using-c"></a>Attacher une base de données avec C#

#### <a name="needed-nugets"></a>Packages NuGet nécessaires

* Installez [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installez [Microsoft.Rest.ClientRuntime.Azure.Authentication à des fins d’authentification](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="example"></a>Exemple

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

# <a name="python"></a>[Python](#tab/python)

### <a name="attach-a-database-using-python"></a>Attacher une base de données en utilisant Python

#### <a name="needed-modules"></a>Modules nécessaires

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="example"></a>Exemple

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="attach-a-database-using-powershell"></a>Attacher une base de données en utilisant PowerShell

#### <a name="needed-modules"></a>Modules nécessaires

```
Install : Az.Kusto
```

#### <a name="example"></a>Exemple

```Powershell
$FollowerClustername = 'follower'
$FollowerClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$FollowerResourceGroupName = 'followerResouceGroup'
$DatabaseName = "db"  ## Can be specific database name or * for all databases
$LeaderClustername = 'leader'
$LeaderClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$LeaderClusterResourceGroup = 'leaderResouceGroup'
$DefaultPrincipalsModificationKind = 'Union'
##Construct the LeaderClusterResourceId and Location
$getleadercluster = Get-AzKustoCluster -Name $LeaderClustername -ResourceGroupName $LeaderClusterResourceGroup -SubscriptionId $LeaderClusterSubscriptionID -ErrorAction Stop
$LeaderClusterResourceid = $getleadercluster.Id
$Location = $getleadercluster.Location
##Handle the config name if all databases needs to be followed
if($DatabaseName -eq '*')  {
        $configname = $FollowerClustername + 'config'
       } 
else {
        $configname = $DatabaseName   
     }
New-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername `
    -Name $configname `
    -ResourceGroupName $FollowerResourceGroupName `
    -SubscriptionId $FollowerClusterSubscriptionID `
    -DatabaseName $DatabaseName `
    -ClusterResourceId $LeaderClusterResourceid `
    -DefaultPrincipalsModificationKind $DefaultPrincipalsModificationKind `
    -Location $Location `
    -ErrorAction Stop 
```

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Joindre une base de données à l’aide d’un modèle Azure Resource Manager

Dans cette section, vous allez apprendre à attacher une base de données à un cluster existant à l’aide d’un [modèle de Azure Resource Manager](/azure/azure-resource-manager/management/overview). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2020-02-15",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Déployer le modèle 

Vous pouvez déployer le modèle Azure Resource Manager [à l’aide du Portail Azure](https://portal.azure.com) ou à l’aide de PowerShell.

   ![déploiement de modèle](media/follower/template-deployment.png)

|**Paramètre**  |**Description**  |
|---------|---------|
|Nom du cluster follower     |  Nom du cluster follower où le modèle sera déployé.  |
|Nom de configurations de base de données attachée    |    Le nom de l’objet de configurations de base de données attachée. Le nom peut être toute chaîne unique au niveau du cluster.     |
|Nom de la base de données     |      Le nom de la base de données à suivre. Si vous souhaitez suivre toutes les bases de données leader, utilisez « * ».   |
|ID de ressource du cluster leader    |   L’ID de la ressource du cluster leader.      |
|Type de modification des principaux par défaut    |   Le type de modification du principal par défaut. Peut être `Union`, `Replace` ou `None`. Pour plus d’informations sur le type de modification du principal par défaut, consultez [Commande de contrôle du type de modification du principal](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind).      |
|Emplacement   |   L’emplacement de toutes les ressources. Le leader et le follower doivent se trouver au même emplacement.       |

---

## <a name="verify-that-the-database-was-successfully-attached"></a>Vérifiez que la base de données a bien été attachée.

Pour vérifier que la base de données a été attachée avec succès, recherchez vos bases de données attachées dans le [Portail Azure](https://portal.azure.com). Vous pouvez vérifier que les bases de données ont été correctement attachées dans les clusters [« follower »](#check-your-follower-cluster) ou [« leader »](#check-your-leader-cluster).

### <a name="check-your-follower-cluster"></a>Vérifier votre cluster follower  

1. Accédez au cluster follower et sélectionnez **Bases de données**
1. Recherchez les nouvelles bases de données en lecture seule dans la liste des bases de données.

    ![Base de données follower en lecture seule](media/follower/read-only-follower-database.png)

### <a name="check-your-leader-cluster"></a>Vérifier votre cluster leader

1. Accédez au cluster leader et sélectionnez **Bases de données**
2. Vérifiez que les bases de données concernées sont marquées comme **PARTAGÉES AVEC D’AUTRES** > **Oui**

    ![Lire et écrire des bases de données attachées](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database"></a>Détacher la base de données follower  

> [!NOTE]
> Pour détacher une base de données du côté follower ou leader, vous devez disposer d’un utilisateur, d’un groupe, d’un principal du service ou d’une identité managée avec au moins le rôle de contributeur sur le cluster duquel vous détachez la base de données. Dans l’exemple ci-dessous, nous utilisons le principal du service.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="detach-the-attached-follower-database-from-the-follower-cluster-using-c"></a>Détacher la base de données follower attachée du cluster follower en utilisant C#


Le cluster follower peut détacher n’importe quelle base de données attachée comme suit :

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster-using-c"></a>Détacher la base de données follower attachée du cluster leader en utilisant C#

Le cluster leader peut détacher toute base de données attachée comme suit :

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

# <a name="python"></a>[Python](#tab/python)

### <a name="detach-the-attached-follower-database-from-the-follower-cluster-using-python"></a>Détacher la base de données follower attachée du cluster follower en utilisant Python

Le cluster follower peut détacher toute base de données attachée de la manière suivante :

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster-using-python"></a>Détacher la base de données follower attachée du cluster leader en utilisant Python

Le cluster leader peut détacher toute base de données attachée comme suit :

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="detach-a-database-using-powershell"></a>Détacher une base de données en utilisant PowerShell

#### <a name="needed-modules"></a>Modules nécessaires

```
Install : Az.Kusto
```

#### <a name="example"></a>Exemple

```Powershell
$FollowerClustername = 'follower'
$FollowerClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$FollowerResourceGroupName = 'followerResouceGroup'
$DatabaseName = "sanjn"  ## Can be specific database name or * for all databases

##Construct the Configuration name 
$confignameraw = (Get-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername -ResourceGroupName $FollowerResourceGroupName -SubscriptionId $FollowerClusterSubscriptionID) | Where-Object {$_.DatabaseName -eq $DatabaseName }
$configname =$confignameraw.Name.Split("/")[1]

Remove-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername -Name $configname -ResourceGroupName $FollowerResourceGroupName
```

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

[Détachez la base de données follower](#detach-the-follower-database) en utilisant C#, Python ou PowerShell.

---

## <a name="manage-principals-permissions-and-caching-policy"></a>Gérer les principaux, les autorisations et la stratégie de mise en cache

### <a name="manage-principals"></a>Gérer les principaux

Lors de l’attachement d’une base de données, spécifiez le **« type de modification des principaux par défaut »** . Le type par défaut conserve la collection de bases de données leader des [principaux autorisés](kusto/management/access-control/index.md#authorization)

|**Type** |**Description**  |
|---------|---------|
|**Union**     |   Les principaux de la base de données attachée incluent toujours les principaux de la base de données d’origine, ainsi que les nouveaux principaux ajoutés à la base de données follower.      |
|**Replace**   |    Aucun héritage des principaux de la base de données d’origine. De nouveaux principaux doivent être créés pour la base de données attachée.     |
|**Aucun**   |   Les principaux de la base de données attachée incluent uniquement les principaux de la base de données d’origine sans principaux supplémentaires.      |

Pour plus d’informations sur l’utilisation des commandes de contrôle pour configurer les principaux autorisés, consultez [Commandes de contrôle pour la gestion du cluster follower](kusto/management/cluster-follower.md).

### <a name="manage-permissions"></a>Gérer les autorisations

La gestion de l’autorisation de base de données en lecture seule est identique à celle de tous les types de bases de données. Consultez [Gérer les autorisations dans le Portail Azure](manage-database-permissions.md#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Configurer la stratégie de mise en cache

L’administrateur de base de données follower peut modifier la [stratégie de mise en cache](kusto/management/cache-policy.md) de la base de données attachée ou de l’une de ses tables sur le cluster hôte. Le type par défaut conserve la collection de bases de données leader et les stratégies de mise en cache au niveau de la table. Vous pouvez, par exemple, disposer d’une stratégie de mise en cache de 30 jours sur la base de données leader pour exécuter des rapports mensuels et d’une stratégie de mise en cache de trois jours sur la base de données follower pour interroger uniquement les données récentes pour la résolution des problèmes. Pour plus d’informations sur l’utilisation des commandes de contrôle pour configurer la stratégie de mise en cache sur la table ou la base de données follower, consultez [Commandes de contrôle pour la gestion du cluster follower](kusto/management/cluster-follower.md).

## <a name="limitations"></a>Limites

* Les clusters follower et leader doivent se trouver dans la région.
* [L’ingestion de streaming](ingest-data-streaming.md) ne peut pas être utilisée sur une base de données suivie.
* Le chiffrement des données à l’aide de [clés gérées par le client](security.md#customer-managed-keys-with-azure-key-vault) n’est pas pris en charge sur les clusters principaux et les clusters suivants. 
* Vous ne pouvez pas supprimer une base de données attachée à un autre cluster avant de la détacher.
* Vous ne pouvez pas supprimer un cluster qui dispose d’une base de données attachée à un autre cluster avant de la détacher.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la configuration du cluster follower, consultez [Commandes de contrôle pour la gestion d’un cluster follower](kusto/management/cluster-follower.md).


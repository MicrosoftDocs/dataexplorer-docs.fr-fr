---
title: Ajouter des principaux de cluster pour Azure Data Explorer à l’aide de Python
description: Dans cet article, vous apprendrez à ajouter des principaux de cluster pour Azure Data Explorer à l’aide de Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 73956a73f5ae84529018abfce64c3a373b9a8ca1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492817"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Ajouter des principaux de cluster pour Azure Data Explorer à l’aide de Python

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Modèle Azure Resource Manager](cluster-principal-resource-manager.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Dans cet article, vous ajouterez des principaux de cluster pour Azure Data Explorer à l’aide de Python.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Créez un cluster](create-cluster-database-python.md).

## <a name="install-python-package"></a>Installer le package Python

Pour installer le package Python pour Azure Data Explorer (Kusto), ouvrez une invite de commandes dont le chemin contient Python. Exécutez cette commande :

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Ajouter un principal de cluster

L’exemple suivant montre comment ajouter par programmation un principal de cluster.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
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
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| client_secret | *xxxxxxxxxxxxxx* | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire. |
| resource_group_name | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| nom_cluster | *mykustocluster* | Nom de votre cluster.|
| principal_assignment_name | *clusterPrincipalAssignment1* | Nom de votre principal de cluster.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID du principal, qui peut être l’e-mail de l’utilisateur, l’ID de l’application ou le nom du groupe de sécurité.|
| rôle | *AllDatabasesAdmin* | Rôle de votre principal de cluster, qui peut être « AllDatabasesAdmin » ou « AllDatabasesViewer ».|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID de locataire du principal.|
| principal_type | *Application* | Type du principal, qui peut être « User » (Utilisateur), « App » (Application) ou « Group » (Groupe).|

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des principaux de base de données](database-principal-python.md)

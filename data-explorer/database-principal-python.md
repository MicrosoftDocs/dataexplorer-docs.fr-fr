---
title: Ajouter des principaux de base de données pour Azure Data Explorer à l’aide de Python
description: Dans cet article, vous allez apprendre à ajouter des principaux de base de données pour Azure Data Explorer à l’aide de Python.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 54b2d05c666c1ac3ed35520ad383f01eff3371cb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350007"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Ajouter des principaux de base de données pour Azure Data Explorer à l’aide de Python

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Modèle Azure Resource Manager](database-principal-resource-manager.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Dans cet article, vous ajouterez des principaux de base de données pour Azure Data Explorer à l’aide de Python.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Créer un cluster et une base de données](create-cluster-database-python.md)

## <a name="install-python-package"></a>Installer le package Python

Pour installer le package Python pour Azure Data Explorer (Kusto), ouvrez une invite de commandes dont le chemin contient Python. Exécutez cette commande :

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Ajouter un principal de base de données

L’exemple suivant montre comment ajouter par programmation un principal de base de données.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| client_secret | *xxxxxxxxxxxxxx* | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire. |
| resource_group_name | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| nom_cluster | *mykustocluster* | Nom de votre cluster.|
| database_name | *mykustodatabase* | Nom de votre base de données.|
| principal_assignment_name | *databasePrincipalAssignment1* | Nom de votre principal de base de données.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID du principal, qui peut être l’e-mail de l’utilisateur, l’ID de l’application ou le nom du groupe de sécurité.|
| rôle | *Administrateur* | Rôle de votre principal de base de données, qui peut être « Admin » (Administrateur), « Ingestor » (Ingesteur), « Monitor » (Superviseur), « User » (Utilisateur), « UnrestrictedViewers » (Utilisateurs en lecture sans restrictions) ou « Viewer » (Utilisateur en lecture seule).|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID de locataire du principal.|
| principal_type | *Application* | Type du principal, qui peut être « User » (Utilisateur), « App » (Application) ou « Group » (Groupe).|

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des données à l'aide de la bibliothèque Python d'Azure Data Explorer](python-ingest-data.md)

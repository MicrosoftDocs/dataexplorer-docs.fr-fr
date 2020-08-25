---
title: Se connecter à Azure Data Explorer à partir d’Azure Databricks
description: Cette rubrique vous montre comment utiliser Azure Databricks pour accéder aux données d’Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 08ac06b5f0a1a65afec6a71106943f3b58c1b9f5
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610302"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks"></a>Se connecter à Azure Data Explorer à partir d’Azure Databricks

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) est une plateforme d’analytique basée sur Apache Spark, qui est optimisée pour la plateforme Microsoft Azure. Cet article vous montre comment utiliser Azure Databricks pour accéder aux données d’Azure Data Explorer. Il existe plusieurs façons de s’authentifier auprès d’Azure Data Explorer, notamment une connexion d’appareil et une application Azure Active Directory (Azure AD).
 
## <a name="prerequisites"></a>Prérequis

- [Créez un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).
- [Créez un espace de travail Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Sous **Service Azure Databricks**, dans la liste déroulante **Niveau tarifaire**, sélectionnez **Premium**. Ceci vous permet d’utiliser des secrets Azure Databricks pour stocker vos informations d’identification et les référencer dans des notebooks et des travaux.

- [Créez un cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) dans Azure Databricks avec les paramètres par défaut.

 ## <a name="install-the-kusto-spark-connector-on-your-azure-databricks-cluster"></a>Installer le connecteur Kusto Spark sur votre cluster Azure Databricks

Pour installer [spark-kusto-connector](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector) sur votre cluster Azure Databricks :

1. Accédez à votre espace de travail Azure Databricks et [créez une bibliothèque](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
1. Recherchez le package *spark-kusto-connector* sur Maven central, installez la dernière version et attachez le package à votre cluster. 

## <a name="connect-to-azure-data-explorer-by-using-a-device-authentication"></a>Se connecter à Azure Data Explorer avec une authentification d’appareil

[Exemple de code](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py).

## <a name="connect-to-azure-data-explorer-by-using-an-azure-ad-app"></a>Se connecter à Azure Data Explorer avec une application Azure AD

1. Créez une application Azure AD en [provisionnant une application Azure AD](kusto/management/access-control/how-to-provision-aad-app.md).
1. Accordez l’accès à votre application Azure AD dans votre base de données Azure Data Explorer comme suit :

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```

    | Paramètre | Description |
    | - | - |
    | `DB Name` | nom de votre base de données |
    | `AAD App ID` | ID de votre application Azure AD |
    | `AAD Tenant ID` | ID de votre locataire Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Rechercher votre ID de locataire Azure AD

Pour authentifier une application, Azure Data Explorer utilise votre ID de locataire Azure AD. Pour trouver votre ID de locataire, utilisez l’URL suivante. Remplacez *YourDomain* par votre domaine.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Par exemple, si votre domaine est *contoso.com*, l’URL est : [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Sélectionnez cette URL pour voir les résultats. La première ligne se présente comme suit : 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Votre ID d’abonné est `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key-optional"></a>Stocker et sécuriser l’ID et la clé de votre application Azure AD (facultatif)  

Stockez et sécurisez la clé et l’ID de votre application Azure AD en utilisant des [secrets](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) Azure Databricks comme suit :

1. [Configurez l’interface CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installez l’interface CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Configurez l’authentification](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configurez les [secrets](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) à l’aide des exemples de commandes suivants :

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="sample-code"></a>Exemple de code

1. [Exemple de code](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py). 
1. Remplacez les valeurs des espaces réservés par le nom de votre cluster, le nom de votre base de données, le nom de votre table, l’ID de votre locataire Azure AD, l’ID de votre application AAD et la clé de votre application AAD. Si vous stockez vos informations d’identification dans le magasin de secrets Databricks, mettez à jour le code en conséquence pour récupérer les valeurs de dbutils.

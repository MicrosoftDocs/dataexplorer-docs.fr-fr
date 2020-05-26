---
title: Se connecter à Azure Data Explorer à partir d’Azure Databricks en utilisant Python
description: Cette rubrique vous montre comment utiliser une bibliothèque Python dans Azure Databricks pour accéder aux données d’Azure Data Explorer en utilisant une des deux méthodes d’authentification.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 1853b85215da63a753a7867cb9d4354c5b99731a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373961"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Se connecter à Azure Data Explorer à partir d’Azure Databricks en utilisant Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) est une plateforme d’analytique basée sur Apache Spark, qui est optimisée pour la plateforme Microsoft Azure. Cet article vous montre comment utiliser une bibliothèque Python dans Azure Databricks pour accéder aux données d’Azure Data Explorer. Il existe plusieurs façons de s’authentifier auprès d’Azure Data Explorer, notamment une connexion d’appareil et une application Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Conditions préalables requises

- [Créez un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).
- [Créez un espace de travail Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Sous **Service Azure Databricks**, dans la liste déroulante **Niveau tarifaire**, sélectionnez **Premium**. Ceci vous permet d’utiliser des secrets Azure Databricks pour stocker vos informations d’identification et les référencer dans des notebooks et des travaux.

- [Créez un cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) dans Azure Databricks avec les spécifications suivantes (paramètres minimum nécessaires pour exécuter les exemples de notebooks) :

   ![Spécifications pour la création d’un cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Installer la bibliothèque Python sur votre cluster Azure Databricks

Pour installer la [bibliothèque Python](kusto/api/python/kusto-python-client-library.md) sur votre cluster Azure Databricks :

1. Accédez à votre espace de travail Azure Databricks et [créez une bibliothèque](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Chargez un package Python PyPI ou Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Chargez, installez et attachez la bibliothèque à votre cluster Databricks.
   - Entrez le nom PyPi : **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Se connecter à Azure Data Explorer avec une connexion d’appareil

[Importez un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) en utilisant le notebook [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb). Vous pouvez ensuite vous connecter à Azure Data Explorer en utilisant vos informations d’identification.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Se connecter à Azure Data Explorer en utilisant une application Azure AD

1. Créez une application Azure AD en [provisionnant une application Azure AD](kusto/management/access-control/how-to-provision-aad-app.md).
1. Accordez l’accès à votre application Azure AD dans votre base de données Azure Data Explorer comme suit :

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | nom de votre base de données |
    | ```AAD App ID``` | ID de votre application Azure AD |
    | ```AAD Tenant ID``` | ID de votre locataire Azure AD |

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

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Stocker et sécuriser l’ID et la clé de votre application Azure AD 

Stockez et sécurisez la clé et l’ID de votre application Azure AD en utilisant des [secrets](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) Azure Databricks comme suit :
1. [Configurez l’interface CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installez l’interface CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Configurez l’authentification](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configurez les [secrets](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) à l’aide des exemples de commandes suivants :

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importer un notebook
[Importez un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) en utilisant le notebook [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) pour vous connecter à Azure Data Explorer. Remplacez les valeurs des espaces réservés par le nom de votre cluster, le nom de votre base de données et l’ID de votre locataire Azure AD.

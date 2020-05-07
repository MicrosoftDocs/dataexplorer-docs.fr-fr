---
title: Créer un cluster et une base de données Azure Data Explorer avec Azure CLI
description: Découvrir comment créer un cluster et une base de données Azure Data Explorer à l’aide de l’interface de ligne de commande Azure
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: cd503948d2f48a0ca431b7e1ce9fbe5c178fc542
ms.sourcegitcommit: 72eaa9e5169d79507ceb6ead4a2eb703121c2190
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774967"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Créer un cluster et une base de données Azure Data Explorer avec Azure CLI

> [!div class="op_single_selector"]
> * [Portail](create-cluster-database-portal.md)
> * [INTERFACE DE LIGNE DE COMMANDE](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modèle ARM](create-cluster-database-resource-manager.md)

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. Dans cet article, vous créez un cluster et une base de données en utilisant Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour suivre cet article, vous devez disposer d’un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser localement Azure CLI, cet article nécessite Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour vérifier votre version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Configurer les paramètres CLI

Les étapes suivantes ne sont pas obligatoires si vous exécutez des commandes dans Azure Cloud Shell. Si vous utilisez l’interface de ligne de commande en local, suivez ces étapes pour vous connecter à Azure et définir votre abonnement actuel :

1. Exécutez la commande ci-après pour vous connecter à Azure :

    ```azurecli-interactive
    az login
    ```

1. Définissez l’abonnement dans lequel vous voulez créer le cluster. Remplacez `MyAzureSub` par le nom de l’abonnement Azure que vous voulez utiliser :

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Créer le cluster Azure Data Explorer

1. Créez votre cluster avec la commande suivante :

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku name="Standard_D13_v2" tier="Standard" --resource-group testrg --location westus
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | name | *azureclitest* | Nom souhaité de votre cluster.|
   | sku | *Standard_D13_v2* | Référence SKU utilisée pour votre cluster. Paramètres : *name* - Nom de la référence SKU. *tier* - Niveau de la référence SKU. |
   | resource-group | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |
   | location | *westus* | Emplacement où sera créé le cluster. |

    Vous pouvez définir d’autres paramètres facultatifs, comme la capacité du cluster.

1. Exécutez la commande suivante pour vérifier si votre cluster a bien été créé :

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Si le résultat contient `provisioningState` avec la valeur `Succeeded`, alors le cluster a correctement été créé.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Créer la base de données dans le cluster Azure Data Explorer

1. Créez votre base de données avec la commande suivante :

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --database-name clidatabase --resource-group testrg --read-write-database soft-delete-period=P365D hot-cache-period=P31D location=westus
    ```

   |**Paramètre** | **Valeur suggérée** | **Description du champ**|
   |---|---|---|
   | cluster-name | *azureclitest* | Nom du cluster dans lequel la base de données est créée.|
   | database-name | *clidatabase* | Nom de votre base de données.|
   | resource-group | *testrg* | Nom du groupe de ressources dans lequel sera créé le cluster. |
   | read-write-database | *P365D* *P31D* *westus* | Type de base de données. Paramètres : *soft-delete-period* - Représente la durée pendant laquelle les données restent disponibles pour les requêtes. Pour plus d’informations, consultez [Stratégie de conservation](kusto/management/retentionpolicy.md). *hot-cache-period* - Représente la durée pendant laquelle les données sont conservées dans le cache. Pour plus d’informations, consultez [Stratégie de cache](kusto/management/cachepolicy.md). *location* - Emplacement où sera créée la base de données. |

1. Exécutez la commande suivante pour voir la base de données que vous avez créée :

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Vous disposez maintenant d’un cluster et d’une base de données.

## <a name="clean-up-resources"></a>Nettoyer les ressources

* Si vous envisagez de suivre nos autres articles, conservez les ressources que vous avez créées.
* Pour nettoyer les ressources, supprimez le cluster. Lorsque vous supprimez un cluster, cela supprime également toutes les bases de données qu’il contient. Utilisez la commande suivante pour supprimer votre cluster :

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des données à l'aide de la bibliothèque Python d'Azure Data Explorer](python-ingest-data.md)

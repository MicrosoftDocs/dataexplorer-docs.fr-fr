---
title: Utiliser le vidage des données afin de supprimer les données personnelles d’un appareil ou d’un service dans Azure Data Explorer
description: Découvrez comment vider (supprimer) des données dans Azure Data Explorer à l’aide du vidage de données.
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 3f170a48f31f9d842e39fcf42fcfce0c383c71ed
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83232364"
---
# <a name="enable-data-purge-on-your-azure-data-explorer-cluster"></a>Activer le vidage des données dans votre cluster Azure Data Explorer

[!INCLUDE [gdpr-intro-sentence](includes/gdpr-intro-sentence.md)]

Azure Data Explorer permet de supprimer des enregistrements un par un. La suppression des données via la commande `.purge` a pour but de protéger les données personnelles et ne doit pas être utilisée dans d’autres scénarios. Elle n’est pas conçue pour prendre en charge les requêtes de suppression fréquentes et la suppression de quantités importantes de données. Ces opérations peuvent donc avoir un impact important sur les performances du service.

L’exécution d’une commande `.purge` déclenche un processus qui peut durer quelques jours. Si la « densité » des enregistrements sur lesquels `predicate` est appliqué est élevée, le processus va réingérer toutes les données de la table. Ce processus a un impact significatif sur les performances et le coût des marchandises vendues. Pour plus d’informations, consultez [Vidage des données dans Azure Data Explorer](kusto/concepts/data-purge.md).

## <a name="methods-of-invoking-purge-operations"></a>Méthodes pour appeler les opérations de vidage 

Azure Data Explorer (Kusto) prend en charge la suppression individuelle des enregistrements, ainsi que le vidage de l’intégralité d’une table. La commande `.purge` peut être [appelée de deux manières](kusto/concepts/data-purge.md#purge-table-tablename-records-command) pour différents scénarios d’utilisation :

* Appel programmatique : étape unique destinée à être appelée par des applications. L’appel de cette commande déclenche directement la séquence d’exécution du vidage.

* Appel humain : processus en deux étapes qui nécessite une confirmation explicite, effectuée dans le cadre d’une étape distincte. L’appel de la commande retourne un jeton de vérification, qui doit être fourni pour exécuter le vidage. Ce processus réduit le risque de supprimer des données par inadvertance. Cette option peut mettre beaucoup de temps à s’exécuter sur les tables volumineuses contenant une grande quantité de données de cache brutes. 

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Connectez-vous à l’[interface utilisateur web](https://dataexplorer.azure.com/).
* Créer [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md)

## <a name="enable-data-purge-on-your-cluster"></a>Activer le vidage des données sur votre cluster

> [!WARNING]
> * L’activation du vidage des données nécessite un redémarrage du service qui peut entraîner l’abandon des requêtes.
> * Passez en revue les [limitations](#limitations) avant d’activer le vidage des données.

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. 
1. Dans **Paramètres**, sélectionnez **Configurations**. 
1. Dans le volet **Configurations**, sélectionnez **Activé** pour activer l’option **Activer le vidage**.
1. Sélectionnez **Enregistrer**.
 
    ![Option Activer le vidage activée](media/data-purge-portal/enable-purge-on.png)

## <a name="disable-data-purge-on-your-cluster"></a>Désactiver le vidage des données sur votre cluster

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. 
1. Dans **Paramètres**, sélectionnez **Configurations**. 
1. Dans le volet **Configurations**, sélectionnez **Désactivé** pour désactiver l’option **Activer le vidage**.
1. Sélectionnez **Enregistrer**.

    ![Option Activer le vidage désactivée](media/data-purge-portal/enable-purge-off.png)

## <a name="limitations"></a>Limites

* Le processus de vidage est définitif et irréversible. Il n’est pas possible d’annuler ce processus ni de récupérer les données ayant fait l’objet d’un vidage. Par conséquent, les commandes telles que [undo table drop](kusto/management/undo-drop-table-command.md) ne permettent pas de récupérer les données vidées, et la restauration des données vers une version antérieure ne peut pas remonter avant le dernier vidage.
* La commande `.purge` est exécutée sur le point de terminaison Gestion des données : *https://ingest- [NomCluster].[Région].kusto.windows.net*. La commande nécessite des autorisations d’[administrateur de base de données](kusto/management/access-control/role-based-authorization.md) pour les bases de données appropriées. 
* En raison de l’impact du processus de vidage sur les performances, l’appelant est censé modifier le schéma de données afin que les tables minimales incluent des données pertinentes, et répartir les commandes par table afin de réduire le fort impact du vidage sur le coût des marchandises vendues.
* Le paramètre `predicate` de la commande de vidage est utilisé pour spécifier les enregistrements à vider. La taille de `Predicate` est limitée à 63 Ko. 

## <a name="next-steps"></a>Étapes suivantes

* [Vidage des données dans Azure Data Explorer](kusto/concepts/data-purge.md)

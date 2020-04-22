---
title: Gérer les extensions de langage dans votre cluster Azure Data Explorer
description: Utilisez l’extension de langage pour intégrer d’autres langages de programmation dans vos requêtes KQL Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 52855651daccfe3c9baf5bb059530bcf7bfb0f19
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81494545"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Gérer les extensions de langage dans votre cluster Azure Data Explorer (préversion)

La fonctionnalité Extensions de langage vous permet d’utiliser des plug-ins d’extension de langage pour intégrer d’autres langages de programmation dans vos requêtes KQL Azure Data Explorer. Lorsque vous exécutez une fonction définie par l’utilisateur (UDF) à l’aide d’un script pertinent, le script obtient les données tabulaires en tant qu’entrée et est censé produire une sortie tabulaire. Le runtime du plug-in est hébergé dans un [bac à sable](kusto/concepts/sandboxes.md), un environnement isolé et sécurisé qui s’exécute sur les nœuds du cluster. Dans cet article, vous gérez le plug-in Extensions de langage dans votre cluster Azure Data Explorer au sein du Portail Azure.

> [!NOTE]
> Les extensions de langage Azure Data Explorer actuellement prises en charge sont Python et R.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Activer les extensions de langage sur votre cluster

> [!WARNING]
> Examinez les [limitations](#limitations) avant d’activer une extension de langage.

Procédez comme suit pour activer les extensions de langage sur votre cluster :

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. 
1. Dans **Paramètres**, sélectionnez **Configurations**. 
1. Dans le volet **Configurations**, sélectionnez **Activé** pour activer une extension de langage.
1. Sélectionnez **Enregistrer**.
 
    ![Extension pour langage activée](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> L’activation du processus d’extension de langage peut prendre quelques minutes. Pendant ce temps, l’exécution de votre cluster cesse temporairement.
 
## <a name="run-language-extension-integrated-queries"></a>Exécuter des requêtes intégrées à l’extension de langage

* Découvrez comment [exécuter des requêtes KQL intégrées à Python](kusto/query/pythonplugin.md).
* Découvrez comment [exécuter des requêtes KQL intégrées à R](kusto/query/rplugin.md). 

## <a name="disable-language-extensions-on-your-cluster"></a>Désactiver les extensions de langage sur votre cluster

> [!NOTE]
> La désactivation des extensions de langage peut prendre quelques minutes.

Procédez comme suit pour désactiver les extensions de langage sur votre cluster :

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. 
1. Dans **Paramètres**, sélectionnez **Configurations**. 
1. Dans le volet **Configurations**, sélectionnez **Désactivé** pour désactiver une extension de langage.
1. Sélectionnez **Enregistrer**.

    ![Extension pour langage désactivée](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Limites

* La fonctionnalité Extensions de langage ne prend pas en charge le [chiffrement de disque](manage-cluster-security.md). 
* Le bac à sable du runtime des extensions de langage alloue de l’espace disque même si aucune requête n’est exécutée dans l’étendue du langage concerné.
Pour plus d’informations sur les limitations, consultez les [bacs à sable](kusto/concepts/sandboxes.md).

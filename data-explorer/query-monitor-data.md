---
title: Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)
description: Dans cette rubrique, vous interrogez les données d’Azure Monitor en créant un proxy Azure Data Explorer pour les requêtes de produit croisé avec Application Insights et Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 39865b049512252e08dac9c182a6b1e20a388abe
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875002"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)

Le cluster proxy Azure Data Explorer (ADX Proxy) est une entité qui vous permet d’effectuer des requêtes entre Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) et [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) dans le service [Azure Monitor](/azure/azure-monitor/). Vous pouvez mapper les espaces de travail d’Azure Monitor Log Analytics ou les applications d’Application Insights comme clusters de proxy. Vous pouvez ensuite interroger le cluster proxy à l’aide des outils Azure Data Explorer et vous y référer dans une requête entre clusters. L’article montre comment se connecter à un cluster proxy, ajouter un cluster proxy à l’interface utilisateur web d’Azure Data Explorer, et exécuter des requêtes contre vos applications AI ou vos espaces de travail LA depuis Azure Data Explorer.

Le flux de proxy Azure Data Explorer : 

![Flux de proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Prérequis

> [!NOTE]
> Le proxy ADX est en mode aperçu. [Connectez-vous au proxy](#connect-to-the-proxy) pour activer la fonctionnalité de proxy ADX pour vos clusters. Pour toute question, contactez l’équipe [ADXProxy](mailto:adxproxy@microsoft.com).

## <a name="connect-to-the-proxy"></a>Se connecter au proxy

1. Vérifiez que votre cluster natif Azure Data Explorer (comme le cluster *d’aide*) apparaît dans le menu de gauche avant de vous connecter à votre cluster Log Analytics ou Application Insights.

    ![Cluster natif ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Dans l’interface utilisateur d’Azure Data Explorer https://dataexplorer.azure.com/clusters), sélectionnez **Ajouter un cluster**.

1. Dans la fenêtre **Ajouter un cluster**, ajoutez l’URL au cluster LA ou AI. 
    
    * Pour LA : `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Pour AI : `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Sélectionnez **Ajouter**.

    ![Ajouter un cluster](media/adx-proxy/add-cluster.png)

    >[!NOTE]
    >Si vous ajoutez une connexion à plusieurs clusters proxy, attribuez un nom différent à chacun d’eux. Sinon ils auront tous le même nom dans le volet gauche.

1. Une fois la connexion établie, votre cluster LA ou AI apparaît dans le volet gauche avec votre cluster natif ADX. 

    ![Clusters Log Analytics et Azure Data Explorer](media/adx-proxy/la-adx-clusters.png)

> [!NOTE]
> Le nombre d’espaces de travail Azure Monitor pouvant être mappés est limité à 100.

## <a name="run-queries"></a>Exécuter des requêtes

Vous pouvez exécuter les requêtes à l’aide des outils clients qui prennent en charge les requêtes Kusto, par exemple : Kusto Explorer, interface utilisateur ADX Web, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, API REST.

> [!TIP]
> * Le nom de la base de données doit avoir le même nom que la ressource spécifiée dans le cluster proxy. Les noms respectent la casse.
> * Dans les requêtes entre clusters, assurez-vous que les noms des applications Application Insights et des espaces de travail Log Analytics sont corrects.
> * Si les noms contiennent des caractères spéciaux, ils sont remplacés par l’encodage d’URL dans le nom du cluster proxy. 
> * Si les noms contiennent des caractères qui ne respectent pas les [règles de nom d’identificateur KQL](kusto/query/schema-entities/entity-names.md), ils sont remplacés par le caractère tiret **-** .

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Requête directe à partir de votre cluster proxy ADX LA ou AI

Exécutez des requêtes sur votre cluster LA ou AI. Vérifiez que votre cluster est sélectionné dans le volet gauche. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Requête de l’espace de travail LA](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Requête croisée de votre cluster proxy ADX LA ou AI et du cluster ADX natif 

Lorsque vous lancez des requêtes croisées à partir du proxy, vérifiez que votre cluster natif ADX est sélectionné dans le volet de gauche. Les exemples suivants illustrent la combinaison de tables de cluster ADX (à l’aide de `union`) avec l’espace de travail LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Requête croisée à partir du proxy Azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

L’utilisation de l’[`join`opérateur](kusto/query/joinoperator.md), au lieu de l’union, peut nécessiter un [`hint`](kusto/query/joinoperator.md#join-hints) pour l’exécuter sur un cluster natif Azure Data Explorer (et non sur le proxy). 

## <a name="function-supportability"></a>Prise en charge des fonctions

Le cluster de proxy Azure Data Explorer prend en charge les fonctions d’Application Insights et de Log Analytics.
Cela permet aux requêtes interclusters de référencer directement une fonction tabulaire Azure Monitor.
Les commandes suivantes sont prises en charge par le proxy :

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

L’image suivante illustre un exemple d’interrogation d’une fonction tabulaire à partir de l’interface utilisateur web d’Azure Data Explorer. Pour utiliser la fonction, exécutez son nom dans la fenêtre de requête.

  [ ![Interroger une fonction tabulaire à partir de l’interface utilisateur web Azure Data Explorer](media/adx-proxy/function-query-adx-proxy.png)](media/adx-proxy/function-query-adx-proxy.png#lightbox)


## <a name="additional-syntax-examples"></a>Exemples de syntaxe supplémentaire

Les options de syntaxe suivantes sont disponibles lors de l’appel des clusters Application Insights (IA) ou Log Analytics (LA) :

|Description de la syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de données dans un cluster qui contient uniquement la ressource définie dans cet abonnement (**recommandé pour les requêtes entre clusters**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster contenant l’ensemble des applications/espaces de travail de cet abonnement    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster qui contient l’ensemble des applications/espaces de travail de l’abonnement et qui sont membres de ce groupe de ressources    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster qui contient uniquement la ressource définie dans cet abonnement      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes](write-queries.md)

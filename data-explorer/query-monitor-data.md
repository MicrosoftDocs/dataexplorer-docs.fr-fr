---
title: Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)
description: Dans cette rubrique, vous allez interroger des données dans Azure Monitor (Application Insights et Log Analytics) en créant des requêtes interproduits Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 607bcc7b1c0401116a94a9c06ff308a06ad936b7
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371529"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)

Azure Data Explorer prend en charge les requêtes interservices entre Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) et [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs). Vous pouvez ensuite interroger votre espace de travail Log Analytics ou Application Insights à l’aide des outils de requête Azure Data Explorer et d’une requête interservice. L’article explique comment créer une requête interservice et comment ajouter l’espace de travail Log Analytics ou Application Insights à l’interface utilisateur web d’Azure Data Explorer.

Le flux de requêtes interservices Azure Data Explorer :

![Flux de proxy Azure Data Explorer](media/query-monitor-data/query-monitor-workflow.png)

> [!NOTE]
> * La fonctionnalité permettant d’interroger les données Azure Monitor à partir d’Azure Data Explorer, que ce soit directement à partir des outils clients Azure Data Explorer ou indirectement en exécutant une requête sur un cluster Azure Data Explorer, est en préversion.
>* Pour obtenir de l’aide, contactez l’[équipe chargée des requêtes interservices](mailto:adxproxy@microsoft.com).


## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Ajouter un espace de travail Log Analytics/Application Insights aux outils clients Azure Data Explorer

Ajoutez un espace de travail Log Analytics ou Application Insights aux outils clients Azure Data Explorer pour permettre l’exécution de requêtes interservices sur vos clusters.

1. Vérifiez que votre cluster natif Azure Data Explorer (comme le cluster *d’aide*) apparaît dans le menu de gauche avant de vous connecter à votre cluster Log Analytics ou Application Insights.

    ![Cluster natif Azure Data Explorer](media/query-monitor-data/web-ui-help-cluster.png)

1. Dans l’interface utilisateur d’Azure Data Explorer https://dataexplorer.azure.com/clusters), sélectionnez **Ajouter un cluster**.

1. Dans la fenêtre **Ajouter un cluster**, ajoutez l’URL au cluster LA ou AI.

    * Pour LA : `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Pour AI : `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

1. Sélectionnez **Ajouter**.

    ![Ajouter un cluster](media/query-monitor-data/add-cluster.png)

    >[!TIP]
    >Si vous ajoutez une connexion à plusieurs espaces de travail Log Analytics ou Application Insights, donnez un nom différent à chacun d’eux. Sinon ils auront tous le même nom dans le volet gauche.

1. Une fois la connexion établie, votre espace de travail Log Analytics ou Application Insights apparaît dans le volet gauche avec votre cluster Azure Data Explorer natif.

    ![Clusters Log Analytics et Azure Data Explorer](media/query-monitor-data/la-adx-clusters.png)

> [!NOTE]
> Le nombre d’espaces de travail Azure Monitor pouvant être mappés est limité à 100.

## <a name="run-queries"></a>Exécuter des requêtes

Vous pouvez exécuter les requêtes à l’aide des outils clients qui prennent en charge les requêtes Kusto, par exemple : Kusto Explorer, l'interface utilisateur web d'Azure Data Explorer, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens, API REST.

> [!NOTE]
> La fonctionnalité de requête interservice est uniquement utilisée pour l’extraction de données. Pour plus d’informations, consultez [Prise en charge des fonctions](#function-supportability).

> [!TIP]
> * La base de données doit porter le même nom que la ressource spécifiée dans la requête interservice. Les noms respectent la casse.
> * Dans les requêtes interservices, vérifiez que les noms des applications Application Insights et des espaces de travail Log Analytics sont corrects.
> * Si les noms contiennent des caractères spéciaux, ils sont remplacés par l’encodage d’URL dans la requête interservice.
> * Si les noms contiennent des caractères qui ne respectent pas les [règles de nom d’identificateur KQL](kusto/query/schema-entities/entity-names.md), ils sont remplacés par le caractère tiret **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Interrogation directe sur vos espaces de travail Log Analytics ou Application Insights à partir des outils clients Azure Data Explorer

Vous pouvez exécuter des requêtes sur vos espaces de travail Log Analytics ou Application Insights à partir des outils clients Azure Data Explorer. 

1. Vérifiez que votre espace de travail est sélectionné dans le volet gauche.

1. Exécutez la requête suivante :

```kusto
Perf | take 10 // Demonstrate cross-service query on the Log Analytics workspace
```

![Interroger l’espace de travail Log Analytics](media/query-monitor-data/query-la.png)

### <a name="cross-query-of-your-log-analytics-or-application-insights-workspace-and-the-azure-data-explorer-native-cluster"></a>Interrogation croisée de votre espace de travail Log Analytics ou Application Insights et du cluster natif Azure Data Explorer

Lorsque vous exécutez des requêtes interservices de cluster, vérifiez que votre cluster natif Azure Data Explorer est sélectionné dans le volet de gauche. Les exemples suivants illustrent la combinaison de tables de cluster Azure Data Explorer (à l’aide de `union`) avec un espace de travail Log Analytics.

Exécutez les requêtes suivantes :

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Requête interservice à partir d’Azure Data Explorer](media/query-monitor-data/cross-query.png)](media/query-monitor-data/cross-query.png#lightbox)

> [!TIP]
> L'utilisation de l'[opérateur `join`](kusto/query/joinoperator.md), au lieu de l'opérateur union, peut nécessiter un [`hint`](kusto/query/joinoperator.md#join-hints) pour l'exécuter sur un cluster natif Azure Data Explorer.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Joindre les données d’un cluster Azure Data Explorer dans un locataire avec une ressource Azure Monitor située dans un autre

Les requêtes interlocataires entre les services ne sont pas prises en charge. Vous êtes connecté à un seul locataire pour exécuter la requête qui couvre les deux ressources.

Si la ressource Azure Data Explorer figure dans le locataire « A » et que l’espace de travail Log Analytics figure dans le locataire « B », utilisez l’une des deux méthodes suivantes :

1. Azure Data Explorer vous permet d’ajouter des rôles pour les principaux dans différents locataires. Ajoutez votre ID d’utilisateur dans le locataire « B » en tant qu’utilisateur autorisé sur le cluster Azure Data Explorer. Validez la propriété *['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster)* sur le cluster Azure Data Explorer qui contient le locataire « B ». Exécutez la requête croisée intégralement dans le locataire « B ».

2. Utilisez [Lighthouse](/azure/lighthouse/) pour projeter la ressource Azure Monitor dans le locataire « A ».

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Se connecter à des clusters Azure Data Explorer à partir de différents locataires

Kusto Explorer vous connecte automatiquement au locataire auquel le compte d’utilisateur appartient à l’origine. Pour accéder aux ressources d’autres locataires avec le même compte d’utilisateur, le `tenantId` doit être spécifié explicitement dans la chaîne de connexion : `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Prise en charge des fonctions

Les requêtes interservices Azure Data Explorer prennent en charge les fonctions d’Application Insights et de Log Analytics.
Cela permet aux requêtes interclusters de référencer directement une fonction tabulaire Azure Monitor.
Les commandes suivantes sont prises en charge avec la requête interservices :

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

L’image suivante illustre un exemple d’interrogation d’une fonction tabulaire à partir de l’interface utilisateur web d’Azure Data Explorer.
Pour utiliser la fonction, exécutez son nom dans la fenêtre de requête.

  [ ![Interroger une fonction tabulaire à partir de l’interface utilisateur web Azure Data Explorer](media/query-monitor-data/function-query.png)](media/query-monitor-data/function-query.png#lightbox)

## <a name="additional-syntax-examples"></a>Exemples de syntaxe supplémentaire

Les options de syntaxe suivantes sont disponibles lors de l’appel des clusters Application Insights ou Log Analytics :

|Description de la syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de données dans un cluster qui contient uniquement la ressource définie dans cet abonnement (**recommandé pour les requêtes entre clusters**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster contenant l’ensemble des applications/espaces de travail de cet abonnement    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster qui contient l’ensemble des applications/espaces de travail de l’abonnement et qui sont membres de ce groupe de ressources    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster qui contient uniquement la ressource définie dans cet abonnement      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes](write-queries.md)
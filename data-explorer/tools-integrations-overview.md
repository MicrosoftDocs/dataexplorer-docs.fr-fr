---
title: Vue d’ensemble des outils et des intégrations Azure Data Explorer - Azure Data Explorer
description: Cet article décrit les outils et les intégrations dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: e341b70dfc2a7c0d3038d6d60d9c8ae2b40b6218
ms.sourcegitcommit: c2ab3176db4dd55ac9ca8eee52bbd24096d1277f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90740250"
---
# <a name="azure-data-explorer-tools-and-integrations-overview"></a>Vue d’ensemble des outils et des intégrations Azure Data Explorer

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour analyser en temps réel de gros volumes de streaming de données provenant des applications, des sites web, des appareils IoT, etc. Azure Data Explorer collecte, stocke et analyse diverses données pour améliorer les produits et l’expérience des clients, ainsi que superviser les appareils et dynamiser les opérations. 

Azure Data Explorer offre différents outils et intégrations pour l’ingestion de données, les requêtes, la visualisation, l’orchestration, etc. En plus de ses services natifs, Azure Data Explorer permet aux utilisateurs de facilement intégrer différents produits et plateformes, de prendre en charge différents cas d’usage de client et d’optimiser les processus métier en simplifiant les workflows et en réduisant les coûts. 

Cet article vous fournit une liste d’outils, de connecteurs et d’intégrations Azure Data Explorer avec des liens vers les documents appropriés pour plus d’informations.

## <a name="ingest-data"></a>Réception de données 

L’ingestion des données est le processus utilisé pour charger des enregistrements de données d’une ou plusieurs sources dans Azure Data Explorer. Une fois ingérées, les données sont disponibles pour les requêtes. Azure Data Explorer fournit plusieurs outils et connecteurs pour l’ingestion des données. 

### <a name="azure-data-explorer-ingestion-tools"></a>Outils d’ingestion Azure Data Explorer

* [LightIngest](lightingest.md)utilitaire d’aide pour l’ingestion des données ad hoc dans Azure Data Explorer
* Ingestion en un clic
    * [Vue d’ensemble de l’ingestion en un clic](ingest-data-one-click.md) 
    * [Ingérer des données d’un conteneur dans une nouvelle table](one-click-ingestion-new-table.md)
    * [Ingérer des données d’un fichier local dans une table existante](one-click-ingestion-existing-table.md)

### <a name="ingestion-integrations"></a>Intégrations d’ingestion

* Event Hub
    * [Ingérer des données à partir d’Event Hub](ingest-data-event-hub-overview.md)
    * Ingérez des données d’Event Hub à l’aide du [portail Azure](ingest-data-event-hub.md), de [C#](data-connection-event-hub-csharp.md), de [Python](data-connection-event-hub-python.md) ou d’un [modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md)
* Event Grid
    * [Ingérer des données d’Event Grid](ingest-data-event-grid-overview.md)
    * Ingérez des données d’Event Grid à l’aide du [portail Azure](ingest-data-event-grid.md), de [C#](data-connection-event-grid-csharp.md), de [Python](data-connection-event-grid-python.md) ou d’un [modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)
* IoT Hub
    * [Ingérer des données d’IoT Hub](ingest-data-iot-hub-overview.md)
    * Ingérez des données d’IoT Hub à l’aide du [portail Azure](ingest-data-iot-hub.md), de [C#](data-connection-iot-hub-csharp.md), de [Python](data-connection-iot-hub-python.md) ou d’un [modèle Azure Resource Manager](data-connection-iot-hub-resource-manager.md)
* [Logstash](ingest-data-logstash.md)
* Azure Data Factory
    * [Intégrer à Azure Data Factory](data-factory-integration.md)
    * [Copie de données](data-factory-load-data.md)
    * [Copier en bloc à partir d’une base de données à l’aide du modèle Azure Data Factory](data-factory-template.md)
    * [Utiliser l’activité de commande Azure Data Factory pour exécuter des commandes de contrôle Azure Data Explorer](data-factory-command-activity.md)
* Apache 
    * [Spark](spark-connector.md)
    * [Kafka](ingest-data-kafka.md)
* [Cosmos DB](https://github.com/Azure/azure-kusto-labs/tree/master/cosmosdb-adx-integration)
* [Power Automate](flow.md)

## <a name="query-data"></a>Interroger des données

### <a name="azure-data-explorer-query-tools"></a>Outils de requête Azure Data Explorer

Plusieurs outils sont disponibles pour exécuter des requêtes dans Azure Data Explorer.

* Kusto.Explorer
    * [Installation et interface utilisateur](kusto/tools/kusto-explorer.md)
    * [Utilisation de Kusto.Explorer](kusto/tools/kusto-explorer-using.md)
    * [options](kusto/tools/kusto-explorer-options.md)
    * Autres rubriques : [résolution des problèmes](kusto/tools/kusto-explorer-troubleshooting.md), [raccourcis clavier](kusto/tools/kusto-explorer-shortcuts.md), [refactorisation de code](kusto/tools/kusto-explorer-refactor.md), [navigation dans le code](kusto/tools/kusto-explorer-codenav.md) et [analyse de code](kusto/tools/kusto-explorer-code-analyzer.md)
* [Interface utilisateur web](web-query-data.md)
* [Kusto.Cli](kusto/tools/kusto-cli.md)

### <a name="query-integrations"></a>Intégrations de requête

* [Azure Monitor](query-monitor-data.md)
* [Azure Data Lake](data-lake-query-data.md)
* [Apache Spark](spark-connector.md)
* Microsoft Power Apps
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic)

## <a name="visualizations-dashboards-and-reporting"></a>Visualisations, tableaux de bord et création de rapports

La [vue d’ensemble de la visualisation](viz-overview.md) détaille la visualisation des données, les tableaux de bord et les options de création de rapports. 

## <a name="notebook-connectivity"></a>Connectivité de notebook

* [Azure Notebooks](azure-notebooks.md)
* [Blocs-notes Jupyter](kqlmagic.md)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic)

## <a name="orchestration"></a>Orchestration

* Power Automate
    * [Connecteur Power Automate](flow.md)
    * [Exemples d’utilisation du connecteur Power Automate](flow-usage.md)
* [Application logique Microsoft](kusto/tools/logicapps.md) 
* [Azure Data Factory](data-factory-integration.md).

## <a name="share-data"></a>Partager des données

* [Azure Data Share](data-share.md)

## <a name="source-control-integration"></a>Intégration du contrôle de code source

* [Azure Pipelines](devops.md) 
* [Synchroniser Kusto](kusto/tools/synckusto.md) 

<!--Open Source Tools-->

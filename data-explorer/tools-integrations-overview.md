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
ms.openlocfilehash: c1be494fd290b051455010d6e6e082d01650107c
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003143"
---
# <a name="azure-data-explorer-tools-and-integrations-overview"></a>Vue d’ensemble des outils et des intégrations Azure Data Explorer

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour analyser en temps réel de gros volumes de streaming de données provenant des applications, des sites web, des appareils IoT, etc. Azure Data Explorer collecte, stocke et analyse diverses données pour améliorer les produits et l’expérience des clients, ainsi que superviser les appareils et dynamiser les opérations. 

Azure Data Explorer offre différents outils et intégrations pour l’ingestion de données, les requêtes, la visualisation, l’orchestration, etc. En plus de ses services natifs, Azure Data Explorer permet aux utilisateurs de facilement intégrer différents produits et plateformes, de prendre en charge différents cas d’usage de client et d’optimiser les processus métier en simplifiant les workflows et en réduisant les coûts. 

Cet article vous fournit une liste d’outils, de connecteurs et d’intégrations Azure Data Explorer avec des liens vers les documents appropriés pour plus d’informations.

## <a name="ingest-data"></a>Réception de données 

L’ingestion des données est le processus utilisé pour charger des enregistrements de données d’une ou plusieurs sources dans Azure Data Explorer. Une fois ingérées, les données sont disponibles pour les requêtes. Azure Data Explorer fournit plusieurs outils et connecteurs pour l’ingestion des données. 

### <a name="azure-data-explorer-ingestion-tools"></a>Outils d’ingestion Azure Data Explorer

* [LightIngest](lightingest.md)utilitaire d’aide pour l’ingestion des données ad hoc dans Azure Data Explorer
* Ingestion en un clic : [vue d’ensemble](ingest-data-one-click.md) et ingérer des données [depuis un conteneur vers une nouvelle table](one-click-ingestion-new-table.md) ou [depuis un fichier local vers une table existante](one-click-ingestion-existing-table.md)

### <a name="ingestion-integrations"></a>Intégrations d’ingestion

* Event Hub : [Vue d’ensemble de l’ingestion depuis Event Hub](ingest-data-event-hub-overview.md) et utilisation du [portail Azure](ingest-data-event-hub.md), de [C#](data-connection-event-hub-csharp.md), de [Python](data-connection-event-hub-python.md) ou d’un [modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md)
* Event Grid : [Vue d’ensemble de l’ingestion depuis Event Grid](ingest-data-event-grid-overview.md) et utilisation du [portail Azure](ingest-data-event-grid.md), de [C#](data-connection-event-grid-csharp.md), de [Python](data-connection-event-grid-python.md) ou d’un [modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)
* IoT Hub : [Vue d’ensemble de l’ingestion depuis IoT Hub](ingest-data-iot-hub-overview.md) et utilisation du [portail Azure](ingest-data-iot-hub.md), de [C#](data-connection-iot-hub-csharp.md), de [Python](data-connection-iot-hub-python.md) ou d’un [modèle Azure Resource Manager](data-connection-iot-hub-resource-manager.md)
* [Logstash](ingest-data-logstash.md)
* Azure Data Factory : [vue d’ensemble de l’intégration](data-factory-integration.md), [copier des données](data-factory-load-data.md), [copier en bloc en utilisant le modèle Azure Data Factory](data-factory-template.md) et [exécuter des commandes de contrôle Azure Data Explorer en utilisant l’activité de commande Azure Data Factory](data-factory-command-activity.md)
* [Azure Synapse - Apache Spark](https://docs.microsoft.com/azure/synapse-analytics/quickstart-connect-azure-data-explorer?context=/azure/data-explorer/context/context)
* [Apache Spark](spark-connector.md)
* [Apache Kafka](ingest-data-kafka.md)
* [Cosmos DB](https://github.com/Azure/azure-kusto-labs/tree/master/cosmosdb-adx-integration)
* [Power Automate](flow.md)

## <a name="query-data"></a>Interroger des données

### <a name="azure-data-explorer-query-tools"></a>Outils de requête Azure Data Explorer

Plusieurs outils sont disponibles pour exécuter des requêtes dans Azure Data Explorer.

* Kusto.Explorer
    * [installation et interface utilisateur](kusto/tools/kusto-explorer.md), [utilisation de Kusto.Explorer](kusto/tools/kusto-explorer-using.md)
    * Autres rubriques : [options](kusto/tools/kusto-explorer-options.md), [résolution des problèmes](kusto/tools/kusto-explorer-troubleshooting.md), [raccourcis clavier](kusto/tools/kusto-explorer-shortcuts.md), [refactorisation du code](kusto/tools/kusto-explorer-refactor.md), [navigation dans le code](kusto/tools/kusto-explorer-codenav.md) et [analyse du code](kusto/tools/kusto-explorer-code-analyzer.md)
* [Interface utilisateur web](web-query-data.md)
* [Kusto.Cli](kusto/tools/kusto-cli.md)

### <a name="query-integrations"></a>Intégrations de requête

* [Azure Monitor](query-monitor-data.md)
* [Azure Data Lake](data-lake-query-data.md)
* [Azure Synapse - Apache Spark](https://docs.microsoft.com/azure/synapse-analytics/quickstart-connect-azure-data-explorer?context=/azure/data-explorer/context/context)
* [Apache Spark](spark-connector.md)
* Microsoft Power Apps
* Azure Data Studio : [Vue d’ensemble de l’extension Kusto](https://docs.microsoft.com/sql/azure-data-studio/extensions/kusto-extension?context=/azure/data-explorer/context/context), [utiliser Kusto](https://docs.microsoft.com/sql/azure-data-studio/notebooks/notebooks-kusto-kernel?context=/azure/data-explorer/context/context) et [utiliser Kqlmagic](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic?context=/azure/data-explorer/context/context)

## <a name="visualizations-dashboards-and-reporting"></a>Visualisations, tableaux de bord et création de rapports

La [vue d’ensemble de la visualisation](viz-overview.md) détaille la visualisation des données, les tableaux de bord et les options de création de rapports. 

## <a name="notebook-connectivity"></a>Connectivité de notebook

* [Azure Notebooks](azure-notebooks.md)
* [Blocs-notes Jupyter](kqlmagic.md)
* Azure Data Studio : [Vue d’ensemble de l’extension Kusto](https://docs.microsoft.com/sql/azure-data-studio/extensions/kusto-extension?context=/azure/data-explorer/context/context), [utiliser Kusto](https://docs.microsoft.com/sql/azure-data-studio/notebooks/notebooks-kusto-kernel?context=/azure/data-explorer/context/context) et [utiliser Kqlmagic](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic?context=/azure/data-explorer/context/context)

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

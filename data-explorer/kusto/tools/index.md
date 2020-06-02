---
title: Outils Azure Data Explorer - Azure Data Explorer | Microsoft Docs
description: Cet article décrit les outils Azure Data Explorer dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: b6bc95158c1dd161a17572342c6a99bdf9d37235
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257907"
---
# <a name="azure-data-explorer-tools"></a>Outils Azure Data Explorer

## <a name="ad-hoc-query-tools"></a>Outils de requête ad hoc

* Kusto.Explorer
   * [Installation et interface utilisateur de Kusto.Explorer](./kusto-explorer.md) - principal outil de bureau pour l’interrogation et le contrôle de Kusto
   * [Utilisation de Kusto.Explorer](./kusto-explorer-using.md)
   * [Résolution des problèmes de Kusto.Explorer](kusto-explorer-troubleshooting.md)
* [Interface utilisateur web](../../web-query-data.md) - interface utilisateur web pour l’interrogation de Kusto

## <a name="visualizations-dashboards-and-reporting-tools"></a>Visualisations, tableaux de bord et outils de création de rapports


* [Azure Notebooks](../../azure-notebooks.md) - utilisez Azure Notebooks pour analyser les données dans Azure Data Explorer.
* Excel
    * [Requête vide Excel](../../excel-blank-query.md) - ajout d’une requête Kusto en tant que source de données Excel
    * [Connecteur Excel](../../excel-connector.md) - connecteur Excel pour Azure Data Explorer 

* PowerBI

   * [Bonnes pratiques pour Power BI](../../power-bi-best-practices.md)
   * [Connecteur Power BI](../../power-bi-connector.md)
   * [Requête importée Power BI](../../power-bi-imported-query.md) 
   * [Requête SQL Power BI](../../power-bi-sql-query.md)

* [Grafana](../../grafana.md)
* [Connecteur open source K2Bridge](../../k2bridge.md) - Visualiser des données à partir d’Azure Data Explorer dans Kibana

## <a name="orchestration-tools"></a>Outils d’orchestration


* Microsoft Flow
    * [Connecteur Microsoft Flow](../../flow.md)
    * [Exemples d’utilisation du connecteur Microsoft Flow](../../flow-usage.md)
* [Microsoft Logic App](./logicapps.md) - exécution automatique de requêtes Kusto dans le cadre de [Microsoft Logic App](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)



## <a name="data-ingestion-tools"></a>Code d’ingestion de données


* [LightIngest](../../lightingest.md)utilitaire d’aide pour l’ingestion des données ad hoc dans Azure Data Explorer
* [Ingestion en un clic](../../ingest-data-one-click.md) - outil permettant d’ingérer rapidement des données et de suggérer automatiquement tables et structures de mappage
* [Azure Data Factory](azure-data-factory.md).


## <a name="source-control-integration-tools"></a>Outils d’intégration du contrôle de code source

* [Azure Pipelines](../../devops.md) - appelle des commandes de contrôle dans le cadre de votre pipeline
* [Sync Kusto](./synckusto.md) - synchronisation de fonctions stockées Kusto vers/depuis Git

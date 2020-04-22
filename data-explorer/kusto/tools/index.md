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
ms.openlocfilehash: e9b274ae129f7cd15ba30edb24f8432c738f55ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490648"
---
# <a name="azure-data-explorer-tools"></a>Outils Azure Data Explorer

## <a name="ad-hoc-query-tools"></a>Outils de requête ad hoc


* [Kusto.Explorer](./kusto-explorer.md)principal outil de bureau pour l’interrogation et le contrôle de Kusto
* [Interface utilisateur web](https://docs.microsoft.com/azure/data-explorer/web-query-data) - interface utilisateur web pour l’interrogation de Kusto

## <a name="visualizations-dashboards-and-reporting-tools"></a>Visualisations, tableaux de bord et outils de création de rapports


* [Azure Notebooks](https://docs.microsoft.com/azure/data-explorer/azure-notebooks) - utilisez Azure Notebooks pour analyser les données dans Azure Data Explorer.
* Excel
    * [Requête vide Excel](https://docs.microsoft.com/azure/data-explorer/excel-blank-query) - ajout d’une requête Kusto en tant que source de données Excel
    * [Connecteur Excel](https://docs.microsoft.com/azure/data-explorer/excel-connector) - connecteur Excel pour Azure Data Explorer 

* PowerBI

   * [Bonnes pratiques pour Power BI](https://docs.microsoft.com/azure/data-explorer/power-bi-best-practices)
   * [Connecteur Power BI](https://docs.microsoft.com/azure/data-explorer/power-bi-connector)
   * [Requête importée Power BI](https://docs.microsoft.com/azure/data-explorer/power-bi-imported-query) 
   * [Requête SQL Power BI](https://docs.microsoft.com/azure/data-explorer/power-bi-sql-query)

* [Grafana](https://docs.microsoft.com/azure/data-explorer/grafana)

## <a name="orchestration-tools"></a>Outils d’orchestration


* Microsoft Flow
    * [Connecteur Microsoft Flow](https://docs.microsoft.com/azure/data-explorer/flow)
    * [Exemples d’utilisation du connecteur Microsoft Flow](https://docs.microsoft.com/azure/data-explorer/flow-usage)
* [Microsoft Logic App](./logicapps.md) - exécution automatique de requêtes Kusto dans le cadre de [Microsoft Logic App](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)



## <a name="data-ingestion-tools"></a>Code d’ingestion de données


* [LightIngest](https://docs.microsoft.com/azure/data-explorer/lightingest)utilitaire d’aide pour l’ingestion des données ad hoc dans Azure Data Explorer
 



## <a name="source-control-integration-tools"></a>Outils d’intégration du contrôle de code source

* [Azure Pipelines](https://docs.microsoft.com/azure/data-explorer/devops) - appelle des commandes de contrôle dans le cadre de votre pipeline
* [Sync Kusto](./synckusto.md) - synchronisation de fonctions stockées Kusto vers/depuis Git

---
title: Visualisation des données avec Azure Data Explorer
description: Découvrez les différentes manières de visualiser vos données Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: b1351ceb9fe4b81a818ca41728a588dddfb4c5a2
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294676"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualisation des données avec Azure Data Explorer 

Azure Data Explorer est un service d’exploration des données rapide et hautement scalable pour les données de journal et de télémétrie qui servent à créer des solutions d’analytique complexes pour les grandes quantités de données. Azure Data Explorer s’intègre avec de nombreux outils de visualisation, ce qui vous permet de visualiser vos données et partager les résultats dans votre organisation. Ces données peuvent être transformées en informations actionnables pour un plus grand impact sur votre entreprise.

La visualisation et la création de rapports de données sont des étapes essentielles du processus d’analytique des données. Azure Data Explorer prend en charge de nombreux services BI. Vous pouvez ainsi utiliser celui qui convient le mieux à votre scénario et à votre budget.

## <a name="azure-data-explorer-dashboards"></a>Tableaux de bord Azure Data Explorer

Les tableaux de bord Azure Data Explorer sont une application web qui vous permet d’exécuter des requêtes et de créer des tableaux de bord dans l’application web autonome, l’[interface utilisateur web](web-query-data.md). Les tableaux de bord Azure Data Explorer offrent trois avantages principaux :

* Exportation des requêtes en mode natif de l’interface utilisateur web vers des tableaux de bord Azure Data Explorer 
* Exploration des données dans l’interface utilisateur web
* Optimisation des performances de rendu des tableaux de bord

Pour plus d’informations, consultez [Visualiser des données avec des tableaux de bord Azure Data Explorer](azure-data-explorer-dashboards.md).

## <a name="kusto-query-language-visualizations"></a>Visualisations du langage de requête Kusto

Le langage de requête Kusto [`render operator`](kusto/query/renderoperator.md) propose différentes visualisations, comme des tables, des graphiques en secteurs et des graphiques à barres, afin de décrire les résultats de requêtes. Les visualisations de requêtes sont utiles pour la détection et la prévision des anomalies, le Machine Learning et bien plus encore.

## <a name="power-bi"></a>Power BI

Azure Data Explorer permet de se connecter à [Power BI](https://powerbi.microsoft.com) à l’aide de différentes méthodes : 

  * [Connecteur Power BI natif intégré](power-bi-connector.md)

  * [Importation de requête Azure Data Explorer dans Power BI](power-bi-imported-query.md)
 
  * [SQL query](power-bi-sql-query.md)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer offre la possibilité de se connecter à [Microsoft Excel](https://products.office.com/excel) à l’aide du [connecteur Excel natif intégré](excel-connector.md), ou d’[importer une requête](excel-blank-query.md) à partir d’Azure Data Explorer dans Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) propose un plug-in Azure Data Explorer qui vous permet de visualiser des données à partir d’Azure Data Explorer. Vous [configurez Azure Data Explorer en tant que source de données pour Grafana, puis vous visualisez les données](grafana.md). 

## <a name="kibana"></a>Kibana

Azure Data Explorer permet de se connecter à [Kibana](https://www.elastic.co/guide/en/kibana/6.8/discover.html) à l’aide de K2Bridge, connecteur open source. Vous [configurez Azure Data Explorer en tant que source de données pour Kibana, puis vous visualisez les données](k2bridge.md).

## <a name="odbc-connector"></a>Connecteur ODBC

Azure Data Explorer fournit un [connecteur Open Database Connectivity (ODBC)](connect-odbc.md) afin que n’importe quelle application prenant en charge ODBC puisse se connecter à Azure Data Explorer.

## <a name="tableau"></a>Tableau

Azure Data Explorer permet de se connecter à [Tableau](https://www.tableau.com) à l’aide du [connecteur ODBC](connect-odbc.md), puis de [visualiser les données dans Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Data Explorer permet de se connecter à [Qlik](https://www.qlik.com) à l’aide du [connecteur ODBC](connect-odbc.md), puis de créer des tableaux de bord Qlik Sense et de visualiser les données. À l’aide de la vidéo suivante, apprenez à visualiser les données Azure Data Explorer avec Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer permet de se connecter à [Sisense](https://www.sisense.com) à l’aide du connecteur JDBC. Vous [configurez Azure Data Explorer en tant que source de données pour Sisense, puis vous visualisez les données](sisense.md).

## <a name="redash"></a>Redash

Vous pouvez utiliser [Redash](https://redash.io/) pour créer des tableaux de bord et visualiser les données. [Configurez Azure Data Explorer en tant que source de données pour Redash, puis visualisez les données](redash.md).

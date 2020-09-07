---
title: Vue d’ensemble de l’API Azure Data Explorer - Azure Data Explorer
description: Cet article décrit l’API dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2020
ms.openlocfilehash: b9fd03bfd08a31d872ca3c0ef48bd96514e9eb18
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428394"
---
# <a name="azure-data-explorer-api-overview"></a>Vue d’ensemble de l’API Azure Data Explorer

Le service Azure Data Explorer prend en charge les points de terminaison de communication suivants :

1. Point de terminaison d’[API REST](#rest-api), par le biais duquel vous pouvez interroger et gérer les données dans Azure Data Explorer.
   Ce point de terminaison prend en charge le [langage de requête Kusto](../query/index.md) pour les requêtes et les [commandes de contrôle](../management/index.md).
1. Un point de terminaison [MS-TDS](#ms-tds), qui implémente un sous-ensemble du protocole Microsoft TDS (Tabular Data Stream), utilisé par les produits Microsoft SQL Server.
   Ce point de terminaison est utile pour les outils qui savent comment communiquer avec un point de terminaison SQL Server pour les requêtes.
1. Un point de terminaison de [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto) qui est le moyen standard pour les services Azure. Le point de terminaison est utilisé pour gérer les ressources, comme des clusters Azure Data Explorer.

## <a name="rest-api"></a>API REST

Le principal moyen de communiquer avec un service Azure Data Explorer consiste à utiliser l’API REST du service. Avec ce point de terminaison entièrement documenté, les appelants peuvent :

* Interroger des données
* Interroger et modifier les métadonnées
* Réception de données
* Interroger l’état d’intégrité du service
* Gestion des ressources

Les différents services Azure Data Explorer communiquent entre eux via la même API REST disponible publiquement.

Plusieurs [bibliothèques de clients](client-libraries.md) sont également disponibles pour utiliser le service sans avoir à gérer le protocole de l’API REST.

## <a name="ms-tds"></a>MS-TDS

Azure Data Explorer prend aussi en charge le protocole de communication Microsoft SQL Server (MS-TDS) et inclut une prise en charge limitée pour l’exécution de requêtes T-SQL. Ce protocole permet aux utilisateurs d’exécuter des requêtes sur Azure Data Explorer avec une syntaxe de requête connue (T-SQL) et d’outils de client de base de données, comme LINQPad, sqlcmd, Tableau, Excel et Power BI.

Pour plus d’informations, consultez [MS-TDS](tds/index.md).

## <a name="client-libraries"></a>Bibliothèques clientes 

Azure Data Explorer fournit plusieurs bibliothèques de clients qui utilisent les points de terminaison ci-dessus pour faciliter l’accès par programmation.

* Kit de développement logiciel (SDK) .NET
* Kit de développement logiciel (SDK) Python
* R
* Kit de développement logiciel (SDK) Java
* SDK Node
* Kit de développement logiciel (SDK) Go
* PowerShell

### <a name="net-framework-libraries"></a>Bibliothèques .NET Framework

Les bibliothèques .NET Framework sont la méthode recommandée pour appeler des fonctionnalités Azure Data Explorer par programmation.
Plusieurs bibliothèques différentes sont disponibles.

* [Kusto.Data (Bibliothèque de client Kusto)](./netfx/about-kusto-data.md) : Peut être utilisée pour interroger des données, interroger des métadonnées et les modifier. 
   Elle s’appuie sur l’API REST Kusto et envoie des requêtes HTTPS au cluster Kusto cible.
* [Kusto.Ingest (Bibliothèque d’ingestion Kusto)](netfx/about-kusto-ingest.md) : Utilise `Kusto.Data` et l’étend pour faciliter l’ingestion des données.

Les bibliothèques ci-dessus utilisent des API Azure, comme l’API Stockage Azure et l’API Azure Active Directory.

### <a name="python-libraries"></a>Bibliothèques Python

Azure Data Explorer fournit une bibliothèque de client Python qui permet aux appelants d’envoyer des requêtes de données et des commandes de contrôle.
Pour plus d’informations, consultez [SDK Python Azure Data Explorer](python/kusto-python-client-library.md).

### <a name="r-library"></a>Bibliothèque R

Azure Data Explorer fournit une bibliothèque de client R qui permet aux appelants d’envoyer des requêtes de données et des commandes de contrôle.
Pour plus d’informations, consultez [SDK R Azure Data Explorer](r/kusto-r-client-library.md).

### <a name="java-sdk"></a>Kit de développement logiciel (SDK) Java

La bibliothèque de client Java offre la possibilité d’interroger des clusters Azure Data Explorer en utilisant Java. Pour plus d’informations, consultez [SDK Java Azure Data Explorer](java/kusto-java-client-library.md).

### <a name="node-sdk"></a>SDK Node

Le SDK Node Azure Data Explorer est compatible avec Node LTS (actuellement v6.14) et créé avec ES6.
Pour plus d’informations, consultez [SDK Node Azure Data Explorer](node/kusto-node-client-library.md).

### <a name="go-sdk"></a>Kit de développement logiciel (SDK) Go

La bibliothèque de client Go Azure Data Explorer offre la possibilité d’interroger, de contrôler et d’ingérer dans des clusters Azure Data Explorer en utilisant Go. Pour plus d’informations, consultez [SDK Golang Azure Data Explorer](golang/kusto-golang-client-library.md).

### <a name="powershell"></a>PowerShell

Les bibliothèques .NET Framework Azure Data Explorer peuvent être utilisées par des scripts PowerShell. Pour plus d’informations, consultez [Appel d’Azure Data Explorer depuis PowerShell](powershell/powershell.md).

## <a name="monaco-ide-integration"></a>Intégration de l’IDE Monaco

Le package `monaco-kusto` prend en charge l’intégration à l’éditeur web de Monaco.
L’éditeur Monaco, développé par Microsoft, est la base pour Visual Studio Code.
Pour plus d’informations, consultez [Package monaco-kusto](monaco/monaco-kusto.md).

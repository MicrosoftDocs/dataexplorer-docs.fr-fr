---
title: Vue d’ensemble de l’API Azure Data Explorer - Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’API Azure Data Explorer dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: c791ae0be0c895a4744e761c58e7d455aa0a22b9
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610264"
---
# <a name="azure-data-explorer-api-overview"></a>Vue d’ensemble de l’API Azure Data Explorer

Le service Azure Data Explorer prend en charge les points de terminaison de communication suivants :

1. Point de terminaison d’[API REST](#rest-api), par le biais duquel vous pouvez interroger et gérer les données dans Azure Data Explorer.
   Ce point de terminaison prend en charge le [langage de requête Kusto](../query/index.md) pour les requêtes et les [commandes de contrôle](../management/index.md).
2. Point de terminaison [MS-TDS](#ms-tds), qui implémente une partie du protocole Microsoft TDS (Tabular Data Stream), utilisé par les produits Microsoft SQL Server.
   Ce point de terminaison s’avère principalement utile pour les outils existants qui savent comment communiquer avec un point de terminaison SQL Server pour les requêtes.
3. Point de terminaison [ARM (Azure Resource Manager)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto), qui est le moyen standard pour les services Azure de gérer des ressources comme les clusters Azure Data Explorer.

Azure Data Explorer fournit plusieurs bibliothèques de clients qui utilisent les points de terminaison ci-dessus pour faciliter l’accès programmatique :

1. Kit de développement logiciel (SDK) .NET
2. Kit de développement logiciel (SDK) Python
3. Kit de développement logiciel (SDK) Java
4. SDK Node
5. Kit de développement logiciel (SDK) Go
6. PowerShell
7. R

## <a name="rest-api"></a>API REST

Le principal moyen de communiquer avec un service Azure Data Explorer consiste à utiliser l’API REST du service. Par le biais de ce point de terminaison entièrement documenté, les appelants peuvent :

1. Interroger des données
2. Interroger et modifier les métadonnées
3. Réception de données
4. Interroger l’état d’intégrité du service
5. Gestion des ressources

Les différents services Azure Data Explorer communiquent entre eux avec la même API REST accessible à tous.

En plus de prendre en charge la création de requêtes dans Azure Data Explorer avec l’API REST, plusieurs bibliothèques de clients sont disponibles pour utiliser le service sans avoir à gérer le protocole de l’API REST.

## <a name="ms-tds"></a>MS-TDS

En guise d’alternative à la connexion à Azure Data Explorer pour en interroger les données, Azure Data Explorer prend en charge le protocole de communication Microsoft SQL Server (MS-TDS) et inclut une prise en charge limitée de l’exécution de requêtes T-SQL. Cela permet aux utilisateurs d’exécuter des requêtes sur Azure Data Explorer à l’aide d’une syntaxe de requête connue (T-SQL) et d’outils de client de base de données éprouvés (comme LINQPad, sqlcmd, Tableau, Excel et Power BI).

Vous trouverez plus de détails sur MS-TDS sur [cette page](tds/index.md).

## <a name="net-framework-libraries"></a>Bibliothèques .NET Framework

Les bibliothèques .NET Framework sont la méthode recommandée pour appeler des fonctionnalités Azure Data Explorer programmatiquement.
Plusieurs bibliothèques différentes sont fournies :

- [**Kusto.Data (bibliothèque de client Kusto)** ](./netfx/about-kusto-data.md), utilisable pour interroger les données, interroger les métadonnées et les modifier.
- [**Kusto.Ingest (bibliothèque d’ingestion Kusto)** ](netfx/about-kusto-ingest.md), qui utilise Kusto.Data et l’étend pour faciliter l’ingestion des données.


La **bibliothèque de client Kusto** (Kusto.Data) s’appuie sur l’API REST Kusto et envoie des requêtes HTTPS au cluster Kusto cible. 

La **bibliothèque d’ingestion Kusto** (Kusto.Ingest) utilise Kusto.Data.



Toutes les bibliothèques ci-dessus utilisent les API Azure (par exemple, API Stockage Azure, API Azure Active Directory).

## <a name="python-libraries"></a>Bibliothèques Python

Azure Data Explorer fournit une bibliothèque de client Python qui permet aux appelants d’envoyer des requêtes de données et des commandes de contrôle.

## <a name="r-library"></a>Bibliothèque R

Azure Data Explorer fournit une bibliothèque de client R qui permet aux appelants d’envoyer des requêtes de données et des commandes de contrôle.



## <a name="using-azure-data-explorer-from-powershell"></a>Utilisation d’Azure Data Explorer à partir de PowerShell

Les bibliothèques .NET Framework Azure Data Explorer peuvent être utilisées par des scripts PowerShell.
[Appel d’Azure Data Explorer à partir de PowerShell](powershell/powershell.md) fournit un exemple.

## <a name="ide-integration"></a>Intégration de l’IDE

Le package `monaco-kusto` prend en charge l’intégration à Monaco Editor, éditeur web développé par Microsoft et base de Visual Studio Code.
Découvrez plus en détail le package [monaco-kusto](monaco/monaco-kusto.md).
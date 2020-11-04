---
title: Vue d’ensemble des bibliothèques clientes-Azure Explorateur de données
description: Cet article répertorie les bibliothèques clientes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 32c8b8c93ca0ad9fa3b3161ca4cd211cc73eb5c8
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349407"
---
# <a name="client-libraries"></a>Bibliothèques clientes

Le tableau suivant répertorie les différentes bibliothèques fournies pour la gestion des requêtes, de l’ingestion et ARM/RP. Utilisez ces bibliothèques pour les API Azure et pour appeler par programmation les fonctionnalités d’Azure Explorateur de données. 


|    Langue/fonctionnalité        |    Requête        |    Ingestion        |    Gestion ARM/RP        |
|------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------    |------------------------------------------------------------------------------------------------------------------------------    |
|    .NET        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)            |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)         |
|    .NET Standard        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard/)        |            |
|    Java        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/data)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/ingest)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto.v2020_09_18)        |
|    JavaScript        |             |             |    [npm](https://www.npmjs.com/package/@azure/arm-kusto)         |
|    NodeJS        |    [NPM](https://www.npmjs.com/package/azure-kusto-data) [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)        |    [NPM](https://www.npmjs.com/package/azure-kusto-ingest)       [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)        |    [npm](https://www.npmjs.com/package/azure-arm-kusto/v/2.0.0)        |
|    Python        |    [Pypi](https://pypi.org/project/azure-kusto-ingest/)    [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)        |    [Pypi](https://pypi.org/project/azure-kusto-data/)      [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)        |    [Pypi](https://pypi.org/project/azure-mgmt-kusto/)        |
|    R        |    [CRAN](https://cran.r-project.org/web/packages/AzureKusto/index.html)               |             |            |
|    Go        |    [GitHub](https://github.com/Azure/azure-kusto-go)        |    [GitHub](https://github.com/Azure/azure-kusto-go/tree/master/kusto/ingest)        |        [GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/kusto/mgmt)        |
|    Ruby        |             |             |    [Perles]( https://rubygems.org/gems/azure_mgmt_kusto)         |
|    PowerShell        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [Package](https://www.powershellgallery.com/packages/Az.Kusto/)         |
|    Azure CLI        |             |             |    [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)         |
|    API REST        |    [REST](rest/index.md)        |    [REST](rest/index.md)        |     [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/azure-kusto/resource-manager/Microsoft.Kusto)         |
|    TypeScript        |             |             |        [NPM](https://www.npmjs.com/package/@azure/arm-kusto/v/2.0.0)        |
|      |      |      |      |


## <a name="tools-and-integrations"></a>Outils et intégrations

* Lighting Lighting : [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 
* Kit d’ingestion en un clic : [GitHub](https://github.com/Azure/azure-kusto-ingestion-tools) 
* Kafka : [GitHub](https://github.com/Azure/kafka-sink-azure-kusto)
* Logstash : [GitHub](https://github.com/Azure/logstash-output-kusto) 
* Spark : [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
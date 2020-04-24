---
title: Ingestion des données dans l’Explorateur de données Azure
description: Découvrez les différentes manières d’ingérer (charger) des données dans l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 8f48499e1b9bb1c67f43ae575a68bb38e314f08e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493117"
---
# <a name="azure-data-explorer-data-ingestion"></a>Ingestion des données dans l’Explorateur de données Azure

L’ingestion des données est le processus qui consiste à charger des enregistrements de données à partir d’une ou plusieurs sources pour créer ou mettre à jour une table dans l’Explorateur de données Azure. Une fois ingérées, les données sont disponibles pour les requêtes. Le diagramme ci-dessous illustre le flux de bout en bout d’utilisation de l’Explorateur de données Azure, notamment l’ingestion des données.

![Flux de données](media/ingest-data-overview/data-flow.png)

Le service de gestion des données Explorateur de données Azure, qui est responsable de l’ingestion des données, offre les fonctionnalités suivantes :

1. **Extraction de données** : extraire des données provenant de sources externes (hubs d’événements) ou lire des requêtes d’ingestion à partir d’une file d’attente Azure.

1. **Traitement par lot** : traiter par lot des données à destination de la même base de données et de la même table, afin optimiser le débit d’ingestion.

1. **Validation** : validation et conversion de format préliminaires si nécessaire.

1. **Manipulation de données** : mise en correspondance de schéma, organisation, indexation, encodage et compression des données.

1. **Point de persistance dans le flux d’ingestion** : gérer la charge d’ingestion sur le moteur et traiter les nouvelles tentatives en cas de défaillance temporaire.

1. **Validation de l’ingestion des données** : rend les données interrogeables.

## <a name="ingestion-methods"></a>Méthodes d’ingestion

L’Explorateur de données Azure prend en charge plusieurs méthodes d’ingestion, chacune avec ses propres scénarios cibles, avantages et inconvénients. L’Explorateur de données Azure offre des pipelines et des connecteurs pour les services les plus courants, l’ingestion par programmation à l’aide de SDK et un accès direct au moteur de fins d’exploration.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Ingestion à l'aide de pipelines, de connecteurs et de plug-ins

Azure Data Explorer prend actuellement en charge ce qui suit :

* Pipeline Event Grid, qui peut être géré à l'aide de l'Assistant de gestion sur le portail Azure. Pour plus d'informations, consultez [Ingérer des objets blob Azure dans Azure Data Explorer](ingest-data-event-grid.md).

* Pipeline Event Hub, qui peut être géré à l'aide de l'Assistant de gestion sur le portail Azure. Pour plus d'informations, consultez [Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md).

* Plug-in Logstash, voir [Ingérer des données Logstash dans Azure Data Explorer](ingest-data-logstash.md).

* Connecteur Kafka, voir [Ingérer des données Kafka dans Azure Data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Ingestion à l'aide des services d'intégration

* Azure Data Factory (ADF), service d’intégration de données entièrement géré pour les charges de travail analytiques dans Azure, permettant de copier des données vers et à partir d’Azure Data Explorer à l’aide de [formats et de magasins de données pris en charge](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Pour plus d’informations, consultez [Copier des données d’Azure Data Factory vers Azure Data Explorer](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Ingestion par programmation

L’Explorateur de données Azure fournit des SDK qui peuvent être utilisées pour l’ingestion de données et les requêtes. L’ingestion par programmation est optimisée afin de réduire les coûts d’ingestion, grâce à la réduction des transactions de stockage pendant et après le processus d’ingestion.

**SDK et projets open source disponibles** :

Kusto propose des SDK client qui peuvent être utilisés pour ingérer et interroger des données avec :

* [Kit de développement logiciel (SDK) Python](kusto/api/python/kusto-python-client-library.md)

* [Kit de développement logiciel (SDK) .NET](kusto/api/netfx/about-the-sdk.md)

* [Kit SDK Java](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

**Techniques d’ingestion par programmation** :

* Ingestion de données par le biais du service de gestion de données Explorateur de données Azure (ingestion à débit élevé et fiable) :

    [**Ingestion par lot**](kusto/api/netfx/kusto-ingest-queued-ingest-sample.md) (fournie par le SDK) : le client charge les données vers le stockage d’objets blob Azure (désigné par le service de gestion des données Explorateur de données Azure) et envoie une notification à une file d’attente Azure. L’ingestion par lot est la technique recommandée pour une ingestion des données à volume élevé, fiable et peu coûteuse.

* Ingestion des données directement dans le moteur de l’Explorateur de données Azure (approche la plus appropriée pour l’exploration et le prototypage) :

  * **Ingestion inline** : commande de contrôle (.ingest inline) contenant des données intrabande et destinée aux tests ad hoc.

  * **Ingestion à partir de requête** : commande de contrôle (.set, .set-or-append, .set-or-replace) qui pointe vers des résultats de requête et qui sert à générer des rapports ou de petites tables temporaires.

  * **Ingestion à partir du stockage** : commande de contrôle (.ingest into) avec données stockées en externe (par exemple, Stockage Blob Azure) qui permet d’effectuer une ingestion en bloc efficace des données.

**Latence des différentes méthodes** :

| Méthode | Latence |
| --- | --- |
| **Ingestion inline** | Immédiat |
| **Ingestion à partir de requête** | Temps de requête + temps de traitement |
| **Ingestion à partir du stockage** | Temps de téléchargement + temps de traitement |
| **Ingestion en file d’attente** | Temps de traitement par lot + temps de traitement |
| |

Le temps de traitement dépend de la taille des données (il est inférieur à quelques secondes). Le temps de traitement par lot par défaut est de cinq minutes.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Choix de la méthode d’ingestion la plus appropriée

Avant de commencer à ingérer les données, vous devez vous poser les questions suivantes.

* Où mes données résident-elles ? 
* Quel est le format des données, et peut-il être modifié ? 
* Quels sont les champs obligatoires à interroger ? 
* Quels sont la vélocité et le volume de données attendus ? 
* Combien de types d’événements sont attendus (correspondant au nombre de tables) ? 
* Quelle est la fréquence de changement attendue du schéma d’événement ? 
* Combien de nœuds les données génèreront-elles ? 
* Quel est le système d’exploitation source ? 
* Quelles sont les exigences de latence ? 
* Peut-on utiliser l’un des pipelines d’ingestion managés existants ? 

Pour les organisations disposant d’une infrastructure existante basée sur un service de messagerie tel qu’Event Hub et IoT Hub, l’utilisation d’un connecteur est probablement la solution la plus appropriée. L’ingestion en file d’attente convient aux grands volumes de données.

## <a name="supported-data-formats"></a>Formats de données pris en charge

Pour toutes les méthodes d’ingestion autres que l’ingestion à partir de requête, mettez en forme les données afin qu’Azure Data Explorer puisse les analyser. 
* les formats de données pris en charge sont : TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (séparé par une ligne, multiligne), Avro, Orc et Parquet. 
* Prend en charge la compression ZIP et GZIP.

> [!NOTE]
> Quand des données sont ingérées, les types de données sont déduits d’après les colonnes de la table cible. Si un enregistrement est incomplet ou un champ ne peut pas être analysé en tant que type de données requis, les colonnes correspondantes de la table sont remplies avec des valeurs null.

## <a name="ingestion-recommendations-and-limitations"></a>Restrictions et recommandations d’ingestion

* La stratégie de rétention effective des données ingérées est dérivée de la stratégie de rétention de la base de données. Pour plus d’informations, consultez [Stratégie de rétention](kusto/management/retentionpolicy.md). L’ingestion des données nécessite l’autorisation **Ingéreur de table** ou **Ingéreur de base de données**.
* L’ingestion prend en charge une taille de fichier maximale de 5 Go. Nous vous recommandons d’ingérer des fichiers entre 100 Mo et 1 Go.

## <a name="schema-mapping"></a>Mappage de schéma

Le mappage de schéma permet de lier des champs de données sources à des colonnes de table de destination.

* Le [mappage CSV](kusto/management/mappings.md#csv-mapping) (facultatif) fonctionne avec tous les formats basés sur un ordinal. Il peut être effectué à l’aide du paramètre de commande d’ingestion ou [précréé sur la table](kusto/management/create-ingestion-mapping-command.md) et référencé à partir du paramètre de commande d’ingestion.
* Le [mappage JSON](kusto/management/mappings.md#json-mapping) (obligatoire) et le [mappage Avro](kusto/management/mappings.md#avro-mapping) (obligatoire) peuvent être effectués à l'aide du paramètre de commande ingest. Ils peuvent également être [pré-créés sur la table](kusto/management/create-ingestion-mapping-command.md) et référencés à partir du paramètre de commande ingest.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Ingérer des données à l'aide de l'abonnement Event Grid dans Azure Data Explorer](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Ingérer des données Kafka dans Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Ingérer des données à l'aide de la bibliothèque Python d'Azure Data Explorer](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingérer des données à l'aide de la bibliothèque Node d'Azure Data Explorer](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingérer des données à l'aide du SDK .NET Standard d'Azure Data Explorer (préversion)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Ingérer des données Logstash dans Azure Data Explorer](ingest-data-logstash.md)

---
title: Vue d’ensemble de l’ingestion des données dans Azure Data Explorer
description: Découvrez les différentes manières d’ingérer (charger) des données dans l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/18/2020
ms.localizationpriority: high
ms.openlocfilehash: 5304d2fcce23d6143faebb9326a6ab960a964f22
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512398"
---
# <a name="azure-data-explorer-data-ingestion-overview"></a>Vue d’ensemble de l’ingestion des données dans Azure Data Explorer 

L’ingestion des données est le processus qui consiste à charger des enregistrements de données d’une ou plusieurs sources pour importer des données dans une table dans Azure Data Explorer. Une fois ingérées, les données sont disponibles pour les requêtes.

Le diagramme ci-dessous montre le flux d’utilisation d’Azure Data Explorer de bout en bout ainsi que différentes méthodes d’ingestion.

:::image type="content" source="media/data-ingestion-overview/data-management-and-ingestion-overview.png" alt-text="Schéma de présentation de l’ingestion et de la gestion des données":::

Le service de gestion des données Azure Data Explorer, qui est responsable de l’ingestion des données, implémente le processus suivant :

Azure Data Explorer extrait des données d’une source externe et lit les demandes d’une file Azure en attente. Les données sont traitées par lot ou diffusées en streaming à Data Manager. Les données traitées par lot à destination de la même base de données et de la même table sont optimisées pour un débit d’ingestion élevé. Azure Data Explorer valide les données initiales et convertit les formats de données si nécessaire. Parmi les autres tâches de manipulation des données, citons la mise en correspondance de schéma, l’organisation, l’indexation, l’encodage et la compression des données. Les données sont conservées dans le stockage en fonction d’une stratégie de conservation définie. Data Manager commite ensuite les données ingérées dans le moteur où elles peuvent faire l’objet de requêtes. 

## <a name="supported-data-formats-properties-and-permissions"></a>Formats de données pris en charge, propriétés et autorisations

* **[Formats de données pris en charge](ingestion-supported-formats.md)** 

* **[Propriétés d’ingestion](ingestion-properties.md)**  : propriétés qui affectent la façon dont les données sont ingérées (par exemple : étiquetage, mappage, heure de création).

* **Autorisations** : pour ingérer des données, le processus nécessite des [autorisations de niveau Ingéreur de base de données](kusto/management/access-control/role-based-authorization.md). D’autres actions, comme l’exécution d’une requête, peuvent nécessiter des autorisations d’administrateur de base de données, d’utilisateur de base de données ou d’administrateur de table.

## <a name="batching-vs-streaming-ingestion"></a>Ingestion par lot/en streaming

* L’ingestion par lot, qui traite les données par lot, est optimisée pour un débit d’ingestion élevé. Cette méthode d’ingestion est la plus performante et donc recommandée. Les données sont traitées par lot en fonction des propriétés d’ingestion. Les petits lots de données sont ensuite fusionnés et optimisés pour des résultats de requête rapides. La stratégie d’[ingestion par lot](kusto/management/batchingpolicy.md) peut être définie sur des bases de données ou des tables. Par défaut, les valeurs maximales de traitement par lot sont les suivantes : 5 minutes, 1000 éléments ou une taille totale de 1 Go.

* L’[ingestion en streaming](ingest-data-streaming.md) ingère les données en continu à partir d’une source de streaming. L’ingestion en streaming permet une latence en quasi temps réel pour les petits jeux de données par table. Les données sont initialement ingérées dans un rowstore, puis déplacées dans des étendues columnstore. L’ingestion en streaming peut être effectuée à l’aide d’une bibliothèque cliente Azure Data Explorer ou de l’un des pipelines de données pris en charge. 

## <a name="ingestion-methods-and-tools"></a>Méthodes d’ingestion et outils

Azure Data Explorer prend en charge plusieurs méthodes d’ingestion, chacune avec ses propres scénarios cibles. Parmi ces méthodes, citons des outils d’ingestion, des connecteurs et des plug-ins pour divers services, des pipelines gérés, l’ingestion programmatique à l’aide de kits SDK et l’accès direct à l’ingestion.

### <a name="ingestion-using-managed-pipelines"></a>Ingestion à l’aide de pipelines gérés

Pour les organisations qui souhaitent confier la gestion (limitation, nouvelles tentatives, analyses, alertes, etc.) à un service externe, l’utilisation d’un connecteur est probablement la solution la plus appropriée. L’ingestion en file d’attente convient aux grands volumes de données. Azure Data Explorer prend en charge les pipelines Azure suivants :

* **[Event Grid](https://azure.microsoft.com/services/event-grid/)**  : pipeline qui écoute le stockage Azure et met à jour Azure Data Explorer pour extraire des informations à la suite d’événements associés à des abonnements. Pour plus d'informations, consultez [Ingérer des objets blob Azure dans Azure Data Explorer](ingest-data-event-grid.md).

* **[Event Hub](https://azure.microsoft.com/services/event-hubs/)**  : pipeline qui transfère des événements de services vers Azure Data Explorer. Pour plus d'informations, consultez [Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md).

* **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)** : pipeline utilisé pour le transfert de données d’appareils IoT pris en charge vers Azure Data Explorer. Pour plus d’informations, consultez [Ingérer à partir d’IoT Hub](ingest-data-iot-hub.md).

* **Azure Data Factory (ADF)**  : service d’intégration de données complètement managé pour les charges de travail analytiques dans Azure. Azure Data Factory se connecte à plus de 90 sources prises en charge pour assurer un transfert de données efficace et résilient. ADF prépare, transforme et enrichit les données pour fournir des insights qui peuvent être supervisés de différentes façons. Ce service peut apporter une solution ponctuelle, s’inscrire dans une chronologie périodique ou être déclenché par des événements spécifiques. 
  * [Intégrer Azure Data Explorer à Azure Data Factory](data-factory-integration.md).
  * [Utiliser Azure Data Factory pour copier des données de sources prises en charge vers Azure Data Explorer](./data-factory-load-data.md).
  * [Copier en bloc à partir d’une base de données vers Azure Data Explorer à l’aide du modèle Azure Data Factory](data-factory-template.md).
  * [Utiliser l’activité de commande Azure Data Factory pour exécuter des commandes de contrôle Azure Data Explorer](data-factory-command-activity.md).

### <a name="ingestion-using-connectors-and-plugins"></a>Ingestion à l’aide de connecteurs et de plug-ins

* **Plug-in Logstash** : consultez [Ingérer des données Logstash dans Azure Data Explorer](ingest-data-logstash.md).

* **Connecteur Kafka** : consultez [Ingérer des données Kafka dans Azure Data Explorer](ingest-data-kafka.md).

* **[Power Automate](https://flow.microsoft.com/)**  : pipeline de workflow automatisé vers Azure Data Explorer. Vous pouvez utiliser Power Automate pour exécuter une requête et effectuer des actions prédéfinies avec pour déclencheur les résultats de la requête. Consultez [Connecteur Azure Data Explorer vers Power Automate (préversion)](flow.md).

* **Connecteur Apache Spark** :  projet open source pouvant s’exécuter sur n’importe quel cluster Spark. Il implémente la source de données et le récepteur de données pour déplacer les données entre les clusters Azure Data Explorer et Spark. Vous pouvez créer des applications rapides et scalables ciblant des scénarios basés sur les données. Consultez [Connecteur Azure Data Explorer pour Apache Spark](spark-connector.md).

### <a name="programmatic-ingestion-using-sdks"></a>Ingestion programmatique à l’aide de kits SDK

L’Explorateur de données Azure fournit des SDK qui peuvent être utilisées pour l’ingestion de données et les requêtes. L’ingestion par programmation est optimisée afin de réduire les coûts d’ingestion, grâce à la réduction des transactions de stockage pendant et après le processus d’ingestion.

**Kits SDK et projets open source disponibles**

* [Kit de développement logiciel (SDK) Python](kusto/api/python/kusto-python-client-library.md)

* [Kit de développement logiciel (SDK) .NET](kusto/api/netfx/about-the-sdk.md)

* [Kit SDK Java](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

* [API GO](kusto/api/golang/kusto-golang-client-library.md)

### <a name="tools"></a>Outils

* **[Ingestion en un clic](ingest-data-one-click.md)**  : vous permet d’ingérer rapidement des données en créant et en ajustant des tables à partir d’un large éventail de types sources. L’ingestion en un clic suggère automatiquement des tables et des structures de mappage en fonction de la source de données dans Azure Data Explorer. L’ingestion en un clic peut constituer une méthode d’ingestion ponctuelle. Elle peut aussi servir à définir l’ingestion continue, par le biais d’Event Grid, sur le conteneur dans lequel les données ont été ingérées.

* **[LightIngest](lightingest.md)**  : utilitaire de ligne de commande pour l’ingestion de données ad hoc dans Azure Data Explorer. L’utilitaire peut extraire les données sources à partir d’un dossier local ou d’un conteneur Stockage Blob Azure.

### <a name="kusto-query-language-ingest-control-commands"></a>Commandes de contrôle d’ingestion KQL

Plusieurs méthodes faisant appel à des commandes KQL (Kusto Query Language) permettent d’ingérer directement des données dans le moteur. Étant donné que cette méthode ignore les services Gestion des données, elle ne convient qu’à l’exploration et au prototypage. N’utilisez pas cette méthode dans les scénarios de production ou ceux impliquant de grands volumes.

  * **Ingestion inline** :  une commande de contrôle [.ingest inline](kusto/management/data-ingestion/ingest-inline.md) est envoyée au moteur, les données à ingérer étant intégrées au texte de la commande. Cette méthode est destinée à des fins de tests improvisés.

  * **Ingestion à partir d’une requête** : une commande de contrôle [.set, .append, .set-or-append ou .set-or-replace](kusto/management/data-ingestion/ingest-from-query.md) est envoyée au moteur, les données étant spécifiées indirectement comme résultats d’une requête ou d’une commande.

  * **Ingestion à partir du stockage - tirage (pull)**  : Une commande de contrôle [.ingest into](kusto/management/data-ingestion/ingest-from-storage.md) est envoyée au moteur, les données étant stockées dans un stockage externe (par exemple, Stockage Blob Azure) accessible par le moteur et vers lequel la commande pointe.

## <a name="comparing-ingestion-methods-and-tools"></a>Comparaison des méthodes et des outils d’ingestion

| Nom de l’ingestion | Type de données | Taille maximale du fichier | Streaming, traitement par lot, directe | Scénarios les plus courants | Considérations |
| --- | --- | --- | --- | --- | --- |
| [**Ingestion en un clic**](ingest-data-one-click.md) | *sv, JSON | 1 Go non compressé (voir remarque)| Traitement par lot vers conteneur, fichier local et objet blob dans l’ingestion directe | Cas uniques, création d’un schéma de table, définition de l’ingestion continue avec Event Grid, ingestion en bloc avec conteneur (jusqu’à 10 000 objets blob) | 10 000 objets blob sélectionnés de façon aléatoire dans le conteneur|
| [**LightIngest**](lightingest.md) | Tous les formats pris en charge | 1 Go non compressé (voir remarque) | Traitement par lot par le biais de DM ou ingestion directe vers le moteur |  Migration des données, données historiques avec horodatages d’ingestion ajustés, ingestion en bloc (aucune restriction de taille)| Respect de la casse, respect des espaces |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX to Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory**](./data-factory-integration.md) | [Formats de données pris en charge](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | Illimitée* (selon les restrictions ADF) | Traitement par lot ou par déclencheur ADF | Prend en charge les formats qui ne sont généralement pas pris en charge et les fichiers volumineux, peut copier de plus de 90 sources du stockage local vers le cloud | Heure d’ingestion |
|[ **Azure Data Flow**](./flow.md) | | | | Commandes d’ingestion dans le cadre du flux| Doit avoir un temps de réponse élevé |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [Formats de données pris en charge](ingest-data-iot-hub-overview.md#data-format)  | N/A | Traitement par lot, streaming | Messages IoT, événements IoT, propriétés IoT | |
| [**Event Hub**](ingest-data-event-hub-overview.md) | [Formats de données pris en charge](ingest-data-event-hub-overview.md#data-format) | N/A | Traitement par lot, streaming | Messages, événements | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [Formats de données pris en charge](ingest-data-event-grid-overview.md#data-format) | 1 Go non compressé | Traitement par lot | Ingestion continue à partir du stockage Azure, données externes dans le stockage Azure | 100 Ko est la taille de fichier optimale, utilisée pour la création et le renommage d’objets blob |
| [**Kit de développement logiciel (SDK) .NET**](./net-sdk-ingest-data.md) | Tous les formats pris en charge | 1 Go non compressé (voir remarque) | Traitement par lot, streaming, directe | Code personnalisé en fonction des besoins de l’organisation |
| [**Python**](python-ingest-data.md) | Tous les formats pris en charge | 1 Go non compressé (voir remarque) | Traitement par lot, streaming, directe | Code personnalisé en fonction des besoins de l’organisation |
| [**Node.js**](node-ingest-data.md) | Tous les formats pris en charge | 1 Go non compressé (voir remarque) | Traitement par lot, streaming, directe | Code personnalisé en fonction des besoins de l’organisation |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | Tous les formats pris en charge | 1 Go non compressé (voir remarque) | Traitement par lot, streaming, directe | Code personnalisé en fonction des besoins de l’organisation |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | Tous les formats pris en charge | 1 Go non compressé (voir remarque) | Traitement par lot, streaming, directe| Code personnalisé en fonction des besoins de l’organisation |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | Tous les formats pris en charge | 1 Go non compressé (voir remarque) | Traitement par lot, streaming, directe | Code personnalisé en fonction des besoins de l’organisation |

> [!Note] 
> Quand elle est référencée dans le tableau ci-dessus, l’ingestion prend en charge une taille de fichier maximale de 4 Go. Nous vous recommandons d’ingérer des fichiers entre 100 Mo et 1 Go.

## <a name="ingestion-process"></a>Processus d’ingestion

Une fois que vous avez choisi la méthode d’ingestion la mieux adaptée à vos besoins, effectuez les étapes suivantes :

1. **Définir la stratégie de conservation**

    Les données ingérées dans une table dans Azure Data Explorer sont soumises à la stratégie de conservation effective de la table. À moins d’être explicitement définie sur une table, la stratégie de conservation effective découle de la stratégie de conservation de la base de données. La conservation à chaud est une fonction de la taille de cluster et de votre stratégie de conservation. Le fait d’ingérer plus de données que d’espace disponible forcera la conservation à froid des premières données entrées.
    
    Vérifiez que la stratégie de conservation de la base de données est adaptée à vos besoins. Si ce n’est pas le cas, remplacez-la explicitement au niveau de la table. Pour plus d’informations, consultez [Stratégie de conservation](kusto/management/retentionpolicy.md). 
    
1. **Création d'une table**

    Avant de pouvoir ingérer des données, vous devez créer une table. Utilisez l’une des options suivantes :
   * Créez une table [avec une commande](kusto/management/create-table-command.md). 
   * Créez une table à l’aide de l’[ingestion en un seul clic](one-click-ingestion-new-table.md).

    > [!Note]
    > Si un enregistrement est incomplet ou un champ ne peut pas être analysé en tant que type de données requis, les colonnes correspondantes de la table sont remplies avec des valeurs null.

1. **Créer un mappage de schéma**

    Le [mappage de schéma](kusto/management/mappings.md) permet de lier des champs de données sources à des colonnes de table de destination. Le mappage vous permet de regrouper dans une même table les données de différentes sources en fonction des attributs définis. Différents types de mappages sont pris en charge, à la fois orientés ligne (CSV, JSON et AVRO) et orientés colonne (Parquet). Dans la plupart des méthodes, des mappages peuvent également être [précréés sur la table](kusto/management/create-ingestion-mapping-command.md) et référencés à partir du paramètre de commande ingest.

1. **Définir une stratégie de mise à jour** (facultatif)

   Certains mappages de format de données (Parquet, JSON et Avro) prennent en charge des transformations simples et utiles au moment de l’ingestion. Si le scénario nécessite un traitement plus complexe au moment de l’ingestion, utilisez la stratégie de mise à jour. Celle-ci permet d’effectuer un traitement léger au moyen de commandes KQL. La stratégie de mise à jour exécute automatiquement les extractions et les transformations sur les données ingérées de la table d’origine, puis ingère les données résultantes dans une ou plusieurs tables de destination. Définissez votre [stratégie de mise à jour](kusto/management/update-policy.md).



## <a name="next-steps"></a>Étapes suivantes

* [Formats de données pris en charge](ingestion-supported-formats.md)
* [Propriétés d’ingestion prises en charge](ingestion-properties.md)
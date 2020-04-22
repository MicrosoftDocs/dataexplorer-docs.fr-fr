---
title: Interroger des données dans Azure Data Lake à l'aide d'Azure Data Explorer
description: Découvrez comment interroger des données dans Azure Data Lake à l'aide d'Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: aa1cdb647190c804c5dc18d1575a6030f166a845
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493329"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Interroger des données dans Azure Data Lake à l'aide d'Azure Data Explorer

Azure Data Lake Storage est une solution Data Lake hautement évolutive et économique pour l’analytique Big Data. Elle combine la puissance d’un système de fichiers haute performance à grande échelle et la rentabilité qui vous permettront d’obtenir des insights rapidement. Data Lake Storage Gen2 étend les fonctionnalités de Stockage Blob Azure et est optimisé pour les charges de travail analytiques.
 
Azure Data Explorer s’intègre à Stockage Blob Azure et Azure Data Lake Storage (Gen1 et Gen2) pour offrir un accès rapide, en cache et indexé aux données du lac. Vous pouvez analyser et interroger les données du lac, sans ingestion préalable dans Azure Data Explorer. Vous pouvez également interroger simultanément des données de lac natives ingérées ou non ingérées.  

> [!TIP]
> Pour des requêtes plus performantes, l'ingestion des données dans Azure Data Explorer est requise. La possibilité d’interroger les données externes sans ingestion préalable est à réserver aux données historiques ou aux données rarement interrogées. [Optimisez les performances de vos requêtes dans le lac](#optimize-your-query-performance) pour obtenir de meilleurs résultats.
 

## <a name="create-an-external-table"></a>Créer une table externe

 > [!NOTE]
 > Les comptes de stockage actuellement pris en charge sont Stockage Blob Azure ou Azure Data Lake Storage (Gen1 et Gen2).

1. Utilisez la commande `.create external table` pour créer une table externe dans Azure Data Explorer. Les commandes de table externe supplémentaires telles que `.show`, `.drop` et `.alter` sont documentées dans [Commandes de table externe](kusto/management/externaltables.md).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * En règle générale, un partitionnement plus granulaire améliore le niveau de performance. Par exemple, les requêtes sur des tables externes avec partitions quotidiennes offrent un meilleur niveau de performance que les requêtes sur des tables partitionnées mensuellement.
    > * Lorsque vous définissez une table externe avec des partitions, la structure de stockage doit être identique.
Par exemple, si la table est définie avec une partition DateTime au format aaaa/mm/jj (par défaut), le chemin d’accès au fichier de stockage URI doit être *container1/aaaa/mm/jj/all_exported_blobs*. 
    > * Si la table externe est partitionnée par une colonne DateHeure, ajoutez toujours à votre requête un filtre de temps définissant une plage fermée. Par exemple, la requête `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` offrira de meilleurs résultats que la requête `ArchivedProducts | where Timestamp > ago(1h)` (avec plage ouverte). 
    > * Tous les [formats d’ingestion pris en charge](ingest-data-overview.md#supported-data-formats) peuvent être interrogés à l’aide de tables externes.

1. La table externe est visible dans le volet gauche de l’interface utilisateur web

    ![table externe dans l'interface utilisateur web](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Créer un tableau externe au format json

Vous pouvez créer un tableau externe au format json. Pour plus d’informations, consultez l’article [Commandes du tableau externe](kusto/management/externaltables.md).

1. Utilisez la commande `.create external table` pour créer un tableau nommé *ExternalTableJson* :

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Le format json nécessite une deuxième étape de création de mappage aux colonnes, comme indiqué ci-dessous. Dans la requête suivante, créez un mappage JSON spécifique nommé *mappingName* :

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Autorisations de table externe
 
* L’utilisateur de base de données peut créer une table externe. Le créateur de la table en devient automatiquement l’administrateur.
* L'administrateur de cluster, base de données ou table peut modifier une table existante.
* Tout utilisateur ou lecteur de base de données peut interroger une table externe.
 
## <a name="query-an-external-table"></a>Interroger une table externe
 
Pour interroger une table externe, utilisez la fonction `external_table()` et entrez le nom de la table en tant qu'argument de fonction. Le reste de la requête correspond au langage de requête Kusto standard.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Actuellement, IntelliSense n’est pas pris en charge sur les requêtes de table externe.

### <a name="query-an-external-table-with-json-format"></a>Interroger un tableau externe au format json

Pour interroger un tableau externe au format json, utilisez la fonction `external_table()` et entrez le nom du tableau et le nom du mappage en tant qu'arguments de fonction. Dans la requête ci-dessous, si *mappingName* n’est pas spécifié, un mappage que vous avez créé précédemment sera utilisé.

```kusto
external_table('ExternalTableJson', 'mappingName')
```

## <a name="query-external-and-ingested-data-together"></a>Interroger simultanément des données externes et ingérées

Vous pouvez utiliser la même requête pour interroger des tables externes et des tables de données ingérées. Vous devez [`join`](kusto/query/joinoperator.md) ou [`union`](kusto/query/unionoperator.md) la table externe avec des données supplémentaires issues d'Azure Data Explorer, de serveurs SQL ou d’autres sources. Utilisez une [`let( ) statement`](kusto/query/letstatement.md) pour attribuer un nom abrégé à une référence de table externe.

Dans l’exemple ci-dessous, *Products* correspond à une table de données ingérées et *ArchivedProducts* à une table externe contenant des données dans Azure Data Lake Storage Gen2 :

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Interroger la table *TaxiRides* dans le cluster d'aide

L'exemple de jeu de données *TaxiRides* contient des données relatives aux taxis new-yorkais issues de [NYC Taxi et Limousines Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Créer une table externe *TaxiRides* 

> [!NOTE]
> Cette section décrit la requête utilisée pour créer la table externe *TaxiRides* dans le cluster *help*. Cette table ayant déjà été créée, vous pouvez ignorer cette section et [interroger*les*données de la table externe](#query-taxirides-external-table-data) TaxiRides. 

1. La requête suivante a été utilisée pour créer la table externe *TaxiRides* dans le cluster d'aide. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. La table qui en résulte a été créée dans le cluster d'*aide* :

    ![Table externe TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Interroger les données de la table externe *TaxiRides* 

Connectez-vous à [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) pour interroger la table externe *TaxiRides*. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Interroger la table externe *TaxiRides* sans partitionnement

[Exécutez cette requête](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) sur la table externe *TaxiRides* pour représenter les courses de chaque jour de la semaine, dans le jeu de données entier. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Cette requête affiche le jour le plus chargé de la semaine. Les données n'étant pas partitionnées, cette requête peut mettre un certain temps à renvoyer des résultats (jusqu'à plusieurs minutes).

![afficher la requête non partitionnée](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Interroger la table externe TaxiRides avec partitionnement 

[Exécutez cette requête](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) sur la table externe *TaxiRides* affichant les types de taxis (jaunes ou verts) utilisés janvier 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Cette requête utilise le partitionnement, ce qui optimise le temps de requête et le niveau de performance. La requête filtre une colonne partitionnée (pickup_datetime) et renvoie les résultats en quelques secondes.

![afficher la requête partitionnée](media/data-lake-query-data/taxirides-with-partition.png)
  
Vous pouvez écrire des requêtes supplémentaires à exécuter sur la table externe *TaxiRides* et en savoir plus sur les données. 

## <a name="optimize-your-query-performance"></a>Optimiser les performances de vos requêtes

Optimisez les performances de vos requêtes dans le lac en observant les bonnes pratiques suivantes pour interroger des données externes. 
 
### <a name="data-format"></a>Format de données
 
Utilisez un format en colonnes pour les requêtes analytiques dans la mesure où :
* Seules les colonnes appropriées à une requête peuvent être lues. 
* Les techniques d’encodage de colonne peuvent réduire considérablement la taille des données.  
Azure Data Explorer prend en charge les formats en colonnes Parquet et ORC. Nous suggérons le format Parquet car son implémentation est optimisée. 
 
### <a name="azure-region"></a>Région Azure 
 
Veillez à ce que les données externes se trouvent dans la même région Azure que votre cluster Azure Data Explorer. Cela réduit le coût et le temps de l’extraction des données.
 
### <a name="file-size"></a>Taille du fichier
 
La taille de fichier optimale est de plusieurs centaines de Mo (jusqu’à 1 Go) par fichier. Évitez d’avoir un grand nombre de petits fichiers qui nécessitent une surcharge inutile, par exemple un processus d’énumération de fichiers plus lent et une utilisation limitée du format en colonnes. Notez que le nombre de fichiers doit être supérieur au nombre de cœurs de processeur dans votre cluster Azure Data Explorer. 
 
### <a name="compression"></a>Compression
 
Utilisez la compression pour réduire la quantité de données extraites du stockage distant. Pour le format Parquet, utilisez le mécanisme de compression Parquet interne qui compresse les groupes de colonnes séparément, ce qui vous permet de les lire séparément. Pour valider l’utilisation du mécanisme de compression, vérifiez que les fichiers sont nommés comme suit : « <filename>.gz.parquet » ou « <filename>.snappy.parquet », et non « <filename>.parquet.gz ». 
 
### <a name="partitioning"></a>Partitionnement
 
Organisez vos données en utilisant des partitions de « dossier » qui permettent à la requête d’ignorer les chemins non pertinents. Lors de la planification du partitionnement, tenez compte de la taille de fichier et des filtres courants dans vos requêtes, tels que l’horodatage ou l’ID de locataire.
 
### <a name="vm-size"></a>Taille de la machine virtuelle
 
Sélectionnez les références SKU de machine virtuelle avec plus de cœurs et un débit réseau plus élevé (la mémoire est moins importante). Pour plus d’informations, consultez [Sélectionner la bonne référence SKU de machine virtuelle pour votre cluster Azure Data Explorer](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Étapes suivantes

Interrogez vos données dans Azure Data Lake à l'aide d'Azure Data Explorer. Apprenez à [écrire des requêtes](write-queries.md) et à tirer plus d'insights de vos données.

---
title: L’option LightIngest est un utilitaire de ligne de commande pour l’ingestion dans Azure Data Explorer.
description: Apprenez-en davantage sur LightIngest, un utilitaire de ligne de commande pour l’ingestion des données ad hoc dans Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: e9918452aaeefc2c4f144c60e2e7bdb70ac87f2b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494509"
---
# <a name="install-and-use-lightingest"></a>Installer et utiliser LightIngest

LightIngest est un utilitaire de ligne de commande pour l’ingestion de données ad hoc dans Azure Data Explorer.
L’utilitaire peut extraire les données sources à partir d’un dossier local ou d’un conteneur Stockage Blob Azure.

## <a name="prerequisites"></a>Prérequis

* LightIngest : téléchargez-le dans le cadre du [package NuGet Microsoft.Azure.Kusto.Tools](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    ![Téléchargement LightIngest](media/lightingest/lightingest-download-area.png)

* WinRAR : téléchargez-le à partir de [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Installer LightIngest

1. Accédez à l’emplacement sur votre ordinateur où vous avez téléchargé LightIngest. 
1. À l’aide de WinRAR, extrayez le répertoire *tools* sur votre ordinateur.

## <a name="run-lightingest"></a>Exécuter LightIngest

1. Accédez au répertoire *tools* extrait sur votre ordinateur.
1. Supprimez les informations d’emplacement existantes de la barre d’emplacement.
    
      ![Supprimer les informations d'emplacement](media/lightingest/lightingest-location-bar.png)

1. Entrez `cmd`, puis appuyez sur **Entrée**.
1. À l’invite de commandes, saisissez `LightIngest.exe` suivi de l’argument de ligne de commande approprié.

    > [!Tip]
    > Pour obtenir la liste des arguments de ligne de commande pris en charge, saisissez `LightIngest.exe /help`.
    >
    >![Aide de ligne de commande](media/lightingest/lightingest-cmd-line-help.png)

1. Saisissez `ingest-` suivi de la chaîne de connexion au cluster Azure Data Explorer qui gérera l’ingestion.
    Mettez la chaîne de connexion entre guillemets doubles et suivez la [Spécification des chaînes de connexion Kusto](kusto/api/connection-strings/kusto.md).

    Par exemple :
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* La méthode recommandée consiste à faire fonctionner LightIngest avec le point de terminaison d’ingestion sur `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`. De cette façon, le service Azure Data Explorer peut gérer la charge d’ingestion et vous pouvez facilement récupérer en cas d’erreurs temporaires. Toutefois, vous pouvez également configurer LightIngest pour fonctionner directement avec le point de terminaison du moteur (`https://{yourClusterNameAndRegion}.kusto.windows.net`).

> [!Note]
> Si vous ingérez directement avec le point de terminaison du moteur, vous n’avez pas besoin d’inclure `ingest-`, mais il n’existe pas de fonctionnalité DM pour protéger le moteur et améliorer le taux de réussite de l’ingestion.

* Pour des performances d’ingestion optimales, il est important pour LightIngest de connaître la taille des données brutes et, par conséquent, LightIngest estime la taille non compressée des fichiers locaux. Toutefois, LightIngest peut ne pas être en mesure d’estimer correctement la taille brute des blobs compressés sans les télécharger au préalable. Par conséquent, lors de la réception d’objets BLOB compressés, définissez la propriété `rawSizeBytes` sur les métadonnées de l’objet BLOB sur la taille des données non compressées en octets.

## <a name="general-command-line-arguments"></a>Arguments généraux de ligne de commande

|Nom de l’argument         |Nom court   |Type    |Obligatoire |Description                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |string  |Obligatoire |[Chaîne de connexion à Azure Data Explorer](kusto/api/connection-strings/kusto.md) spécifiant le point de terminaison Kusto qui gérera l’ingestion. La valeur doit être entourée de guillemets doubles |
|-database             |-db          |string  |Facultatif  |Nom de la base de données Azure Data Explorer cible |
|-table                |             |string  |Obligatoire |Nom de la table Azure Data Explorer cible |
|-sourcePath           |-source      |string  |Obligatoire |Chemin d’accès aux fichiers sources ou URI racine du conteneur d’objets BLOB. Si les données se trouvent dans des objets BLOB, une clé de compte de stockage ou une signature d’accès partagé doit être incluse. Il est recommandé d’entourer la valeur de guillemets doubles |
|-prefix               |             |string  |Facultatif  |Lorsque les données sources à ingérer résident dans le stockage d’objets BLOB, ce préfixe d’URL est partagé par tous les objets BLOB, à l’exclusion du nom du conteneur. <br>Par exemple, si les données sont dans `MyContainer/Dir1/Dir2`, le préfixe doit être `Dir1/Dir2`. La délimitation entre guillemets doubles est recommandée |
|-pattern              |             |string  |Facultatif  |Modèle suivant lequel les fichiers sources/objets BLOB sont choisis. Prend en charge les caractères génériques. Par exemple : `"*.csv"`. Il est recommandé d’entourer la valeur de guillemets doubles |
|-zipPattern           |             |string  |Facultatif  |Expression régulière à utiliser lors de la sélection des fichiers d’une archive ZIP à ingérer.<br>Tous les autres fichiers de l’archive sont ignorés. Par exemple, `"*.csv"`. Il est recommandé de l’entourer de guillemets doubles |
|-format               |-f           |string  |Facultatif  |Format des données sources. Doit être dans l'un des [formats pris en charge](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) |
|-ingestionMappingPath |-mappingPath |string  |Facultatif  |Chemin d’accès au fichier de mappage des colonnes d’ingestion (obligatoire pour les formats JSON et Avro). Consultez [mappages de données](kusto/management/mappings.md) |
|-ingestionMappingRef  |-mappingRef  |string  |Facultatif  |Nom d’un mappage de colonnes d’ingestion créé au préalable (obligatoire pour les formats JSON et Avro). Consultez [mappages de données](kusto/management/mappings.md) |
|-creationTimePattern  |             |string  |Facultatif  |Lorsque cette option est définie, elle est utilisée pour extraire la propriété CreationTime du chemin d’accès du fichier ou de l’objet BLOB. Consultez [Utilisation de l’argument CreationTimePattern](#using-creationtimepattern-argument) |
|-ignoreFirstRow       |-ignoreFirst |bool    |Facultatif  |Si cette valeur est définie, le premier enregistrement de chaque fichier/objet BLOB est ignoré (par exemple, si les données sources ont des en-têtes) |
|-tag                  |             |string  |Facultatif  |[Balises](kusto/management/extents-overview.md#extent-tagging) à associer aux données ingérées. Plusieurs occurrences sont autorisées |
|-dontWait             |             |bool    |Facultatif  |Si la valeur est « true », n’attend pas la fin de l’ingestion. Utile lors de la réception de grandes quantités de fichiers/objets BLOB |

### <a name="using-creationtimepattern-argument"></a>Utilisation de l’argument CreationTimePattern

L’argument `-creationTimePattern` sert à extraire la propriété CreationTime du chemin d’accès du fichier ou de l’objet BLOB. Le modèle n’a pas besoin de refléter le chemin d’accès complet de l’élément, mais uniquement la section entourant le timestamp que vous souhaitez utiliser.

Les valeurs d’argument doivent inclure :
* Texte fixe précédant immédiatement l’horodatage, placé entre guillemets simples
* Format d’horodatage, en notation [.NET DateTime standard](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Texte fixe qui suit immédiatement le timestamp. Par exemple, si les noms de blob se terminent par `historicalvalues19840101.parquet` (le timestamp est composé de quatre chiffres pour l’année, deux chiffres pour le mois et deux chiffres pour le jour du mois), la valeur correspondante pour l’argument `-creationTimePattern` est :

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>Arguments de ligne de commande pour les scénarios avancés

|Nom de l’argument         |Nom court   |Type    |Obligatoire |Description                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-compression          |-cr          |double  |Facultatif  |Indicateur de taux de compression. Utile lors de la réception de fichiers ou d’objets BLOB compressés pour aider Azure Data Explorer à évaluer la taille des données brutes. Calculé comme taille d’origine divisée par la taille compressée |
|-limit                |-l           |entier |Facultatif  |Si cette valeur est définie, limite l’ingestion aux N premiers fichiers |
|-listOnly             |-list        |bool    |Facultatif  |Si cette option est définie, affiche uniquement les éléments qui auraient été sélectionnés pour l’ingestion| 
|-ingestTimeout        |             |entier |Facultatif  |Délai d’expiration en minutes pour l’exécution de toutes les opérations de réception. La valeur par défaut est `60`|
|-forceSync            |             |bool    |Facultatif  |Si cette option est définie, force l’ingestion synchrone. La valeur par défaut est `false` |
|-dataBatchSize        |             |entier |Facultatif  |Définit la limite de taille totale (Mo, sans compression) de chaque opération de réception |
|-filesInBatch         |             |entier |Facultatif  |Définit la limite du nombre de fichiers/objets BLOB pour chaque opération d’ingestion |
|-devTracing           |-trace       |string  |Facultatif  |Si cette option est définie, les journaux de diagnostic sont écrits dans un répertoire local (par défaut, `RollingLogs` dans le répertoire actif, modifiable en définissant la valeur du commutateur) |

## <a name="blob-metadata-properties"></a>Propriétés des métadonnées des objets blob
Lorsqu’il est utilisé avec des blobs Azure, LightIngest utilise certaines propriétés de métadonnées blob pour améliorer le processus d’ingestion.

|Propriété de métadonnées                            | Usage                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Si cette valeur est définie, elle est interprétée comme la taille des données non compressées                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Interprété comme un horodatage UTC. Si cette valeur est définie, elle est utilisée pour remplacer l’heure de création dans Kusto. Utile pour les scénarios de renvoi |

## <a name="usage-examples"></a>Exemples d'utilisation

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Réception d’objets BLOB à l’aide d’une clé de compte de stockage ou d’un jeton SAS

* Ingérez 10 objets BLOB dans le compte de stockage spécifié `ACCOUNT`, dans le dossier `DIR`, sous le conteneur `CONT` et correspondant au modèle `*.csv.gz`
* La destination est la base de données `DB`, table `TABLE`, et le mappage d’ingestion `MAPPING` est précréé sur la destination
* L’outil attend que les opérations de réception se terminent
* Notez les différentes options pour spécifier la base de données cible et la clé de compte de stockage par rapport au Jeton SAS

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Réception de tous les objets BLOB dans un conteneur, à l’exclusion des lignes d’en-tête

* Ingérez tous les objets BLOB sous le compte de stockage spécifié `ACCOUNT`, dans le dossier `DIR1/DIR2`, sous le conteneur `CONT` et en correspondant au modèle `*.csv.gz`
* La destination est la base de données `DB`, table `TABLE`, et le mappage d’ingestion `MAPPING` est précréé sur la destination
* Les objets BLOB sources contiennent une ligne d’en-tête. L’outil est donc invité à supprimer le premier enregistrement de chaque objet BLOB
* L’outil publie les données à des fins d’ingestion et n’attend pas la fin des opérations d’ingestion

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Réception de tous les fichiers JSON à partir d’un chemin d’accès

* Recevoir tous les fichiers sous le chemin d’accès `PATH` correspondant au modèle `*.json`
* La destination est la base de données `DB`, table `TABLE`, et le mappage d’ingestion est défini dans le fichier local `MAPPING_FILE_PATH`
* L’outil publie les données à des fins d’ingestion et n’attend pas la fin des opérations d’ingestion

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Ingestion de fichiers et écriture de fichiers de trace de diagnostic

* Recevoir tous les fichiers sous le chemin d’accès `PATH` correspondant au modèle `*.json`
* La destination est la base de données `DB`, table `TABLE`, et le mappage d’ingestion est défini dans le fichier local `MAPPING_FILE_PATH`
* L’outil publie les données à des fins d’ingestion et n’attend pas la fin des opérations d’ingestion
* Les fichiers de trace de diagnostic seront écrits localement sous le dossier `LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Journal des modifications
|Version        |Modifications                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Ajout de l’argument `-zipPattern`</li><li>Ajout de l’argument `-listOnly`</li><li>Le résumé des arguments s’affiche avant le début de l’exécution</li><li>CreationTime est lu à partir des propriétés des métadonnées BLOB ou du nom de fichier ou d’objet BLOB, en fonction de l’argument `-creationTimePattern`</li></ul>|
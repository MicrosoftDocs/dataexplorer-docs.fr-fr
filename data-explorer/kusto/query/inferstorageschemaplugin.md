---
title: plug-in infer_storage_schema-Azure Explorateur de données
description: Cet article décrit infer_storage_schema plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 5c430fd0ca18265c5800165b33bfe14126aee02a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373269"
---
# <a name="infer_storage_schema-plugin"></a>plug-in infer_storage_schema

Ce plug-in déduit le schéma de données externes et le retourne en tant que chaîne de schéma CSL. La chaîne peut être utilisée lors de la [création de tables externes](../management/external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table).

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
  ],
  'DataFormat': 'parquet',
  'FileExtension': '.parquet'
});
evaluate infer_storage_schema(options)
```

**Syntaxe**

`evaluate``infer_storage_schema(` *Options*`)`

**Arguments**

Un argument d' *options* unique est une valeur constante de type `dynamic` qui contient un jeu de propriétés spécifiant les propriétés de la requête :

|Nom                    |Obligatoire|Description|
|------------------------|--------|-----------|
|`StorageContainers`|Oui|Liste des [chaînes de connexion de stockage](../api/connection-strings/storage.md) qui représentent l’URI de préfixe pour les artefacts de données stockées|
|`DataFormat`|Oui|Un des [formats de données](../../ingestion-supported-formats.md)pris en charge.|
|`FileExtension`|Non|Analyser uniquement les fichiers se terminant par cette extension de fichier. Ce n’est pas obligatoire, mais sa spécification peut accélérer le processus (ou éliminer les problèmes de lecture des données).|
|`FileNamePrefix`|Non|Analyser uniquement les fichiers commençant par ce préfixe. Ce n’est pas obligatoire, mais sa spécification peut accélérer le processus|
|`Mode`|Non|Stratégie d’inférence de schéma, l’une des suivantes : `any` , `last` , `all` . Déduire le schéma de données à partir de n’importe quel fichier (premier trouvé), du dernier fichier écrit ou de tous les fichiers, respectivement. La valeur par défaut est `last`.|

**Retourne**

Le `infer_storage_schema` plug-in retourne une table de résultats unique contenant une seule ligne/colonne contenant une chaîne de schéma CSL.

> [!NOTE]
> * La stratégie d’inférence de schéma « tout » est une opération très « coûteuse », car elle implique la lecture de *tous les* artefacts trouvés et la fusion de leur schéma.
> * Certains types retournés ne sont peut-être pas des types réels en raison d’une mauvaise estimation de type (ou, suite à un processus de fusion de schéma). C’est pourquoi vous devez examiner le résultat avec soin avant de créer une table externe.

**Exemple**

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents/2015;secretKey'
  ],
  'FileExtension': '.parquet',
  'FileNamePrefix': 'part-',
  'DataFormat': 'parquet'
});
evaluate infer_storage_schema(options)
```

*Résultat*

|CslSchema|
|---|
|app_id : String, user_id : long, event_time : DateTime, Country : String, ville : String, device_type : String, device_vendor : String, ad_network : String, Campaign : String, site_id : String, event_type : String, event_name : String, organique : String, days_from_install : int, revenue : Real|
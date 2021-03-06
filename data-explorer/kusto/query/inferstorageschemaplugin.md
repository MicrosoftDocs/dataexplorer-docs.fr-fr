---
title: plug-in infer_storage_schema-Azure Explorateur de données
description: Cet article décrit infer_storage_schema plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 895f12799f4c44346af2b6b9be43bf98b7cf870e
ms.sourcegitcommit: e820a59191d2ca4394e233d51df7a0584fa4494d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94446206"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema, plug-in

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

## <a name="syntax"></a>Syntaxe

`evaluate` `infer_storage_schema(` *Options* `)`

## <a name="arguments"></a>Arguments

Un argument d' *options* unique est une valeur constante de type `dynamic` qui contient un jeu de propriétés spécifiant les propriétés de la requête :

|Nom                    |Obligatoire|Description|
|------------------------|--------|-----------|
|`StorageContainers`|Oui|Liste des [chaînes de connexion de stockage](../api/connection-strings/storage.md) qui représentent l’URI de préfixe pour les artefacts de données stockées|
|`DataFormat`|Oui|Un des [formats de données](../../ingestion-supported-formats.md)pris en charge.|
|`FileExtension`|Non|Analyser uniquement les fichiers se terminant par cette extension de fichier. Ce n’est pas obligatoire, mais sa spécification peut accélérer le processus (ou éliminer les problèmes de lecture des données).|
|`FileNamePrefix`|Non|Analyser uniquement les fichiers commençant par ce préfixe. Ce n’est pas obligatoire, mais sa spécification peut accélérer le processus|
|`Mode`|Non|Stratégie d’inférence de schéma, l’une des suivantes : `any` , `last` , `all` . Déduire le schéma de données à partir de n’importe quel fichier (premier trouvé), du dernier fichier écrit ou de tous les fichiers, respectivement. La valeur par défaut est `last`.|

## <a name="returns"></a>Retours

Le `infer_storage_schema` plug-in retourne une table de résultats unique contenant une seule ligne/colonne contenant une chaîne de schéma CSL.

> [!NOTE]
> * Les clés secrètes de l’URI du conteneur de stockage doivent disposer des autorisations pour la *liste* , en plus de la *lecture*.
> * La stratégie d’inférence de schéma « tout » est une opération très « coûteuse », car elle implique la lecture de *tous les* artefacts trouvés et la fusion de leur schéma.
> * Certains types retournés ne sont peut-être pas des types réels en raison d’une mauvaise estimation de type (ou, suite à un processus de fusion de schéma). C’est pourquoi vous devez examiner le résultat avec soin avant de créer une table externe.

## <a name="example"></a>Exemple

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents;secretKey'
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

Utilisez le schéma retourné dans la définition de la table externe :

```kusto
.create external table MobileEvents(
    app_id:string, user_id:long, event_time:datetime, country:string, city:string, device_type:string, device_vendor:string, ad_network:string, campaign:string, site_id:string, event_type:string, event_name:string, organic:string, days_from_install:int, revenue:real
)
kind=blob
partition by (dt:datetime = bin(event_time, 1d), app:string = app_id)
pathformat = ('app=' app '/dt=' datetime_pattern('yyyyMMdd', dt))
dataformat = parquet
(
    h@'https://storageaccount.blob.core.windows.net/MovileEvents;secretKey'
)
```
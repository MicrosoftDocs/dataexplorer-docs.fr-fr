---
title: infer_storage_schema plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit infer_storage_schema plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 6c4543a3b029017067867bb70d913509941c332e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513904"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema plugin

Ce plug-in déduit schéma de données externes, et le renvoie comme chaîne de schéma CSL qui peut être utilisé lors [de la création de tables externes](../management/externaltables.md#create-or-alter-external-table).

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

`evaluate` `infer_storage_schema(` *Options* `)`

**Arguments**

Un argument *Unique Options* est `dynamic` une valeur constante de type qui détient un sac de propriété spécifiant les propriétés de la demande:

|Nom                    |Obligatoire|Description|
|------------------------|--------|-----------|
|`StorageContainers`|Oui|Liste des chaînes de connexion de [stockage](../api/connection-strings/storage.md) qui représentent le préfixe URI pour les artefacts de données stockés|
|`DataFormat`|Oui|L’un des [formats](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)de données pris en charge .|
|`FileExtension`|Non|Seuls les fichiers d’analyse se terminant par cette extension de fichier. Ce n’est pas nécessaire, mais le spécifier peut accélérer le processus (ou éliminer les problèmes de lecture de données)|
|`FileNamePrefix`|Non|N’analysez que les fichiers en commençant par ce préfixe. Ce n’est pas nécessaire, mais le spécifier peut accélérer le processus|
|`Mode`|Non|Schema stratégie d’inférence, `last` `all`l’un de: `any`, , . Inférer le schéma de données de n’importe quel fichier (premier trouvé), du dernier fichier écrit ou de tous les fichiers respectivement. La valeur par défaut est `last`.|

**Retourne**

Le `infer_storage_schema` plugin renvoie une seule table de résultat contenant une seule ligne/colonne tenant la chaîne de schémaS CSL.

> [!NOTE]
> * Schema inférence stratégie «tout» est très «coûteux» opération car il implique la lecture de *tous les* artefacts trouvés et la fusion de leur schéma.
> * Certains types retournés peuvent ne pas être les vrais à la suite de deviner le mauvais type (ou, à la suite du processus de fusion de schémas). C’est pourquoi il est conseillé d’examiner attentivement le résultat avant de créer une table externe.

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
|app_id:string, user_id:long, event_time:datetime, country:string, city:string, device_type:string, device_vendor:string, ad_network:string, campaign:string, site_id:string, event_type:string, event_name:string, organic:string, days_from_install:int, revenue:real|
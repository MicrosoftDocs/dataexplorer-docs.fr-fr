---
title: Données d’exportation vers le stockage - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les données d’exportation au stockage dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 12800955d1680a280aa4772db86d8b71e44e7089
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521571"
---
# <a name="export-data-to-storage"></a>Données d’exportation au stockage

Exécute une requête et écrit le premier résultat réglé sur un stockage externe, spécifié par une [chaîne de connexion de stockage](../../api/connection-strings/storage.md).

**Syntaxe**

`.export`[`async`]`compressed` `to` [ ] *OutputDataFormat* 
 `(` *StorageConnectionString* [`,` ...] `)` `with` [ `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *Requête*

**Arguments**

* `async`: Si spécifié, indique que la commande fonctionne en mode asynchrone.
  Voir ci-dessous pour plus de détails sur le comportement dans ce mode.

* `compressed`: Si spécifié, les artefacts de `.gz` stockage de sortie sont compressés sous forme de fichiers. Voir `compressionType` pour comprimer les fichiers parquet comme accrocheur. 

* *OutputDataFormat*: Indique le format de données des artefacts de stockage écrits par la commande. Les valeurs `csv`soutenues `json`sont `parquet`: , `tsv`, et .

* *StorageConnectionString*: Specifie une ou plusieurs [chaînes de connexion de stockage](../../api/connection-strings/storage.md) qui indiquent à quel stockage écrire les données. (Plus d’une chaîne de connexion de stockage peut être spécifiée pour les écrits évolutifs.) Chaque chaîne de connexion doit indiquer les informations d’identification à utiliser lors de l’écriture au stockage.
  Par exemple, lorsque vous écrivez à Azure Blob Storage, les informations d’identification peuvent être la clé du compte de stockage, ou une clé d’accès partagée (SAS) avec les autorisations de lire, d’écrire et de répertorier les blobs.

> [!NOTE]
> Il est fortement recommandé d’exporter des données vers le stockage qui sont co-localisés dans la même région que le cluster Kusto lui-même. Cela comprend les données qui sont exportées afin qu’elles puissent être transférées à un autre service cloud dans d’autres régions. Les écrits doivent être faits localement, tandis que les lectures peuvent se produire à distance.

* *PropertyName*/*PropertyValue*: Propriétés d’exportation zéro ou plus facultatives :

|Propriété        |Type    |Description                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |La limite de taille dans les octets d’un seul artefact de stockage en cours d’écriture (avant la compression). La portée autorisée est de 100 Mo (par défaut) à 1 Go.|
|`includeHeaders`|`string`|`csv` / Pour `tsv` la sortie, contrôle la génération d’en-têtes de colonne. Peut être `none` l’un des (par `all` défaut; pas de lignes d’en-tête émises), (émettre une ligne d’en-tête dans chaque artefact de stockage), ou `firstFile` (émettre une ligne d’en-tête dans le premier artefact de stockage seulement).|
|`fileExtension` |`string`|Indique la partie « extension » de `.csv` l’artefact de stockage (par exemple, ou `.tsv`). Si la compression `.gz` est utilisée, sera annexée ainsi.|
|`namePrefix`    |`string`|Indique un préfixe à ajouter à chaque nom d’artefact de stockage généré. Un préfixe aléatoire sera utilisé s’il n’est pas spécifié.       |
|`encoding`      |`string`|Indique comment coder le `UTF8NoBOM` texte : `UTF8BOM`(par défaut) ou . |
|`compressionType`|`string`|Indique le type de compression à utiliser. Les valeurs possibles sont `gzip` ou `snappy`. La valeur par défaut est `gzip`. `snappy`peut (optionnellement) `parquet` être utilisé pour le format. |
|`distribution`   |`string`  |Indice de`single` `per_node`distribution `per_shard`( , , ). Si la `single`valeur est égale, un seul thread écrira au stockage. Dans le cas contraire, l’exportation écrira de tous les nœuds exécutant la requête en parallèle. Voir [évaluer l’opérateur plugin](../../query/evaluateoperator.md). La valeur par défaut est `per_shard`.
|`distributed`   |`bool`  |Désactiver/activer l’exportation distribuée. Le réglage du `single` faux équivaut à un indice de distribution. La valeur par défaut est true.
|`persistDetails`|`bool`  |Indique que la commande doit `async` persister ses résultats (voir drapeau). Par défaut `true` dans les exécutions async, mais peut être désactivé si l’appelant n’a pas besoin des résultats). Par défaut `false` dans les exécutions synchrones, mais peut être activé dans ceux-ci ainsi. |
|`parquetRowGroupSize`|`int`  |Pertinent uniquement lorsque le format de données est parquet. Contrôle la taille du groupe de ligne dans les fichiers exportés. La taille du groupe de rangée par défaut est de 100000 enregistrements.|

**Résultats**

Les commandes renvoie une table qui décrit les artefacts de stockage générés.
Chaque enregistrement décrit un seul artefact et comprend le chemin de stockage de l’artefact et le nombre d’enregistrements de données qu’il détient.

|Path|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**Mode asynchrone**

Si `async` le drapeau est spécifié, la commande s’exécute en mode asynchrone.
Dans ce mode, la commande revient immédiatement avec une pièce d’identité d’opération, et l’exportation de données se poursuit en arrière-plan jusqu’à l’achèvement. L’ID d’opération retourné par la commande peut être utilisé pour suivre ses progrès et, en fin de compte, ses résultats via les commandes suivantes :

* [.afficher les opérations](../operations.md#show-operations): Suivre les progrès.
* [.afficher les détails de l’opération](../operations.md#show-operation-details): Obtenez les résultats d’achèvement.

Par exemple, après un achèvement réussi, vous pouvez récupérer les résultats en utilisant :

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**Exemples** 

Dans cet exemple, Kusto exécute la requête, puis exporte le premier enregistrement produit par la requête à un ou plusieurs blobs CSV comprimés.
Les étiquettes de nom de colonne sont ajoutées comme première rangée pour chaque blob.

```kusto 
.export
  async compressed
  to csv (
    h@"https://storage1.blob.core.windows.net/containerName;secretKey",
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey"
  ) with (
    sizeLimit=100000,
    namePrefix=export,
    includeHeaders=all,
    encoding =UTF8NoBOM
  )
  <| myLogs | where id == "moshe" | limit 10000
```

**Problèmes connus**

*Erreurs de stockage pendant la commande d’exportation*

Par défaut, la commande d’exportation est distribuée de telle sorte que toutes les [étendues](../extents-overview.md) qui contiennent des données à exporter écrivent au stockage simultanément. Sur les grandes exportations, lorsque le nombre de ces étendues est élevé, cela peut entraîner une charge élevée sur le stockage qui entraîne des limitations de stockage, ou des erreurs de stockage transitoires. Dans de tels cas, il est recommandé d’essayer d’augmenter le nombre de comptes de stockage fournis à la commande d’exportation `per_node` (la charge sera répartie entre les comptes) et/ou de réduire la concordance en définissant l’indice de distribution (voir les propriétés de commande). Une distribution entièrement invalidante est également possible, mais cela peut avoir un impact significatif sur les performances de commande.
 
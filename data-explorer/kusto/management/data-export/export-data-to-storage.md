---
title: Exporter des données vers le stockage-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’exportation de données vers le stockage dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 6b76f7a3ce61a0530d885de29c1a85d170431bb9
ms.sourcegitcommit: 4507466bdcc7dd07e6e2a68c0707b6226adc25af
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87106431"
---
# <a name="export-data-to-storage"></a>Exporter des données dans le stockage

Exécute une requête et écrit le premier jeu de résultats dans un stockage externe, spécifié par une [chaîne de connexion de stockage](../../api/connection-strings/storage.md).

**Syntaxe**

`.export`[ `async` ] [ `compressed` ] `to` *OutputDataFormat* 
 `(` *StorageConnectionString* [ `,` ...] `)` [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *Requête*

**Arguments**

* `async`: S’il est spécifié, indique que la commande s’exécute en mode asynchrone.
  Pour plus d’informations sur le comportement dans ce mode, voir ci-dessous.

* `compressed`: Si spécifié, les artefacts de stockage de sortie sont compressés sous forme de `.gz` fichiers. `compressionType`Pour compresser les fichiers parquet en tant qu’alignement, consultez. 

* *OutputDataFormat*: indique le format des données des artefacts de stockage écrits par la commande. Les valeurs prises en charge sont les suivantes : `csv` , `tsv` , `json` et `parquet` .

* *StorageConnectionString*: spécifie une ou plusieurs [chaînes de connexion de stockage](../../api/connection-strings/storage.md) qui indiquent le stockage dans lequel les données doivent être écrites. (Il est possible de spécifier plusieurs chaînes de connexion de stockage pour les écritures évolutives.) Chaque chaîne de connexion doit indiquer les informations d’identification à utiliser lors de l’écriture dans le stockage.
  Par exemple, lors de l’écriture dans le stockage d’objets BLOB Azure, les informations d’identification peuvent être la clé du compte de stockage, ou une clé d’accès partagé (SAP) avec les autorisations de lecture, d’écriture et de liste des objets BLOB.

> [!NOTE]
> Il est vivement recommandé d’exporter les données vers le stockage qui est colocalisé dans la même région que le cluster Kusto. Cela comprend les données exportées afin qu’elles puissent être transférées vers un autre service Cloud dans d’autres régions. Les écritures doivent être effectuées localement, tandis que les lectures peuvent se produire à distance.

* *PropertyName* / *PropertyValue*: zéro, une ou plusieurs propriétés d’exportation facultatives :

|Propriété        |Type    |Description                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |Limite de taille en octets d’un seul artefact de stockage écrit (avant la compression). La plage autorisée est de 100 Mo (valeur par défaut) à 1 Go.|
|`includeHeaders`|`string`|Pour `csv` / `tsv` la sortie, contrôle la génération des en-têtes de colonnes. Il peut s’agir de l’une des `none` valeurs (par défaut ; aucune ligne d’en-tête émise), `all` (émettre une ligne d’en-tête dans chaque artefact de stockage) ou `firstFile` (émettre une ligne d’en-tête dans le premier artefact de stockage uniquement).|
|`fileExtension` |`string`|Indique la partie « extension » de l’artefact de stockage (par exemple, `.csv` ou `.tsv` ). Si la compression est utilisée, est `.gz` également ajouté.|
|`namePrefix`    |`string`|Indique un préfixe à ajouter à chaque nom d’artefact de stockage généré. Un préfixe aléatoire est utilisé s’il n’est pas spécifié.       |
|`encoding`      |`string`|Indique comment encoder le texte : `UTF8NoBOM` (valeur par défaut) ou `UTF8BOM` . |
|`compressionType`|`string`|Indique le type de compression à utiliser. Les valeurs possibles sont `gzip` ou `snappy`. La valeur par défaut est `gzip`. `snappy`peut (éventuellement) être utilisé pour le `parquet` format. |
|`distribution`   |`string`  |Indicateur de distribution ( `single` , `per_node` , `per_shard` ). Si la valeur est égale `single` à, un thread unique écrit dans le stockage. Dans le cas contraire, l’exportation écrira à partir de tous les nœuds exécutant la requête en parallèle. Consultez [évaluer un opérateur de plug-in](../../query/evaluateoperator.md). La valeur par défaut est `per_shard`.
|`distributed`   |`bool`  |Désactiver/activer l’exportation distribuée. La définition de la valeur false équivaut à l' `single` indicateur de distribution. La valeur par défaut est True.
|`persistDetails`|`bool`  |Indique que la commande doit conserver ses résultats (consultez `async` flag). La valeur par défaut `true` est dans les exécutions Async, mais peut être désactivée si l’appelant n’a pas besoin des résultats. La valeur par défaut est `false` dans les exécutions synchrones, mais peut également être activée dans celles-ci. |
|`parquetRowGroupSize`|`int`  |S’applique uniquement lorsque le format de données est parquet. Contrôle la taille du groupe de lignes dans les fichiers exportés. La taille du groupe de lignes par défaut est de 100000 enregistrements.|

**Résultats**

La commande retourne une table qui décrit les artefacts de stockage générés.
Chaque enregistrement décrit un artefact unique et inclut le chemin d’accès de stockage à l’artefact et le nombre d’enregistrements de données qu’il contient.

|Chemin|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**Mode asynchrone**

Si l' `async` indicateur est spécifié, la commande s’exécute en mode asynchrone.
Dans ce mode, la commande est retournée immédiatement avec un ID d’opération, et l’exportation des données se poursuit en arrière-plan jusqu’à la fin. L’ID d’opération retourné par la commande peut être utilisé pour suivre sa progression et enfin ses résultats à l’aide des commandes suivantes :

* [. Show Operations](../operations.md#show-operations): suivre la progression.
* [. afficher les détails](../operations.md#show-operation-details)de l’opération : obtenir les résultats de la saisie semi-automatique.

Par exemple, une fois l’opération terminée, vous pouvez récupérer les résultats à l’aide de :

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**Exemples** 

Dans cet exemple, Kusto exécute la requête, puis exporte le premier jeu d’enregistrements produit par la requête vers un ou plusieurs objets BLOB CSV compressés.
Les étiquettes de nom de colonne sont ajoutées en tant que première ligne pour chaque objet BLOB.

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

#### <a name="known-issues"></a>Problèmes connus

*Erreurs de stockage pendant l’exportation, commande*

Par défaut, la commande d’exportation est distribuée de telle sorte que toutes les [étendues](../extents-overview.md) contiennent des données à exporter simultanément dans le stockage. Sur les exportations volumineuses, lorsque le nombre d’étendues est élevé, cela peut entraîner une charge élevée sur le stockage qui entraîne une limitation du stockage, ou des erreurs de stockage temporaire. Dans ce cas, il est recommandé d’essayer d’améliorer le nombre de comptes de stockage fournis à la commande d’exportation (la charge sera distribuée entre les comptes) et/ou de réduire la concurrence en définissant l’indicateur de distribution sur `per_node` (voir Propriétés de la commande). La désactivation complète de la distribution est également possible, mais cela peut avoir un impact significatif sur les performances de la commande.
 
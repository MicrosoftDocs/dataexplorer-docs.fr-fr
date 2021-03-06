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
ms.openlocfilehash: fd0ac46aa0e2cf73cf0ee5a51359cd346bf1beda
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321554"
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
|`compressionType`|`string`|Indique le type de compression à utiliser. Les valeurs possibles sont `gzip` ou `snappy`. La valeur par défaut est `gzip`. `snappy` peut (éventuellement) être utilisé pour le `parquet` format. |
|`distribution`   |`string`  |Indicateur de distribution ( `single` , `per_node` , `per_shard` ). Si la valeur est égale `single` à, un thread unique écrit dans le stockage. Dans le cas contraire, l’exportation écrira à partir de tous les nœuds exécutant la requête en parallèle. Consultez [évaluer un opérateur de plug-in](../../query/evaluateoperator.md). La valeur par défaut est `per_shard`.
|`distributed`   |`bool`  |Désactiver/activer l’exportation distribuée. La définition de la valeur false équivaut à l' `single` indicateur de distribution. La valeur par défaut est true.
|`persistDetails`|`bool`  |Indique que la commande doit conserver ses résultats (consultez `async` flag). La valeur par défaut `true` est dans les exécutions Async, mais peut être désactivée si l’appelant n’a pas besoin des résultats. La valeur par défaut est `false` dans les exécutions synchrones, mais peut également être activée dans celles-ci. |
|`parquetRowGroupSize`|`int`  |S’applique uniquement lorsque le format de données est parquet. Contrôle la taille du groupe de lignes dans les fichiers exportés. La taille du groupe de lignes par défaut est de 100000 enregistrements.|

**Résultats**

La commande retourne une table qui décrit les artefacts de stockage générés.
Chaque enregistrement décrit un artefact unique et inclut le chemin d’accès de stockage à l’artefact et le nombre d’enregistrements de données qu’il contient.

|Chemin d’accès|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**Mode asynchrone**

Si l' `async` indicateur est spécifié, la commande s’exécute en mode asynchrone.
Dans ce mode, la commande est retournée immédiatement avec un ID d’opération, et l’exportation des données se poursuit en arrière-plan jusqu’à la fin. L’ID d’opération retourné par la commande peut être utilisé pour suivre sa progression et enfin ses résultats à l’aide des commandes suivantes :

* [`.show operations`](../operations.md#show-operations): Suivre la progression.
* [`.show operation details`](../operations.md#show-operation-details): Obtenir les résultats de la saisie semi-automatique.

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

## <a name="failures-during-export-commands"></a>Échecs lors des commandes d’exportation

Les commandes d’exportation peuvent échouer momentanément au cours de l’exécution. L' [exportation continue](continuous-data-export.md) réessaiera automatiquement la commande. Les commandes d’exportation standard ([Exporter vers le stockage](export-data-to-storage.md), [Exporter vers une table externe](export-data-to-an-external-table.md)) n’effectuent pas de nouvelles tentatives.

*  Lorsque la commande d’exportation échoue, les artefacts déjà écrits dans le stockage ne sont pas supprimés. Ces artefacts sont conservés dans le stockage. Si la commande échoue, supposez que l’exportation est incomplète, même si certains artefacts ont été écrits. 
* La meilleure façon d’effectuer le suivi de l’exécution de la commande et des artefacts exportés en cas de réussite de l’opération consiste à utiliser les [`.show operations`](../operations.md#show-operations) [`.show operation details`](../operations.md#show-operation-details) commandes et.

### <a name="storage-failures"></a>Échecs de stockage

Par défaut, les commandes d’exportation sont distribuées de telle sorte qu’il peut y avoir de nombreuses écritures simultanées dans le stockage. Le niveau de distribution dépend du type de commande d’exportation :
* La distribution par défaut de la `.export` commande normale est `per_shard` , ce qui signifie que toutes les [étendues](../extents-overview.md) contiennent des données à exporter simultanément dans le stockage. 
* La distribution par défaut des commandes [Exporter vers une table externe](export-data-to-an-external-table.md) est `per_node` , ce qui signifie que la concurrence est le nombre de nœuds dans le cluster.

Lorsque le nombre d’étendues/de nœuds est élevé, cela peut entraîner une charge élevée sur le stockage qui entraîne une limitation du stockage, ou des erreurs de stockage temporaire. Les suggestions suivantes peuvent résoudre ces erreurs (par ordre de priorité) :

* Augmentez le nombre de comptes de stockage fournis à la commande d’exportation ou à la définition de la [table externe](../external-tables-azurestorage-azuredatalake.md) (la charge sera distribuée uniformément entre les comptes).
* Réduisez la concurrence en affectant à l’indicateur de distribution la valeur `per_node` (consultez Propriétés de la commande).
* Réduisez la concurrence du nombre de nœuds exportés en affectant à la [propriété de demande du client](../../api/netfx/request-properties.md) `query_fanout_nodes_percent` la concurrence souhaitée (pourcentage de nœuds). La propriété peut être définie dans le cadre de la requête d’exportation. Par exemple, la commande suivante limite le nombre de nœuds écrivant simultanément au stockage sur 50% des nœuds de cluster :

    ```kusto
    .export async  to csv
        ( h@"https://storage1.blob.core.windows.net/containerName;secretKey" ) 
        with
        (
            distribuion="per_node"
        ) 
        <| 
        set query_fanout_nodes_percent = 50;
        ExportQuery
    ```

* En cas d’exportation vers une table externe partitionnée, la définition des `spread` / `concurrency` Propriétés peut réduire la concurrence (voir les détails dans les [Propriétés](export-data-to-an-external-table.md#syntax)de la commande.
* Si aucun des éléments ci-dessus ne fonctionne, est également possible de désactiver complètement la distribution en affectant `distributed` à la propriété la valeur false, mais cela n’est pas recommandé, car cela peut avoir un impact significatif sur les performances de la commande.

---
title: Créer et modifier des tables externes dans Azure Storage ou Azure Data Lake-Azure Explorateur de données
description: Cet article explique comment créer et modifier des tables externes dans le stockage Azure ou Azure Data Lake
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1d0625c949fe563084caeec936e3433c9ee70f5e
ms.sourcegitcommit: ef3d919dee27c030842abf7c45c9e82e6e8350ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630107"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>Créer et modifier des tables externes dans Stockage Azure ou Azure Data Lake

La commande suivante décrit comment créer une table externe située dans le stockage d’objets BLOB Azure, Azure Data Lake Store Gen1 ou Azure Data Lake Store Gen2. 

Pour obtenir une présentation de la fonctionnalité de tables de stockage Azure externes, consultez [interroger des données dans Azure Data Lake à l’aide d’Azure Explorateur de données](../../data-lake-query-data.md).

## <a name="create-or-alter-external-table"></a>. Create ou. Alter External table

**Syntaxe**

( `.create`  |  `.alter`  |  `.create-or-alter` ) `external` `table` *[TableName](#table-name)* `(` *[Schéma](#schema)* TableName`)`  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` `(` *[Partitions](#partitions)* `)` [ `pathformat` `=` `(` *[PathFormat](#path-format)* `)` ]]  
`dataformat``=` *[Format](#format)*  
`(`*[StorageConnectionString](#connection-string)* [ `,` ...]`)`   
[ `with` `(` *[PropertyName](#properties)* `=` *[Valeur](#properties)* NomPropriété `,` ... `)` ]  

Crée ou modifie une nouvelle table externe dans la base de données dans laquelle la commande est exécutée.

> [!NOTE]
> * Si la table existe, la `.create` commande échoue avec une erreur. Utilisez `.create-or-alter` ou `.alter` pour modifier des tables existantes.
> * La modification du schéma, du format ou de la définition de partition d’une table d’objets BLOB externe n’est pas prise en charge. 
> * L’opération requiert l' [autorisation utilisateur de base de données](../management/access-control/role-based-authorization.md) pour `.create` et l’autorisation d’administrateur de [table](../management/access-control/role-based-authorization.md) pour `.alter` . 

**Paramètres**

<a name="table-name"></a>
*TableName*

Nom de la table externe qui respecte les règles de [noms d’entité](../query/schema-entities/entity-names.md) .
Une table externe ne peut pas avoir le même nom qu’une table normale dans la même base de données.

<a name="schema"></a>
*Schéma*

Le schéma de données externes est décrit en utilisant le format suivant :

&nbsp;&nbsp;*ColumnName* `:` *ColumnType* [ `,` *ColumnName* `:` *ColumnType* ...]

où *ColumnName* respecte les règles d' [affectation de noms d’entités](../query/schema-entities/entity-names.md) et *ColumnType* est l’un des [types de données pris en charge](../query/scalar-data-types/index.md).

> [!TIP]
> Si le schéma de données externes est inconnu, utilisez le plug-in [déduire le \_ \_ schéma de stockage](../query/inferstorageschemaplugin.md) , qui permet de déduire le schéma en fonction du contenu du fichier externe.

<a name="partitions"></a>
*Partitions*

Liste séparée par des virgules des colonnes par lesquelles une table externe est partitionnée. La colonne de partition peut exister dans le fichier de données lui-même ou dans la partie sa du chemin d’accès du fichier (en savoir plus sur les [colonnes virtuelles](#virtual-columns)).

La liste partitions est toute combinaison de colonnes de partition, spécifiée à l’aide de l’une des formes suivantes :

* Partition représentant une [colonne virtuelle](#virtual-columns).

  *PartitionName* `:` (`datetime` | `string`)

* Partition, en fonction d’une valeur de colonne de chaîne.

  *PartitionName* `:` `string` `=` *ColumnName*

* Partition, en fonction d’un [hachage](../query/hashfunction.md)de valeur de colonne de chaîne, *numéro* modulo.

  *PartitionName* `:` `long` `=` `hash` `(` *ColumnName* `,` *Nombre* de ColumnName`)`

* Partition, en fonction de la valeur tronquée d’une colonne DateTime. Consultez la documentation sur [STARTOFYEAR](../query/startofyearfunction.md), [StartOfMonth](../query/startofmonthfunction.md), [startOfWeek](../query/startofweekfunction.md), [startofday](../query/startofdayfunction.md) ou [bin](../query/binfunction.md) functions.

  *PartitionName* `:` `datetime` `=` ( `startofyear` \| `startofmonth` \| `startofweek` \| `startofday` ) `(` *ColumnName*`)`  
  *PartitionName* `:` `datetime` `=` `bin` `(` *ColumnName* `,` *Intervalle* de ColumnName`)`

Pour vérifier l’exactitude de la définition de partitionnement, utilisez la propriété lors de la `sampleUris` création d’une table externe.

<a name="path-format"></a>
*PathFormat*

Format de chemin d’accès au fichier d’URI de données externes, qui peut être spécifié en plus des partitions. Le format du chemin d’accès est une séquence d’éléments de partition et de séparateurs de texte :

&nbsp;&nbsp;[ *StringSeparator* ] *Partition* [ *StringSeparator* ] [ *partition* [ *StringSeparator* ]...]  

où *partition* fait référence à une partition déclarée dans la `partition` `by` clause, et *StringSeparator* est un texte placé entre guillemets. Les éléments de partition consécutifs doivent être séparés à l’aide de *StringSeparator* .

Le préfixe du chemin d’accès au fichier d’origine peut être construit à l’aide d’éléments de partition rendus sous forme de chaînes et séparés par des séparateurs de texte Pour spécifier le format utilisé pour le rendu d’une valeur de partition DateTime, vous pouvez utiliser la macro suivante :

&nbsp;&nbsp;`datetime_pattern``(` *DateTimeFormat* `,` *PartitionName* DateTimeFormat`)`  

où *DateTimeFormat* adhère à la spécification de format .net, avec une extension qui permet de placer les spécificateurs de format entre accolades. Par exemple, les deux formats suivants sont équivalents :

&nbsp;&nbsp;`'year='yyyy'/month='MM` les `year={yyyy}/month={MM}`

Par défaut, les valeurs DateTime sont rendues à l’aide des formats suivants :

| Fonction de partition    | Format par défaut |
|-----------------------|----------------|
| `startofyear`         | `yyyy`         |
| `startofmonth`        | `yyyy/MM`      |
| `startofweek`         | `yyyy/MM/dd`   |
| `startofday`          | `yyyy/MM/dd`   |
| `bin(`*Chronique*`, 1d)` | `yyyy/MM/dd`   |
| `bin(`*Chronique*`, 1h)` | `yyyy/MM/dd/HH` |
| `bin(`*Chronique*`, 1m)` | `yyyy/MM/dd/HH/mm` |

Si *PathFormat* est omis de la définition de la table externe, il est supposé que toutes les partitions, exactement dans le même ordre que celui dans lequel elles sont définies, sont séparées par un `/` séparateur. Les partitions sont rendues à l’aide de leur présentation de chaîne par défaut.

Pour vérifier que la définition de format de chemin d’accès est correcte, utilisez la propriété lors de la `sampleUris` création d’une table externe.

<a name="format"></a>
*Format*

Format de données, l’un des [formats](../../ingestion-supported-formats.md)d’ingestion.

> [!NOTE]
> L’utilisation de la table externe pour le [scénario d’exportation](data-export/export-data-to-an-external-table.md) se limite aux formats suivants : `CSV` , `TSV` `JSON` et `Parquet` .

<a name="connection-string"></a>
*StorageConnectionString*

Un ou plusieurs chemins d’accès aux conteneurs d’objets BLOB de stockage d’objets BLOB Azure ou Azure Data Lake Store systèmes de fichiers (répertoires virtuels ou dossiers), y compris les informations d’identification.
Pour plus d’informations, consultez [chaînes de connexion de stockage](../api/connection-strings/storage.md) .

> [!TIP]
> Fournissez plus d’un compte de stockage unique pour éviter la limitation du stockage lors de l' [exportation](data-export/export-data-to-an-external-table.md) de grandes quantités de données vers la table externe. L’exportation répartit les écritures entre tous les comptes fournis. 

<a name="properties"></a>
*Propriétés facultatives*

| Propriété         | Type     | Description       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Dossier de la table                                                                     |
| `docString`      | `string` | Chaîne documentant la table                                                       |
| `compressed`     | `bool`   | Si cette valeur est définie, indique si les fichiers sont compressés en tant que `.gz` fichiers (utilisés dans le [scénario d’exportation](data-export/export-data-to-an-external-table.md) uniquement). |
| `includeHeaders` | `string` | Pour les formats de texte délimité (CSV, TSV,...), indique si les fichiers contiennent un en-tête. Les valeurs possibles sont : `All` (tous les fichiers contiennent un en-tête), `FirstFile` (le premier fichier d’un dossier contient un en-tête), `None` (aucun fichier ne contient un en-tête). |
| `namePrefix`     | `string` | Si cette valeur est définie, indique le préfixe des fichiers. Lors des opérations d’écriture, tous les fichiers sont écrits avec ce préfixe. Sur les opérations de lecture, seuls les fichiers avec ce préfixe sont lus. |
| `fileExtension`  | `string` | S’il est défini, indique les extensions de fichier des fichiers. Lors de l’écriture, les noms de fichiers se terminent par ce suffixe. Lors de la lecture, seuls les fichiers avec cette extension de fichier seront lus.           |
| `encoding`       | `string` | Indique comment le texte est encodé : `UTF8NoBOM` (valeur par défaut) ou `UTF8BOM` .             |
| `sampleUris`     | `bool`   | Si cette option est définie, le résultat de la commande fournit plusieurs exemples d’URI de fichiers de données externes tels qu’ils sont attendus par la définition de la table externe (les exemples sont retournés dans la deuxième table de résultats). Cette option permet de vérifier si les *[partitions](#partitions)* et les paramètres *[PathFormat](#path-format)* sont définis correctement. |
| `validateNotEmpty` | `bool`   | Si cette valeur est définie, les chaînes de connexion sont validées pour qu’elles contiennent du contenu. La commande échoue si l’emplacement d’URI spécifié n’existe pas ou s’il n’y a pas d’autorisations suffisantes pour y accéder. |

> [!TIP]
> Pour en savoir plus sur le rôle `namePrefix` et les `fileExtension` Propriétés lus dans le filtrage des fichiers de données pendant la requête, consultez la section [logique de filtrage de fichiers](#file-filtering) .
 
<a name="examples"></a>
**Illustre** 

Table externe non partitionnée. Les fichiers de données sont censés être placés directement sous le ou les conteneurs définis :

```kusto
.create external table ExternalTable (x:long, s:string)  
kind=blob 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

Table externe partitionnée par date. Les fichiers de date sont censés être placés dans des répertoires de format DateTime par défaut `yyyy/MM/dd` :

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by (Date:datetime = bin(Timestamp, 1d)) 
dataformat=csv 
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
```

Une table externe partitionnée par mois, avec un format de répertoire `year=yyyy/month=MM` :

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=blob 
partition by (Month:datetime = startofmonth(Timestamp)) 
pathformat = (datetime_pattern("'year='yyyy'/month='MM", Month)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

Une table externe partitionnée d’abord par nom de client, puis par date. La structure de répertoire attendue est, par exemple `customer_name=Softworks/2019/02/01` :

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerNamePart:string = CustomerName, Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_name=" CustomerNamePart "/" Date)
dataformat=csv 
(  
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
)
```

Une table externe partitionnée d’abord par le hachage du nom de client (modulo dix), puis par date. La structure de répertoire attendue est, par exemple, `customer_id=5/dt=20190201` . Les noms de fichiers de données se terminent par l' `.txt` extension :

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerId:long = hash(CustomerName, 10), Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_id=" CustomerId "/dt=" datetime_pattern("yyyyMMdd", Date)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with (fileExtension = ".txt")
```

**Exemple de sortie**

|TableName|TableType|Dossier|DocString|Propriétés|ConnectionStrings|Partitions|PathFormat|
|---------|---------|------|---------|----------|-----------------|----------|----------|
|ExternalTable|Objet blob|ExternalTables|Docs|{"Format" : "CSV", "Compressed" : false, "CompressionType" : null, "FileExtension" : null, "IncludeHeaders" : "none", "Encoding" : null, "NamePrefix" : null}|["https://storageaccount.blob.core.windows.net/container1;\*\*\*\*\*\*\*"]|[{« Mod » : 10, « Name » : « CustomerId », « ColumnName » : « CustomerName », « ordinal » : 0}, {« Function » : « StartOfDay », « Name » : « date », « ColumnName » : « timestamp », « ordinal » : 1}]|«Customer \_ ID = "CustomerID"/DT = "DateTime \_ pattern (" AAAAMMJJ ", date)|

<a name="virtual-columns"></a>
**Colonnes virtuelles**

Lorsque les données sont exportées à partir de Spark, les colonnes de partition (spécifiées dans la méthode du writer tableau `partitionBy` ) ne sont pas écrites dans les fichiers de données. Ce processus évite la duplication des données, car les données sont déjà présentes dans les noms de « dossiers ». Par exemple, `column1=<value>/column2=<value>/` et Spark peut le reconnaître lors de la lecture.

Les tables externes prennent en charge la syntaxe suivante pour spécifier des colonnes virtuelles :

```kusto
.create external table ExternalTable (EventName:string, Revenue:double)  
kind=blob  
partition by (CustomerName:string, Date:datetime)  
pathformat = ("customer=" CustomerName "/date=" datetime_pattern("yyyyMMdd", Date))  
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

<a name="file-filtering"></a>
**Logique de filtrage de fichier**

Lors de l’interrogation d’une table externe, le moteur de requête améliore les performances en filtrant les fichiers de stockage externe inutiles. Le processus d’itération sur les fichiers et de détermination de la nécessité de traiter un fichier est décrit ci-dessous.

1. Générez un modèle d’URI qui représente un emplacement où les fichiers sont trouvés. Initialement, le modèle d’URI est égal à une chaîne de connexion fournie dans le cadre de la définition de la table externe. Si des partitions sont définies, elles sont rendues à l’aide de *[PathFormat](#path-format)* , puis ajoutées au modèle d’URI.

2. Pour tous les fichiers trouvés sous le ou les modèles d’URI créés, activez la case à cocher :

   * Les valeurs de partition correspondent aux prédicats utilisés dans une requête.
   * Le nom de l’objet BLOB commence par `NamePrefix` , si une telle propriété est définie.
   * Le nom de l’objet BLOB se termine par `FileExtension` , si une telle propriété est définie.

Une fois que toutes les conditions sont remplies, le fichier est extrait et traité par le moteur de requête.

> [!NOTE]
> Le modèle d’URI initial est généré à l’aide de valeurs de prédicat de requête. Cela fonctionne mieux pour un ensemble limité de valeurs de chaîne, ainsi que pour des plages de temps fermées.

## <a name="show-external-table-artifacts"></a>. afficher les artefacts de table externe

Retourne une liste de tous les fichiers qui seront traités lors de l’interrogation d’une table externe donnée.

> [!NOTE]
> L’opération requiert l' [autorisation utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show``external` `table` *TableName* `artifacts` [ `limit` *MaxResults* ]

où *MaxResults* est un paramètre facultatif, qui peut être défini pour limiter le nombre de résultats.

**Sortie**

| Paramètre de sortie | Type   | Description                       |
|------------------|--------|-----------------------------------|
| Uri              | string | URI du fichier de données de stockage externe |
| Taille             | long   | Longueur du fichier en octets              |
| Partition        | dynamique | Objet dynamique décrivant les partitions de fichiers pour une table externe partitionnée |

> [!TIP]
> L’itération sur tous les fichiers référencés par une table externe peut être relativement coûteuse, en fonction du nombre de fichiers. Veillez à utiliser le `limit` paramètre si vous souhaitez simplement voir des exemples d’URI.

**Exemples :**

```kusto
.show external table T artifacts
```

**Output:**

| Uri                                                                     | Taille | Partition |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` | 10743 | `{}`   |


Pour une table partitionnée, la `Partition` colonne contient des valeurs de partition extraites :

**Output:**

| Uri                                                                     | Taille | Partition |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/customer=john.doe/dt=20200101/file.csv` | 10743 | `{"Customer": "john.doe", "Date": "2020-01-01T00:00:00.0000000Z"}` |


## <a name="create-external-table-mapping"></a>. créer un mappage de table externe

`.create``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

Crée un nouveau mappage. Pour plus d’informations, consultez [mappages de données](./mappings.md#json-mapping).

**Exemple** 
 
```kusto
.create external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**Exemple de sortie**

| Nom     | Kind | Mappage                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "Properties" : {"path" : "$. rowguid"}}] |

## <a name="alter-external-table-mapping"></a>. modification du mappage de tables externes

`.alter``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

Modifie un mappage existant. 
 
**Exemple** 
 
```kusto
.alter external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**Exemple de sortie**

| Nom     | Kind | Mappage                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "Properties" : {"path" : "$. rowguid"}}] |

## <a name="show-external-table-mappings"></a>. afficher les mappages de tables externes

`.show``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* ExternalTableName `json``mappings`

Affichez les mappages (tout ou partie spécifiés par nom).
 
**Exemple** 
 
```kusto
.show external table MyExternalTable json mapping "Mapping1" 

.show external table MyExternalTable json mappings 
```

**Exemple de sortie**

| Nom     | Kind | Mappage                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "Properties" : {"path" : "$. rowguid"}}] |

## <a name="drop-external-table-mapping"></a>. supprimer le mappage de table externe

`.drop``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

Supprime le mappage de la base de données.
 
**Exemple** 
 
```kusto
.drop external table MyExternalTable json mapping "Mapping1" 
```
## <a name="next-steps"></a>Étapes suivantes

* [Interroger des tables externes](../../data-lake-query-data.md).
* [Exporter des données vers une table externe](data-export/export-data-to-an-external-table.md).
* [Exportation continue de données vers une table externe](data-export/continuous-data-export.md).

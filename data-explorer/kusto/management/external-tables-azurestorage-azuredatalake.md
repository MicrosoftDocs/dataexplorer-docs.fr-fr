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
ms.openlocfilehash: 2ef238d863f2f3fe181814ac14e3605de21a5aff
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863368"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>Créer et modifier des tables externes dans le stockage Azure ou Azure Data Lake

La commande suivante décrit comment créer une table externe. La table peut être située dans le stockage d’objets BLOB Azure, Azure Data Lake Store Gen1 ou Azure Data Lake Store Gen2. 
Les [chaînes de connexion de stockage](../api/connection-strings/storage.md) décrivent la création de la chaîne de connexion pour chacune de ces options. 

## <a name="create-or-alter-external-table"></a>. Create ou. Alter External table

**Syntaxe**

( `.create`  |  `.alter` ) `external` `table` *TableName* (*Schema*)  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` *Partition* [.... `,` ]]  
`dataformat` `=` *Format*  
`(`  
*StorageConnectionString* [ `,` ...]  
`)`  
[ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*], *property_name* `=` *valeur* `,` ... `)` ]

Crée ou modifie une nouvelle table externe dans la base de données dans laquelle la commande est exécutée.

**Paramètres**

* *TableName* -nom de la table externe. Doit suivre les règles pour les [noms d’entité](../query/schema-entities/entity-names.md). Une table externe ne peut pas avoir le même nom qu’une table normale dans la même base de données.
* *Schéma-schéma* de données externes au format : `ColumnName:ColumnType[, ColumnName:ColumnType ...]` . Si le schéma de données externes est inconnu, utilisez le plug-in [infer_storage_schema](../query/inferstorageschemaplugin.md) , qui peut déduire le schéma en fonction du contenu du fichier externe.
* *Partition* : une ou plusieurs définitions de partition (facultatif). Consultez la syntaxe de partition ci-dessous.
* *Format* : format de données. Tous les [formats](../../ingestion-supported-formats.md) d’ingestion sont pris en charge pour l’interrogation. L’utilisation de la table externe pour le [scénario d’exportation](data-export/export-data-to-an-external-table.md) se limite aux formats suivants : `CSV` , `TSV` , `JSON` , `Parquet` .
* *StorageConnectionString* : un ou plusieurs chemins d’accès aux conteneurs d’objets BLOB de stockage BLOB Azure ou Azure Data Lake Store systèmes de fichiers (répertoires virtuels ou dossiers), y compris les informations d’identification. Pour plus d’informations, consultez [chaînes de connexion de stockage](../api/connection-strings/storage.md) . Fournissez plus d’un compte de stockage unique pour éviter la limitation du stockage lors de l' [exportation](data-export/export-data-to-an-external-table.md) de grandes quantités de données vers la table externe. L’exportation répartit les écritures entre tous les comptes fournis. 

**Syntaxe de la partition**

[ `format_datetime =` *DateTimePartitionFormat*] `bin(` *TimestampColumnName*, *PartitionByTimeSpan*`)`  
|   
[*StringFormatPrefix*] *StringColumnName* [*StringFormatSuffix*])

**Paramètres de partition**

* *DateTimePartitionFormat* : format de la structure de répertoires requise dans le chemin de sortie (facultatif). Si le partitionnement est défini et que le format n’est pas spécifié, la valeur par défaut est « yyyy/MM/JJ/HH/MM ». Ce format est basé sur PartitionByTimeSpan. Par exemple, si vous partitionnez par 1J, structure sera « yyyy/MM/JJ ». Si vous partitionnez par 1 h, la structure sera « yyyy/MM/JJ/HH ».
* *TimestampColumnName* : colonne DateTime sur laquelle la table est partitionnée. La colonne timestamp doit exister dans la définition de schéma de la table externe et la sortie de la requête d’exportation, lors de l’exportation vers la table externe.
* *PartitionByTimeSpan* -TimeSpan littéral par lequel partitionner.
* *StringFormatPrefix* : littéral de chaîne constant qui fera partie du chemin d’artefact, concaténé avant la valeur de la table (facultatif).
* *StringFormatSuffix* : littéral de chaîne constant qui fera partie du chemin d’artefact, concaténé après la valeur de la table (facultatif).
* *StringColumnName* : colonne de chaîne sur laquelle la table est partitionnée. La colonne de chaîne doit exister dans la définition de schéma de la table externe.

**Propriétés facultatives**:

| Propriété         | Type     | Description       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Dossier de la table                                                                     |
| `docString`      | `string` | Chaîne documentant la table                                                       |
| `compressed`     | `bool`   | Si cette valeur est définie, indique si les objets BLOB sont compressés en tant que `.gz` fichiers                  |
| `includeHeaders` | `string` | Pour les objets BLOB CSV ou TSV, indique si les objets BLOB contiennent un en-tête                     |
| `namePrefix`     | `string` | Si cette valeur est définie, indique le préfixe des objets BLOB. Lors des opérations d’écriture, tous les objets BLOB sont écrits avec ce préfixe. Sur les opérations de lecture, seuls les objets BLOB avec ce préfixe sont lus. |
| `fileExtension`  | `string` | S’il est défini, indique les extensions de fichier des objets BLOB. Lors de l’écriture, les noms d’objets BLOB se terminent par ce suffixe. En lecture, seuls les objets BLOB avec cette extension de fichier seront lus.           |
| `encoding`       | `string` | Indique comment le texte est encodé : `UTF8NoBOM` (valeur par défaut) ou `UTF8BOM` .             |

Pour plus d’informations sur les paramètres de table externe dans les requêtes, consultez [logique de filtrage d’artefact](#artifact-filtering-logic).

> [!NOTE]
> * Si la table existe, la `.create` commande échoue avec une erreur. Utilisez `.alter` pour modifier des tables existantes. 
> * La modification du schéma, du format ou de la définition de partition d’une table d’objets BLOB externe n’est pas prise en charge. 

Requiert l' [autorisation utilisateur de base de données](../management/access-control/role-based-authorization.md) pour `.create` et l’autorisation d’administrateur de [table](../management/access-control/role-based-authorization.md) pour `.alter` . 
 
**Exemple** 

Table externe non partitionnée. Tous les artefacts sont censés être directement sous le ou les conteneurs définis :

```kusto
.create external table ExternalBlob (x:long, s:string) 
kind=blob
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)  
```

Table externe partitionnée par dateTime. Les artefacts se trouvent dans des répertoires au format « YYYY/MM/JJ » dans le ou les chemins d’accès définis :

```kusto
.create external table ExternalAdlGen2 (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by bin(Timestamp, 1d)
dataformat=csv
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)  
```

Table externe partitionnée par dateTime avec un format de répertoire « Year = yyyy/month = MM/Day = DD » :

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="'year='yyyy/'month='MM/'day='dd" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)
```

Une table externe avec des partitions de données mensuelles et un format de répertoire « yyyy/MM » :

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="yyyy/MM" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)
```

Table externe avec deux partitions. La structure de répertoires est la concaténation des deux partitions : mise en forme de CustomerName suivie du format dateTime par défaut. Par exemple, « CustomerName = n ° de la année/2011/11/11 » :

```kusto
.create external table ExternalMultiplePartitions (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"   
)
```

**Sortie**

|TableName|TableType|Dossier|DocString|Propriétés|ConnectionStrings|Partitions|
|---|---|---|---|---|---|---|
|ExternalMultiplePartitions|Objet blob|ExternalTables|Documents|{"Format" : "CSV", "Compressed" : false, "CompressionType" : null, "FileExtension" : "CSV", "IncludeHeaders" : "none", "Encoding" : null, "NamePrefix" : null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat" : "CustomerName = {0} ", "ColumnName" : "customerName", "ordinal" : 0}, PartitionBy " :" 1.00:00:00 "," ColumnName " :" timestamp "," ordinal " : 1}]|

### <a name="artifact-filtering-logic"></a>Logique de filtrage d’artefact

Lors de l’interrogation d’une table externe, le moteur de requête améliore les performances en filtrant les artefacts de stockage externe non pertinents (objets BLOB). Le processus d’itération sur les objets BLOB et de choix du traitement d’un objet blob est décrit ci-dessous.

1. Créez un modèle d’URI qui représente un emplacement où les objets BLOB sont trouvés. Initialement, le modèle d’URI est égal à une chaîne de connexion fournie dans le cadre de la définition de la table externe. Si des partitions sont définies, elles sont ajoutées au modèle d’URI.
Par exemple, si la chaîne de connexion est : `https://storageaccount.blob.core.windows.net/container1` et qu’une partition DateTime est définie : `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)` , le modèle d’URI correspondant est : `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd` , et nous allons Rechercher des objets BLOB sous les emplacements qui correspondent à ce modèle.
Si une partition de chaîne supplémentaire `"CustomerId" customerId` est définie, le modèle d’URI correspondant est : `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*` .

1. Pour tous les objets BLOB *directs* trouvés sous le ou les modèles d’URI que vous avez créés, consultez :

   * Les valeurs de partition correspondent aux prédicats utilisés dans une requête.
   * Le nom de l’objet BLOB commence par `NamePrefix` , si une telle propriété est définie.
   * Le nom de l’objet BLOB se termine par `FileExtension` , si une telle propriété est définie.

Une fois que toutes les conditions sont réunies, l’objet blob est extrait et traité par le moteur de requête.

### <a name="spark-virtual-columns-support"></a>Prise en charge des colonnes virtuelles Spark

Lorsque les données sont exportées à partir de Spark, les colonnes de partition (spécifiées dans la méthode du writer tableau `partitionBy` ) ne sont pas écrites dans les fichiers de données. Ce processus évite la duplication des données, car les données sont déjà présentes dans les noms de « dossiers ». Par exemple, `column1=<value>/column2=<value>/` et Spark peut le reconnaître lors de la lecture. Toutefois, Kusto nécessite que les colonnes de partition soient présentes dans les données elles-mêmes. La prise en charge des colonnes virtuelles dans Kusto est prévue. À ce moment-là, utilisez la solution de contournement suivante : lors de l’exportation de données à partir de Spark, créez une copie de toutes les colonnes sur lesquelles les données sont partitionnées avant d’écrire un tableau :

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Lors de la définition d’une table externe dans Kusto, spécifiez les colonnes de partition comme dans l’exemple suivant :

```kusto
.create external table ExternalSparkTable(a:string, b:datetime) 
kind=blob
partition by 
   "_a="a,
   format_datetime="'_b='yyyyMMdd" bin(b, 1d)
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

## <a name="show-external-table-artifacts"></a>. afficher les artefacts de table externe

* Retourne une liste de tous les artefacts qui seront traités lors de l’interrogation d’une table externe donnée.
* Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show` `external` `table` *TableName* `artifacts`

**Sortie**

| Paramètre de sortie | Type   | Description                       |
|------------------|--------|-----------------------------------|
| Uri              | string | URI de l’artefact de stockage externe |

**Exemples :**

```kusto
.show external table T artifacts
```

**Output:**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

## <a name="create-external-table-mapping"></a>. créer un mappage de table externe

`.create``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

Crée un nouveau mappage. Pour plus d’informations, consultez [mappages de données](./mappings.md#json-mapping).

**Exemple** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Exemple de sortie**

| Nom     | Genre | Mappage                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "ColumnType" : "int", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "ColumnType" : "", "Properties" : {"path" : "$. rowguid"}}] |

## <a name="alter-external-table-mapping"></a>. modification du mappage de tables externes

`.alter``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

Modifie un mappage existant. 
 
**Exemple** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Exemple de sortie**

| Nom     | Genre | Mappage                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "ColumnType" : "", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "ColumnType" : "", "Properties" : {"path" : "$. rowguid"}}] |

## <a name="show-external-table-mappings"></a>. afficher les mappages de tables externes

`.show``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* ExternalTableName `json``mappings`

Affichez les mappages (tout ou partie spécifiés par nom).
 
**Exemple** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**Exemple de sortie**

| Nom     | Genre | Mappage                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "ColumnType" : "", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "ColumnType" : "", "Properties" : {"path" : "$. rowguid"}}] |

## <a name="drop-external-table-mapping"></a>. supprimer le mappage de table externe

`.drop``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

Supprime le mappage de la base de données.
 
**Exemple** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```
## <a name="next-steps"></a>Étapes suivantes

* [Commandes de contrôle générales de table externe](externaltables.md)
* [Créer et modifier des tables SQL externes](external-sql-tables.md)

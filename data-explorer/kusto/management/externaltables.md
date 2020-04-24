---
title: Gestion des tables externes-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la gestion des tables externes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 624c0a7f1105ff13642649174f769781f1749598
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108069"
---
# <a name="external-table-management"></a>Gestion des tables externes

Pour obtenir une vue d’ensemble des tables externes, consultez [tables externes](../query/schema-entities/externaltables.md) . 

## <a name="common-external-tables-control-commands"></a>Commandes de contrôle de tables externes communes

Les commandes suivantes s’appliquent à _toute_ table externe (de tout type).

### <a name="show-external-tables"></a>. afficher les tables externes

* Retourne toutes les tables externes de la base de données (ou d’une table externe spécifique).
* Nécessite l' [autorisation du moniteur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show` `external` `tables`

`.show``external` *TableName* TableName `table`

**Sortie**

| Paramètre de sortie | Type   | Description                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | Nom de la table externe                                             |
| TableType        | string | Type de table externe                                              |
| Dossier           | string | Dossier de la table                                                     |
| DocString        | string | Chaîne documentant la table                                       |
| Propriétés       | string | Propriétés sérialisées JSON de la table (spécifiques au type de table) |


**Exemples :**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Dossier         | DocString | Propriétés |
|-----------|-----------|----------------|-----------|------------|
| T         | Objet blob      | ExternalTables | Docs      | {}         |


### <a name="show-external-table-schema"></a>. afficher le schéma de la table externe

* Retourne le schéma de la table externe, au format JSON ou CSL. 
* Nécessite l' [autorisation du moniteur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show``external` `schema` *TableName* (`json`) `table` `as`  | `csl`

`.show``external` *TableName* TableName `table``cslschema`

**Sortie**

| Paramètre de sortie | Type   | Description                        |
|------------------|--------|------------------------------------|
| TableName        | string | Nom de la table externe            |
| schéma           | string | Schéma de table au format JSON |
| nom_base_de_données     | string | Nom de la base de données de la table             |
| Dossier           | string | Dossier de la table                    |
| DocString        | string | Chaîne documentant la table      |

**Exemples :**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**Output:**

*format*

| TableName | schéma    | nom_base_de_données | Dossier         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {« Name » : « ExternalBlob »,<br>« Dossier » : « ExternalTables »,<br>« DocString » : « docs »,<br>"OrderedColumns" : [{"Name" : "x", "type" : "System. Int64", "CslType" : "long", "DocString" : ""}, {"Name" : "s", "type" : "System. String", "CslType" : "String", "DocString" : ""}]} | DB           | ExternalTables | Docs      |


*CSL*

| TableName | schéma          | nom_base_de_données | Dossier         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long,s:string | DB           | ExternalTables | Docs      |

### <a name="drop-external-table"></a>. supprimer la table externe

* Supprime une table externe 
* La définition de la table externe ne peut pas être restaurée après cette opération
* Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :**  

`.drop``external` *TableName* TableName `table`

**Sortie**

Retourne les propriétés de la table supprimée. Consultez [. afficher les tables externes](#show-external-tables).

**Exemples :**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Dossier         | DocString | schéma       | Propriétés |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Objet blob      | ExternalTables | Docs      | [{"Name" : "x", "CslType" : "long"},<br> {"Name" : "s", "CslType" : "String"}] | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Tables externes dans le stockage Azure ou Azure Data Lake

La commande suivante décrit comment créer une table externe. La table peut être située dans le stockage d’objets BLOB Azure, Azure Data Lake Store Gen1 ou Azure Data Lake Store Gen2. 
Les [chaînes de connexion de stockage](../api/connection-strings/storage.md) décrivent la création de la chaîne de connexion pour chacune de ces options. 

### <a name="create-or-alter-external-table"></a>. Create ou. Alter External table

**Syntaxe**

`.create` | (`.alter`) `external` *TableName (* *Schema*) `table`  
`kind` `=` (`blob` | `adl`)  
[`partition` `by` *Partition* [`,` ....]]  
`dataformat``=` *Format*  
`(`  
*StorageConnectionString* [`,` ...]  
`)`  
[`with` `(`[`docstring` `=` *FolderName* `=` *value* *Documentation* `folder` `,` *property_name* Documentation] [`,` NomDossier], property_name valeur... `=` `)`]

Crée ou modifie une nouvelle table externe dans la base de données dans laquelle la commande est exécutée.

**Parameters**

* *TableName* -nom de la table externe. Doit suivre les règles pour les [noms d’entité](../query/schema-entities/entity-names.md). Une table externe ne peut pas avoir le même nom qu’une table normale dans la même base de données.
* *Schéma-schéma* de données externes au format `ColumnName:ColumnType[, ColumnName:ColumnType ...]`:. Si le schéma de données externes est inconnu, utilisez le plug-in [infer_storage_schema](../query/inferstorageschemaplugin.md) , qui peut déduire le schéma en fonction du contenu du fichier externe.
* *Partition* : une ou plusieurs définitions de partition (facultatif). Consultez la syntaxe de partition ci-dessous.
* *Format* : format de données. Tous les [formats](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) d’ingestion sont pris en charge pour l’interrogation. L’utilisation de la table externe pour le [scénario d’exportation](data-export/export-data-to-an-external-table.md) se limite `CSV`aux `TSV`formats `JSON`suivants `Parquet`:,,,.
* *StorageConnectionString* : un ou plusieurs chemins d’accès aux conteneurs d’objets BLOB de stockage BLOB Azure ou Azure Data Lake Store systèmes de fichiers (répertoires virtuels ou dossiers), y compris les informations d’identification. Pour plus d’informations, consultez [chaînes de connexion de stockage](../api/connection-strings/storage.md) . Il est vivement recommandé de fournir plus d’un seul compte de stockage pour éviter la limitation du stockage en cas d' [exportation](data-export/export-data-to-an-external-table.md) de grandes quantités de données vers la table externe. L’exportation répartit les écritures entre tous les comptes fournis. 

**Syntaxe de la partition**

[`format_datetime =` *DateTimePartitionFormat*] `bin(` *TimestampColumnName*, *PartitionByTimeSpan*`)`  
|   
[*StringFormatPrefix*] *StringColumnName* [*StringFormatSuffix*])

**Paramètres de partition**

* *DateTimePartitionFormat* : format de la structure de répertoires requise dans le chemin de sortie (facultatif). Si le partitionnement est défini et que le format n’est pas spécifié, la valeur par défaut est « yyyy/MM/JJ/HH/mm », en fonction du PartitionByTimeSpan. Par exemple, si vous partitionnez par 1J, structure sera « yyyy/MM/JJ ». Si vous partitionnez par 1H, la structure sera « yyyy/MM/JJ/HH ».
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
| `compressed`     | `bool`   | Si cette valeur est définie, indique si les objets `.gz` BLOB sont compressés en tant que fichiers                  |
| `includeHeaders` | `string` | Pour les objets BLOB CSV ou TSV, indique si les objets BLOB contiennent un en-tête                     |
| `namePrefix`     | `string` | Si cette valeur est définie, indique le préfixe des objets BLOB. Lors des opérations d’écriture, tous les objets BLOB sont écrits avec ce préfixe. Sur les opérations de lecture, seuls les objets BLOB avec ce préfixe sont lus. |
| `fileExtension`  | `string` | S’il est défini, indique les extensions de fichier des objets BLOB. Lors de l’écriture, les noms d’objets BLOB se terminent par ce suffixe. En lecture, seuls les objets BLOB avec cette extension de fichier seront lus.           |
| `encoding`       | `string` | Indique comment le texte est encodé : `UTF8NoBOM` (valeur par défaut) ou `UTF8BOM`.             |

> [!NOTE]
> * Si la table existe, `.create` la commande échoue avec une erreur. Utilisez `.alter` pour modifier des tables existantes. 
> * La modification du schéma, du format ou de la définition de partition d’une table d’objets BLOB externe n’est pas prise en charge. 

Requiert [l’autorisation utilisateur de base de données](../management/access-control/role-based-authorization.md) `.create` pour et l’autorisation d’administrateur de [table](../management/access-control/role-based-authorization.md) pour `.alter`. 
 
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
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

Table externe partitionnée par dateTime. Les artefacts résident dans des répertoires au format « YYYY/MM/JJ » dans le ou les chemins d’accès définis :

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
   folder = "ExternalTables",
   namePrefix="Prefix"
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
   folder = "ExternalTables",
   namePrefix="Prefix"
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
   folder = "ExternalTables",
   namePrefix="Prefix"
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
|ExternalMultiplePartitions|Objet blob|ExternalTables|Docs|{"Format" : "CSV", "Compressed" : false, "CompressionType" : null, "FileExtension" : "CSV", "IncludeHeaders" : "none", "Encoding" : null, "NamePrefix" : null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat" : "CustomerName ={0}", "ColumnName" : "customerName", "ordinal" : 0}, PartitionBy " :" 1.00:00:00 "," ColumnName " :" timestamp "," ordinal " : 1}]|

#### <a name="spark-virtual-columns-support"></a>Prise en charge des colonnes virtuelles Spark

Lorsque les données sont exportées à partir de Spark, les colonnes de partition ( `partitionBy` spécifiées dans la méthode du writer tableau) ne sont pas écrites dans les fichiers de données. Cela permet d’éviter la duplication des données, car les données déjà présentes dans les noms de « dossiers » `column1=<value>/column2=<value>/`(par exemple,), et Spark peuvent la reconnaître lors de la lecture. Toutefois, Kusto nécessite que les colonnes de partition soient présentes dans les données elles-mêmes. La prise en charge des colonnes virtuelles dans Kusto est prévue. À ce moment-là, utilisez la solution de contournement suivante : lors de l’exportation de données à partir de Spark, créez une copie de toutes les colonnes sur lesquelles les données sont partitionnées avant d’écrire un tableau :

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

### <a name="show-external-table-artifacts"></a>. afficher les artefacts de table externe

* Retourne une liste de tous les artefacts qui seront traités lors de l’interrogation d’une table externe donnée.
* Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show``external` *TableName* TableName `table``artifacts`

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

### <a name="create-external-table-mapping"></a>. créer un mappage de table externe

`.create``external` `mapping` *MappingName* *MappingInJsonFormat* *ExternalTableName* ExternalTableName `json` MappingInJsonFormat `table`

Crée un nouveau mappage. Pour plus d’informations, consultez [mappages de données](./mappings.md#json-mapping).

**Exemple** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Exemple de sortie**

| Nom     | Type | Mappage                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "ColumnType" : "int", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "ColumnType" : "", "Properties" : {"path" : "$. rowguid"}}] |

### <a name="alter-external-table-mapping"></a>. modification du mappage de tables externes

`.alter``external` `mapping` *MappingName* *MappingInJsonFormat* *ExternalTableName* ExternalTableName `json` MappingInJsonFormat `table`

Modifie un mappage existant. 
 
**Exemple** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Exemple de sortie**

| Nom     | Type | Mappage                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "ColumnType" : "", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "ColumnType" : "", "Properties" : {"path" : "$. rowguid"}}] |

### <a name="show-external-table-mappings"></a>. afficher les mappages de tables externes

`.show``external` `mapping` *MappingName* *ExternalTableName* ExternalTableName MappingName `table` `json` 

`.show``external` `json` *ExternalTableName* ExternalTableName `table``mappings`

Affichez les mappages (tout ou partie spécifiés par nom).
 
**Exemple** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**Exemple de sortie**

| Nom     | Type | Mappage                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName" : "RowNumber", "ColumnType" : "", "Properties" : {"path" : "$. RowNumber"}}, {"ColumnName" : "rowguid", "ColumnType" : "", "Properties" : {"path" : "$. rowguid"}}] |

### <a name="drop-external-table-mapping"></a>. supprimer le mappage de table externe

`.drop``external` `mapping` *MappingName* *ExternalTableName* ExternalTableName MappingName `table` `json` 

Supprime le mappage de la base de données.
 
**Exemple** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>Table SQL externe

### <a name="create-or-alter-external-sql-table"></a>. créer ou modifier une table SQL externe

Crée ou modifie une table SQL externe dans la base de données dans laquelle la commande est exécutée.  

**Syntaxe**

`.create` | (`.alter`) `external` TableName ([ColumnName : ColumnType],...) *TableName* `table`  
`kind` `=` `sql`  
`table``=` *SqlTableName*  
`(`*SqlServerConnectionString*`)`  
[`with` `(`[`docstring` `=` *FolderName* `=` *value* *Documentation* `folder` `,` *property_name* Documentation] [`,` NomDossier], property_name valeur... `=` `)`]


**Paramètres**

* *TableName* -nom de la table externe. Doit suivre les règles pour les [noms d’entité](../query/schema-entities/entity-names.md). Une table externe ne peut pas avoir le même nom qu’une table normale dans la même base de données.
* *SqlTableName* : nom de la table SQL.
* *SqlServerConnectionString* : chaîne de connexion au serveur SQL Server. Il peut s’agir de l’un des éléments suivants : 
    * **Authentification intégrée AAD** (`Authentication="Active Directory Integrated"`) : l’utilisateur ou l’application s’authentifie via AAD sur Kusto, et le même jeton est ensuite utilisé pour accéder au point de terminaison du réseau SQL Server.
    * **Authentification par nom d’utilisateur/mot de passe** (`User ID=...; Password=...;`). Si la table externe est utilisée pour l' [exportation continue](data-export/continuous-data-export.md), l’authentification doit être effectuée à l’aide de cette méthode. 

> [!WARNING]
> Les chaînes de connexion et les requêtes qui incluent des informations confidentielles doivent être obscurcies pour qu’elles soient omises de tout suivi Kusto. Pour plus d’informations, consultez [littéraux de chaîne obscurcis](../query/scalar-data-types/string.md#obfuscated-string-literals) .

**Propriétés facultatives**
| Propriété            | Type            | Description                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Dossier de la table.                  |
| `docString`         | `string`        | Chaîne qui documente la table.      |
| `firetriggers`      | `true`/`false`  | Si `true`la valeur est, indique au système cible d’activer les déclencheurs d’insertion définis sur la table SQL. Par défaut, il s’agit de `false`. (Pour plus d’informations, consultez [Bulk Insert](https://msdn.microsoft.com/library/ms188365.aspx) et [System. Data. SqlClient. SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)) |
| `createifnotexists` | `true`/ `false` | Si `true`la, la table SQL cible est créée si elle n’existe pas déjà ; la `primarykey` propriété doit être fournie dans ce cas pour indiquer la colonne de résultats qui est la clé primaire. Par défaut, il s’agit de `false`.  |
| `primarykey`        | `string`        | Si `createifnotexists` a `true`la valeur, indique le nom de la colonne dans le résultat qui sera utilisé comme clé primaire de la table SQL s’il est créé par cette commande.                  |

> [!NOTE]
> * Si la table existe, la `.create` commande échoue et génère une erreur. Utilisez `.alter` pour modifier des tables existantes. 
> * La modification du schéma ou du format d’une table SQL externe n’est pas prise en charge. 

Requiert [l’autorisation utilisateur de base de données](../management/access-control/role-based-authorization.md) `.create` pour et l’autorisation d’administrateur de [table](../management/access-control/role-based-authorization.md) pour `.alter`. 
 
**Exemple** 

```kusto
.create external table ExternalSql (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**Sortie**

| TableName   | TableType | Dossier         | DocString | Propriétés                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| ExternalSql | SQL       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString" : "Server = TCP :myserver. Database. Windows. net, 1433 ; Authentication = Active Directory intégré ; initial catalog = mabdd ;»,<br>  « FireTriggers » : true,<br>  « CreateIfNotExists » : true,<br>  « PrimaryKey » : « x »<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>Interrogation d’une table externe de type SQL 
Comme pour d’autres types de tables externes, l’interrogation d’une table SQL externe est prise en charge. Consultez [interrogation de tables externes](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data). Notez que l’implémentation de la requête de table externe SQL exécute un « SELECT * » complet (ou sélectionne des colonnes pertinentes) à partir de la table SQL, tandis que le reste de la requête s’exécute du côté Kusto. Examinez la requête de table externe suivante : 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto exécute une requête’SELECT * FROM TABLE’dans la base de données SQL, suivie d’un décompte du côté Kusto. Dans ce cas, les performances devraient être meilleures si elles sont écrites directement dans T-SQL (« SELECT COUNT (1) FROM TABLE ») et exécutées à l’aide du [plug-in sql_request](../query/sqlrequestplugin.md), au lieu d’utiliser la fonction de table externe. De même, les filtres ne sont pas transmis à la requête SQL.  
Il est recommandé d’utiliser la table externe pour interroger la table SQL lorsque celle-ci requiert la lecture de la totalité de la table (ou des colonnes appropriées) pour une exécution ultérieure côté Kusto. Lorsqu’une requête SQL peut être optimisée de manière significative dans T-SQL, utilisez le [plug-in sql_request](../query/sqlrequestplugin.md).

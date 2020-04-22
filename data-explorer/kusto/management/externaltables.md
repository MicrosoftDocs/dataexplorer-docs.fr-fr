---
title: Gestion de table externe - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion externe de la table dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 680a4e25d6b478fe171aa3296de81c0106417877
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744715"
---
# <a name="external-table-management"></a>Gestion externe de la table

Voir [les tableaux externes](../query/schema-entities/externaltables.md) pour un aperçu des tables externes. 

## <a name="common-external-tables-control-commands"></a>Commandes de contrôle de tables externes communes

Les commandes suivantes sont pertinentes pour _toute_ table externe (de tout type).

### <a name="show-external-tables"></a>.afficher les tables externes

* Renvoie toutes les tables externes dans la base de données (ou un tableau externe spécifique).
* Nécessite [l’autorisation de surveillance de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show` `external` `tables`

`.show`Nom *de la table* `external` `table`

**Sortie**

| Paramètre de sortie | Type   | Description                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | Nom de la table externe                                             |
| TableType        | string | Type de table externe                                              |
| Dossier           | string | Dossier de table                                                     |
| DocString (en)        | string | Chaîne documentant la table                                       |
| Propriétés       | string | Propriétés sérialisées JSON de table (spécifiques au type de tableau) |


**Exemples :**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Dossier         | DocString (en) | Propriétés |
|-----------|-----------|----------------|-----------|------------|
| T         | Objet blob      | ExternalTables (en) | Docs      | {}         |


### <a name="show-external-table-schema"></a>.montrer schéma de table externe

* Retourne le schéma de la table externe, comme JSON ou CSL. 
* Nécessite [l’autorisation de surveillance de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show``external` `as` *TableName* `schema` `csl`TableName`json`( | ) `table`

`.show`Nom *de la table* `external` `table``cslschema`

**Sortie**

| Paramètre de sortie | Type   | Description                        |
|------------------|--------|------------------------------------|
| TableName        | string | Nom de la table externe            |
| schéma           | string | Le schéma de table dans un format JSON |
| nom_base_de_données     | string | Nom de base de données de table             |
| Dossier           | string | Dossier de table                    |
| DocString (en)        | string | Chaîne documentant la table      |

**Exemples :**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**Output:**

*Json:*

| TableName | schéma    | nom_base_de_données | Dossier         | DocString (en) |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | "Name":"ExternalBlob",<br>"Folder":"ExternalTables",<br>"DocString":"Docs",<br>"OrderedColumns":["Name":"x","Type":"System.Int64","CslType":"long","DocString":"","Name":"s","Type":"System.String","CslType":"string","DocString":""] | DB           | ExternalTables (en) | Docs      |


*Csl:*

| TableName | schéma          | nom_base_de_données | Dossier         | DocString (en) |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long, s:string | DB           | ExternalTables (en) | Docs      |

### <a name="drop-external-table"></a>.drop table externe

* Dépose une table externe 
* La définition de table externe ne peut pas être restaurée à la suite de cette opération
* Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :**  

`.drop`Nom *de la table* `external` `table`

**Sortie**

Retourne les propriétés de la table abandonnée. Voir [.show tables externes](#show-external-tables).

**Exemples :**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Dossier         | DocString (en) | schéma       | Propriétés |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Objet blob      | ExternalTables (en) | Docs      | ["Nom": "x", "CslType": "long",<br> "Nom": "s", "CslType": "corde" | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Tables externes dans Azure Storage ou Azure Data Lake

La commande suivante décrit comment créer une table externe. La table peut être située dans Azure Blob Storage, Azure Data Lake Store Gen1 ou Azure Data Lake Store Gen2. 
[Les chaînes de connexion de stockage](../api/connection-strings/storage.md) décrivent la création de la chaîne de connexion pour chacune de ces options. 

### <a name="create-or-alter-external-table"></a>.créer ou .modifier la table externe

**Syntaxe**

`.create` | (`.alter` `external` ) `table` *Nom de table* (*Schema*)  
`kind` `=` (`blob` | `adl`)  
`partition` `by` *[Partition* `,` [....]]  
`dataformat``=` *Format*  
`(`  
*StorageConnectionString* `,` [...]  
`)`  
[`with` `(``docstring` `=` *Documentation*]`,` `folder` `=` [ *FolderName*], *property_name* `=` *valeur*`,`... `)`]

Crée ou modifie une nouvelle table externe dans la base de données dans laquelle la commande est exécutée.

**Paramètres**

* *TableName* - Nom de table externe. Doit suivre les règles pour les [noms d’entités](../query/schema-entities/entity-names.md). Une table externe ne peut pas avoir le même nom qu’une table ordinaire dans la même base de données.
* *Schema* - Schéma de données `ColumnName:ColumnType[, ColumnName:ColumnType ...]`externes en format: . Si le schéma de données externes est inconnu, utilisez le [plug-in infer_storage_schema,](../query/inferstorageschemaplugin.md) qui peut déduire le schéma basé sur le contenu externe du fichier.
* *Partition* - Une ou plusieurs définitions de partition (facultatif). Voir la syntaxe de partition ci-dessous.
* *Format* - Le format de données. Tous les [formats d’ingestion](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) sont pris en charge pour la requête. L’utilisation d’un tableau externe pour `CSV`le `TSV` `JSON`scénario `Parquet` [d’exportation](data-export/export-data-to-an-external-table.md) est limitée aux formats suivants : , , . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
* *StorageConnectionString* - Un ou plusieurs chemins vers des conteneurs de blob Azure Blob Storage ou des systèmes de fichiers Azure Data Lake Store (annuaires virtuels ou dossiers), y compris les informations d’identification. Voir les [chaînes de connexion de stockage](../api/connection-strings/storage.md) pour plus de détails. Il est fortement recommandé de fournir plus d’un seul compte de stockage pour éviter la limitation de stockage si [l’exportation](data-export/export-data-to-an-external-table.md) de grandes quantités de données à la table externe. L’exportation distribuera les écrits entre tous les comptes fournis. 

**Syntaxe de partition**

[`format_datetime =` *DateTimePartitionFormat*] `bin(` *TimestampColumnName*, *PartitionByTimeSpan*`)`  
|   
[*StringFormatPrefix*] *StringColumnName* [*StringFormatSuffix*])

**Paramètres de partition**

* *DateTimePartitionFormat* - Le format de la structure d’annuaire requise dans le chemin de sortie (facultatif). Si le partitionnement est défini et que le format n’est pas spécifié, la valeur par défaut est "yyyy/MM/dd/HH/mm", basée sur la PartitionByTimeSpan. Par exemple, si vous divisez par 1d, la structure sera "yyyy/MM/dd". Si vous divisez avant 1h, la structure sera "yyyy/MM/dd/HH".
* *TimestampColumnName* - Colonne Datetime sur laquelle la table est divisée. La colonne Timestamp doit exister dans la définition et la production externes du schéma de table et de la production de la requête d’exportation, lors de l’exportation vers la table externe.
* *PartitionByTimeSpan* - Timespan littéral par lequel cloisonner.
* *StringFormatPrefix* - Une chaîne constante littérale qui fera partie du chemin de l’artefact, concatenated avant la valeur de la table (facultatif).
* *StringFormatSuffix* - Une chaîne constante littérale qui fera partie du chemin de l’artefact, concatenated après la valeur de la table (facultatif).
* *StringColumnName* - Colonne de cordes sur laquelle la table est cloisonnée. La colonne de chaîne doit exister dans la définition externe de schéma de table.

**Propriétés facultatives**:

| Propriété         | Type     | Description       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Dossier de table                                                                     |
| `docString`      | `string` | Chaîne documentant la table                                                       |
| `compressed`     | `bool`   | Si défini, indique si les blobs sont compressés en tant que `.gz` fichiers                  |
| `includeHeaders` | `string` | Pour les blobs CSV ou TSV, indique si les blobs contiennent un en-tête                     |
| `namePrefix`     | `string` | Si réglé, indique le préfixe des blobs. Sur les opérations d’écriture, tous les blobs seront écrits avec ce préfixe. Sur les opérations de lecture, seuls les blobs avec ce préfixe sont lus. |
| `fileExtension`  | `string` | Si l’ensemble, indique les extensions de fichiers des blobs. Sur l’écriture, les noms de blobs se termineront avec ce suffixe. Sur lecture, seuls les blobs avec cette extension de fichier seront lus.           |
| `encoding`       | `string` | Indique comment le texte est `UTF8NoBOM` codé : `UTF8BOM`(par défaut) ou .             |

> [!NOTE]
> * Si le tableau `.create` existe, la commande échouera avec une erreur. Utiliser `.alter` pour modifier les tables existantes. 
> * Modifier le schéma, le format ou la définition de partition d’une table de blob externe n’est pas pris en charge. 

Nécessite [la permission de l’utilisateur de base](../management/access-control/role-based-authorization.md) de données pour `.create` et [l’autorisation d’administration de table](../management/access-control/role-based-authorization.md) pour `.alter`. 
 
**Exemple** 

Une table externe non cloisonnée. On s’attend à ce que tous les artefacts soient directement sous le récipient (s) définis :

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

Une table externe cloisonnée par dateTime. Les artefacts résident dans les répertoires en format "yyyy/MM/dd" sous le chemin (s) défini:

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

Une table externe cloisonnée par dateTime avec un format d’annuaire de "année-yyyy/mois-MM/jour-dd":

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

Un tableau externe avec des partitions de données mensuelles et un format d’annuaire de "yyyy/MM":

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

Une table externe avec deux cloisons. La structure d’annuaire est la concatenation des deux partitions : Le CustomerName formaté suivi du format datetime par défaut. Par exemple « CustomerName-softworks/2011/11/11 » :

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

|TableName|TableType|Dossier|DocString (en)|Propriétés|ConnectionStrings|Partitions|
|---|---|---|---|---|---|---|
|ExternalMultiplePartitions|Objet blob|ExternalTables (en)|Docs|"Format":"Csv","Compressed":false,"CompressionType":null,"FileExtension":"csv","IncludeHeaders":"None","Encoding":null,"NamePrefix":null|["https://storageaccount.blob.core.windows.net/container1;*******"]}|["StringFormat":"CustomerName"","ColumnName":"CustomerName","Ordinal":0',PartitionBy":"1.00:00:00","ColumnName":"Timestamp","Ordinal":1']{0}|

#### <a name="spark-virtual-columns-support"></a>Support de colonnes virtuelles Spark

Lorsque les données sont exportées à partir de Spark, `partitionBy` les colonnes de partition (qui sont spécifiées dans la méthode de l’auteur de dataframe) ne sont pas écrites dans des fichiers de données. Ceci est fait pour éviter la duplication des données parce que `column1=<value>/column2=<value>/`les données déjà présentes dans les noms de «dossier» (par exemple, ), et Spark peut le reconnaître à la lecture. Cependant, Kusto exige que les colonnes de partition soient présentes dans les données elles-mêmes. Le support pour les colonnes virtuelles dans Kusto est prévu. D’ici là, utilisez la solution de contournement suivante : lors de l’exportation de données de Spark, créez une copie de toutes les colonnes par lesquelles les données sont partitionnées avant d’écrire un dataframe :

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Lors de la définition d’une table externe dans Kusto spécifier des colonnes de partition comme dans l’exemple suivant:

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

### <a name="show-external-table-artifacts"></a>.montrer des artefacts de table externes

* Renvoie une liste de tous les artefacts qui seront traités lors de la requête d’une table externe donnée.
* Nécessite la [permission de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show`Nom *de la table* `external` `table``artifacts`

**Sortie**

| Paramètre de sortie | Type   | Description                       |
|------------------|--------|-----------------------------------|
| Uri              | string | URI d’artefact de stockage externe |

**Exemples :**

```kusto
.show external table T artifacts
```

**Output:**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| https://storageaccount.blob.core.windows.net/container1/folder/file.csv |

### <a name="create-external-table-mapping"></a>.créer la cartographie externe des tables

`.create``external` `json` `mapping` *MappingName* *MappingInJsonFormat* *ExternalTableName* ExternalTableName MappingName MappingInJsonFormat `table`

Crée une nouvelle cartographie. Pour plus d’informations, voir [Data Mappings](./mappings.md#json-mapping).

**Exemple** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Exemple de sortie**

| Nom     | Type | Mappage                                                           |
|----------|------|-------------------------------------------------------------------|
| cartographie1 | JSON | ["ColumnName":"rownumber","ColumnType":"int","Properties":"Path":"$.rownumber"', "ColumnName":"rowguid","ColumnType":"","Properties":"Path":"$.rowguid" |

### <a name="alter-external-table-mapping"></a>.modifier la cartographie externe des tables

`.alter``external` `json` `mapping` *MappingName* *MappingInJsonFormat* *ExternalTableName* ExternalTableName MappingName MappingInJsonFormat `table`

Modifie une cartographie existante. 
 
**Exemple** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Exemple de sortie**

| Nom     | Type | Mappage                                                                |
|----------|------|------------------------------------------------------------------------|
| cartographie1 | JSON | ["ColumnName":"rownumber","ColumnType":"""""Properties":"Path":"$.rownumber"', "ColumnName":"rowguid","ColumnType":"","Properties":"Path":"$.rowguid"' |

### <a name="show-external-table-mappings"></a>.afficher les cartographies externes de table

`.show``external` *ExternalTableName* `json` `mapping` *MappingName* ExternalTableName MappingNameName `table` 

`.show``external` `json` ExternalTableName *(en)* `table``mappings`

Afficher les cartes (tout ou celle spécifiée par son nom).
 
**Exemple** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**Exemple de sortie**

| Nom     | Type | Mappage                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| cartographie1 | JSON | ["ColumnName":"rownumber","ColumnType":"""""Properties":"Path":"$.rownumber"', "ColumnName":"rowguid","ColumnType":"","Properties":"Path":"$.rowguid"' |

### <a name="drop-external-table-mapping"></a>.drop cartographie de table externe

`.drop``external` *ExternalTableName* `json` `mapping` *MappingName* ExternalTableName MappingNameName `table` 

Laisse tomber la cartographie de la base de données.
 
**Exemple** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>Table SQL externe

### <a name="create-or-alter-external-sql-table"></a>.créer ou modifier la table sql externe

Crée ou modifie une table SQL externe dans la base de données dans laquelle la commande est exécutée.  

**Syntaxe**

`.create` | (`.alter` `external` ) `table` *TableName* ([columnName:columnType], ...)  
`kind` `=` `sql`  
`table``=` *SqlTableName (en anglais)*  
`(`*SqlServerConnectionString*`)`  
[`with` `(``docstring` `=` *Documentation*]`,` `folder` `=` [ *FolderName*], *property_name* `=` *valeur*`,`... `)`]


**Paramètres:**

* *TableName* - Nom de table externe. Doit suivre les règles pour les [noms d’entités](../query/schema-entities/entity-names.md). Une table externe ne peut pas avoir le même nom qu’une table ordinaire dans la même base de données.
* *SqlTableName* - Le nom de la table SQL.
* *SqlServerConnectionString* - La chaîne de connexion au serveur SQL. Il peut s'agir d'une des méthodes suivantes : 
    * **Authentification intégrée AAD** (`Authentication="Active Directory Integrated"`) : L’utilisateur ou l’application authentifie via AAD à Kusto, et le même jeton est ensuite utilisé pour accéder au critère de terminaison du réseau SQL Server.
    * **Nom d’utilisateur/authentification par mot de passe** (`User ID=...; Password=...;`). Si la table externe est utilisée pour [l’exportation continue,](data-export/continuous-data-export.md)l’authentification doit être effectuée en utilisant cette méthode. 

> [!WARNING]
> Les chaînes de connexion et les requêtes qui comprennent des informations confidentielles doivent être obscurcies de sorte qu’elles soient omises de tout tracé Kusto. Voir [les littérals de cordes obscurcis](../query/scalar-data-types/string.md#obfuscated-string-literals) pour plus d’informations.

**Propriétés facultatives**
| Propriété            | Type            | Description                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Le dossier de la table.                  |
| `docString`         | `string`        | Une chaîne documentant la table.      |
| `firetriggers`      | `true`/`false`  | Si `true`, demande au système cible de tirer des déclencheurs INSERT définis sur la table SQL. Par défaut, il s’agit de `false`. (Pour plus d’informations voir [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) et [System.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)) |
| `createifnotexists` | `true`/ `false` | Si `true`, la table SQL cible sera créée si elle n’existe pas déjà; la `primarykey` propriété doit être fournie dans ce cas pour indiquer la colonne de résultat qui est la clé principale. Par défaut, il s’agit de `false`.  |
| `primarykey`        | `string`        | Si `createifnotexists` `true`c’est , indique le nom de la colonne dans le résultat qui sera utilisé comme clé principale de la table SQL si elle est créée par cette commande.                  |

> [!NOTE]
> * Si le tableau existe, la `.create` commande échouera avec une erreur. Utiliser `.alter` pour modifier les tables existantes. 
> * La modification du schéma ou du format d’une table SQL externe n’est pas prise en charge. 

Nécessite [la permission de l’utilisateur de base de données](../management/access-control/role-based-authorization.md) pour `.create` et la permission [d’administration de table](../management/access-control/role-based-authorization.md) pour `.alter`. 
 
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

| TableName   | TableType | Dossier         | DocString (en) | Propriétés                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| ExterneSql | SQL       | ExternalTables (en) | Docs      | {<br>  "TargetEntityKind": "sqltable",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString": "Server-tcp:myserver.database.windows.net,1433; Authentification-Annuaire actif Intégré;Catalogue initial-mydatabase;,<br>  "FireTriggers": vrai,<br>  "CreateIfNotExists": vrai,<br>  "PrimaryKey": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>Requête d’une table externe de type SQL 
Semblable à d’autres types de tables externes, la requête d’une table SQL externe est soutenue. Voir [les tables externes de requête](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data). Notez que la mise en œuvre de la requête externe SQL exécutera un « SELECT » complet (ou sélectionner des colonnes pertinentes) à partir de la table SQL, tandis que le reste de la requête s’exécutera du côté de Kusto. Considérez la requête externe suivante : 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto exécutera une requête ' SELECT ' from TABLE' à la base de données SQL, suivie d’un compte sur le côté kusto. Dans de tels cas, on s’attend à ce que les performances soient meilleures si elles sont écrites directement dans T-SQL (« SELECT COUNT(1) FROM TABLE ») et exécutées à l’aide du [plugin sql_request,](../query/sqlrequestplugin.md)au lieu d’utiliser la fonction de table externe. De même, les filtres ne sont pas poussés à la requête SQL.  
Il est recommandé d’utiliser la table externe pour interroger la table SQL lorsque la requête nécessite la lecture de la table entière (ou des colonnes pertinentes) pour une exécution ultérieure du côté de Kusto. Lorsqu’une requête SQL peut être considérablement optimisée dans T-SQL, utilisez le [plugin sql_request.](../query/sqlrequestplugin.md)

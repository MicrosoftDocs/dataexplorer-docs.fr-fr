---
title: Table SQL externe-Azure Explorateur de données
description: Cet article décrit la création d’une table SQL externe dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 9de01863ddb832421c5b6dbfe65b8ae451382fb7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373366"
---
# <a name="external-sql-table"></a>Table SQL externe

Crée ou modifie une table SQL externe dans la base de données dans laquelle la commande est exécutée.  

## <a name="syntax"></a>Syntaxe

( `.create`  |  `.alter` ) `external` `table` *TableName* ([ColumnName : ColumnType],...)  
`kind` `=` `sql`  
`table``=` *SqlTableName*  
`(`*SqlServerConnectionString*`)`  
[ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*], *property_name* `=` *valeur* `,` ... `)` ]

## <a name="parameters"></a>Paramètres

* *TableName* -nom de la table externe. Doit suivre les règles pour les [noms d’entité](../query/schema-entities/entity-names.md). Une table externe ne peut pas avoir le même nom qu’une table normale dans la même base de données.
* *SqlTableName* : nom de la table SQL.
* *SqlServerConnectionString* : chaîne de connexion au serveur SQL Server. Il peut s’agir de l’une des méthodes suivantes : 
  * *Authentification intégrée à AAD* ( `Authentication="Active Directory Integrated"` ) : l’utilisateur ou l’application s’authentifie via AAD sur Kusto, et le même jeton est ensuite utilisé pour accéder au point de terminaison du réseau SQL Server.
  * *Authentification par nom d’utilisateur/mot de passe* ( `User ID=...; Password=...;` ). Si la table externe est utilisée pour l' [exportation continue](data-export/continuous-data-export.md), l’authentification doit être effectuée à l’aide de cette méthode. 

> [!WARNING]
> Les chaînes de connexion et les requêtes qui incluent des informations confidentielles doivent être obscurcies pour qu’elles soient omises de tout suivi Kusto. Pour plus d’informations, consultez [littéraux de chaîne obscurcis](../query/scalar-data-types/string.md#obfuscated-string-literals).

## <a name="optional-properties"></a>Propriétés facultatives

| Propriété            | Type            | Description                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Dossier de la table.                  |
| `docString`         | `string`        | Chaîne qui documente la table.      |
| `firetriggers`      | `true`/`false`  | Si `true` la valeur est, indique au système cible d’activer les déclencheurs d’insertion définis sur la table SQL. Par défaut, il s’agit de `false`. (Pour plus d’informations, consultez [Bulk Insert](https://msdn.microsoft.com/library/ms188365.aspx) et [System. Data. SqlClient. SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)) |
| `createifnotexists` | `true`/ `false` | Si `true` la valeur est, la table SQL cible est créée si elle n’existe pas déjà ; la `primarykey` propriété doit être fournie dans ce cas pour indiquer la colonne de résultats qui est la clé primaire. Par défaut, il s’agit de `false`.  |
| `primarykey`        | `string`        | Si `createifnotexists` est `true` , le nom de colonne résultant sera utilisé comme clé primaire de la table SQL s’il est créé par cette commande.                  |

> [!NOTE]
> * Si la table existe, la `.create` commande échoue et génère une erreur. Utilisez `.alter` pour modifier des tables existantes. 
> * La modification du schéma ou du format d’une table SQL externe n’est pas prise en charge. 

Requiert l' [autorisation utilisateur de base de données](../management/access-control/role-based-authorization.md) pour `.create` et l’autorisation d’administrateur de [table](../management/access-control/role-based-authorization.md) pour `.alter` . 
 
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
| ExternalSql | SQL       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable'",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString" : "Server = TCP :myserver. Database. Windows. net, 1433 ; Authentication = Active Directory intégré ; initial catalog = mabdd ;»,<br>  « FireTriggers » : true,<br>  « CreateIfNotExists » : true,<br>  « PrimaryKey » : « x »<br>} |

## <a name="querying-an-external-table-of-type-sql"></a>Interrogation d’une table externe de type SQL 

L’interrogation d’une table SQL externe est prise en charge. Consultez [interrogation de tables externes](../../data-lake-query-data.md). 

> [!Note]
> L’implémentation de la requête de table externe SQL exécute un « SELECT * » complet (ou sélectionne des colonnes appropriées) dans la table SQL. Le reste de la requête s’exécute du côté Kusto. 

Examinez la requête de table externe suivante : 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto exécute une requête’SELECT * FROM TABLE’dans la base de données SQL, suivie d’un décompte du côté Kusto. Dans ce cas, les performances devraient être meilleures si elles sont écrites directement dans T-SQL (« SELECT COUNT (1) FROM TABLE ») et exécutées à l’aide du [plug-in sql_request](../query/sqlrequestplugin.md), au lieu d’utiliser la fonction de table externe. De même, les filtres ne sont pas transmis à la requête SQL.  

Utilisez la table externe pour interroger la table SQL lorsque celle-ci requiert la lecture de la totalité de la table (ou des colonnes appropriées) pour une exécution ultérieure côté Kusto. Quand une requête SQL peut être optimisée dans T-SQL, utilisez le [plug-in sql_request](../query/sqlrequestplugin.md).

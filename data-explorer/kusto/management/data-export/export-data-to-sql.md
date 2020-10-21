---
title: Exporter des données vers SQL-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’exportation de données vers SQL dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61c65e2667356f2b2ee9e53c3dd1c210e84c72f8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342805"
---
# <a name="export-data-to-sql"></a>Exporter des données vers SQL

L’exportation de données vers SQL vous permet d’exécuter une requête et d’envoyer ses résultats à une table dans une base de données SQL, telle qu’une base de données SQL hébergée par le service Azure SQL Database.

**Syntaxe**

`.export`[ `async` ] `to` `sql` *SqlTableName* *sqlConnectionString* [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] `<|` *Requête*

Où :
* *Async*: la commande s’exécute en mode asynchrone (facultatif).
* *SqlTableName* Nom de la table de base de données SQL dans laquelle les données sont insérées.
  Pour vous protéger contre les attaques par injection, ce nom est restreint.
* *SqlConnectionString* est un `string` littéral qui suit le `ADO.NET` format de chaîne de connexion et décrit le point de terminaison SQL et la base de données auxquels vous vous connectez. Pour des raisons de sécurité, la chaîne de connexion est limitée.
* *PropertyName*, *PropertyValue* sont des paires d’un nom (identificateur) et d’une valeur (littéral de chaîne).

Propriétés :

|Nom               |Valeurs           |Description|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` ou `false`|Si `true` la valeur est, indique au système cible d’activer les déclencheurs d’insertion définis sur la table SQL. Par défaut, il s’agit de `false`. (Pour plus d’informations, consultez [Bulk Insert](/sql/t-sql/statements/bulk-insert-transact-sql) et [System. Data. SqlClient. SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy))|
|`createifnotexists`|`true` ou `false`|Si `true` la valeur est, la table SQL cible est créée si elle n’existe pas déjà ; la `primarykey` propriété doit être fournie dans ce cas pour indiquer la colonne de résultats qui est la clé primaire. Par défaut, il s’agit de `false`.|
|`primarykey`       |                 |Si `createifnotexists` a la valeur `true` , indique le nom de la colonne dans le résultat qui sera utilisé comme clé primaire de la table SQL s’il est créé par cette commande.|
|`persistDetails`   |`bool`           |Indique que la commande doit conserver ses résultats (consultez `async` flag). La valeur par défaut `true` est dans les exécutions asynchrones, mais peut être désactivée si l’appelant n’a pas besoin des résultats. La valeur par défaut est `false` dans les exécutions synchrones, mais peut être activée. |
|`token`            |`string`         |Le jeton d’accès AAD que Kusto transmet au point de terminaison SQL pour l’authentification. Lorsque cette valeur est définie, la chaîne de connexion SQL ne doit pas inclure d’informations d’authentification telles que `Authentication` , `User ID` ou `Password` .|

## <a name="limitations-and-restrictions"></a>Limitations et restrictions

Il existe un certain nombre de limitations et de restrictions lors de l’exportation de données vers une base de données SQL :

1. Kusto étant un service Cloud, la chaîne de connexion doit pointer vers une base de données accessible à partir du Cloud. (En particulier, un ne peut pas exporter vers une base de données locale, car il n’est pas accessible à partir du cloud public.)

2. Kusto prend en charge Active Directory l’authentification intégrée lorsque le principal appelant est un Azure Active Directory principal ( `aaduser=` ou `aadapp=` ).
   Kusto prend également en charge la fourniture des informations d’identification pour la base de données SQL dans le cadre de la chaîne de connexion. Les autres méthodes d’authentification ne sont pas prises en charge. L’identité présentée à la base de données SQL émane toujours de l’appelant de commande et non de l’identité de service Kusto elle-même.

3. Si la table cible de la base de données SQL existe, elle doit correspondre au schéma de résultat de la requête. Notez que dans certains cas (par exemple, Azure SQL Database), cela signifie que la table possède une colonne marquée comme colonne d’identité.

4. L’exportation de grands volumes de données peut prendre beaucoup de temps. Il est recommandé de définir la table SQL cible pour une journalisation minimale lors de l’importation en bloc.
   Pour plus [d’informations sur les > fonctionnalités de base de données, consultez SQL Server Moteur de base de données > > et l’importation et l’exportation de données en bloc](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import).

5. L’exportation de données s’effectue à l’aide de la copie en bloc SQL et ne fournit aucune garantie transactionnelle sur la base de données SQL cible. Pour plus d’informations [, consultez transactions et opérations de copie en bloc](/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations) .

6. Le nom de la table SQL est limité à un nom composé de lettres, de chiffres, d’espaces, de traits de soulignement ( `_` ), de points ( `.` ) et de traits d’Union ( `-` ).

7. La chaîne de connexion SQL est limitée comme suit : `Persist Security Info` est défini explicitement sur `false` , a la valeur `Encrypt` `true` et `Trust Server Certificate` a `false` la valeur.

8. La propriété de clé primaire de la colonne peut être spécifiée lors de la création d’une nouvelle table SQL. Si la colonne est de type `string` , SQL peut refuser de créer la table en raison d’autres limitations sur la colonne de clé primaire. La solution de contournement consiste à créer manuellement la table dans SQL avant d’exporter les données. La raison de cette limitation est que les colonnes clés primaires dans SQL ne peuvent pas être de taille illimitée, mais que les colonnes de la table Kusto n’ont aucune limite de taille déclarée.

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Documentation sur l’authentification intégrée Azure DB AAD

* [Utilisez l’authentification Azure Active Directory pour l’authentification avec SQL Database](/azure/sql-database/sql-database-aad-authentication)
* [Azure AD des extensions d’authentification pour Azure SQL DB et SQL DW Tools] (https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**Exemples** 

Dans cet exemple, Kusto exécute la requête, puis exporte le premier jeu d’enregistrements généré par la requête vers la `MySqlTable` table de la `MyDatabase` base de données dans le serveur `myserver` .

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

Dans cet exemple, Kusto exécute la requête, puis exporte le premier jeu d’enregistrements généré par la requête vers la `MySqlTable` table de la `MyDatabase` base de données dans le serveur `myserver` .
Si la table cible n’existe pas dans la base de données cible, elle est créée.

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```
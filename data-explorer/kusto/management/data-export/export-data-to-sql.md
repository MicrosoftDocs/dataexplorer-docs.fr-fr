---
title: Données d’exportation vers SQL - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les données d’exportation à SQL dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7deeb3868800f00a828eb09cc605e4d7e5227032
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521622"
---
# <a name="export-data-to-sql"></a>Données d’exportation vers SQL

Les données d’exportation vers SQL vous permettent d’exécuter une requête et de faire envoyer ses résultats à un tableau dans une base de données SQL, comme une base de données SQL hébergée par le service de base de données SqL Azure.

**Syntaxe**

`.export`[`async` `to` `sql` ] *SqlTableName* *SqlConnectionString* `with` `(`[ *PropertyName* `=` *PropertyValue*`,`... `)`] `<|` *Requête*

Où :
* *async*: Commande fonctionne en mode asynchrone (facultatif).
* *SqlTableName (en anglais)* Nom de table de base de données SQL où les données sont insérées.
  Pour se protéger contre les attaques par injection, ce nom est restreint.
* *SqlConnectionString* est `string` un littéral qui suit le `ADO.NET` format de chaîne de connexion et décrit le point de terminaison SQL et la base de données à laquelle vous vous connectez. Pour des raisons de sécurité, la chaîne de connexion est limitée.
* *PropertyName*, *PropertyValue* sont des paires d’un nom (identifiant) et une valeur (string littérale).

Propriétés :

|Nom               |Valeurs           |Description|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` ou `false`|Si `true`, demande au système cible de tirer des déclencheurs INSERT définis sur la table SQL. Par défaut, il s’agit de `false`. (Pour plus d’informations voir [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) et [System.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx))|
|`createifnotexists`|`true` ou `false`|Si `true`, la table SQL cible sera créée si elle n’existe pas déjà; la `primarykey` propriété doit être fournie dans ce cas pour indiquer la colonne de résultat qui est la clé principale. Par défaut, il s’agit de `false`.|
|`primarykey`       |                 |Si `createifnotexists` `true`c’est , indique le nom de la colonne dans le résultat qui sera utilisé comme clé principale de la table SQL si elle est créée par cette commande.|
|`persistDetails`   |`bool`           |Indique que la commande doit `async` persister ses résultats (voir drapeau). Par défaut `true` dans les exécutions async, mais peut être désactivé si l’appelant n’a pas besoin des résultats). Par défaut `false` dans les exécutions synchrones, mais peut être activé. |
|`token`            |`string`         |Le jeton d’accès AAD que Kusto transmettra au point de terminaison SQL pour l’authentification. Lorsqu’elle est réglée, la chaîne de connexion `Authentication` `User ID`SQL `Password`ne doit pas inclure d’informations d’authentification comme, , ou .|

## <a name="limitations-and-restrictions"></a>Limitations et restrictions

Il y a un certain nombre de limites et de restrictions lors de l’exportation de données vers une base de données SQL :

1. Kusto est un service cloud, de sorte que la chaîne de connexion doit indiquer une base de données accessible à partir du cloud. (En particulier, on ne peut pas exporter vers une base de données sur place puisqu’elle n’est pas accessible depuis le cloud public.)

2. Kusto prend en charge Active Directory Integrated authentification lorsque`aaduser=` `aadapp=`le directeur d’appel est un directeur d’annuaire actif Azure ( ou ).
   Par ailleurs, Kusto prend également en charge la fourniture des informations d’identification de la base de données SQL dans le cadre de la chaîne de connexion. D’autres méthodes d’authentification ne sont pas prises en charge. L’identité présentée à la base de données SQL émane toujours de l’appelant de commande et non de l’identité de service Kusto elle-même.

3. Si le tableau cible de la base de données SQL existe, il doit correspondre au schéma de résultat de requête. Notez que dans certains cas (comme Azure SQL Database) cela signifie que le tableau a une colonne marquée comme une colonne d’identité.

4. L’exportation d’importants volumes de données peut prendre beaucoup de temps. Il est recommandé que la table SQL cible soit définie pour un minimum d’exploitation forestière pendant l’importation en vrac.
   Voir [SQL Server Database Engine > ... > Database Features > Bulk Import and Export of Data](https://msdn.microsoft.com/library/ms190422.aspx).

5. L’exportation de données est effectuée à l’aide de copie en vrac SQL et ne fournit aucune garantie transactionnelle sur la base de données SQL cible. Voir [Transaction et Opérations de copie en vrac](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations) pour plus de détails.

6. Le nom de table SQL est limité à un nom composé`_`de lettres, chiffres, espaces, souligne ( ), points (`.`) et traits d’union (`-`).

7. La chaîne de connexion SQL `Persist Security Info` est limitée `false` `Encrypt` comme suit: est explicitement réglé à , est fixé à `true`, et `Trust Server Certificate` est fixé à `false`.

8. La propriété principale clé sur la colonne peut être spécifiée lors de la création d’une nouvelle table SQL. Si la colonne `string`est de type, puis SQL pourrait refuser de créer la table en raison d’autres limitations sur la colonne principale de clé. La solution de contournement consiste à créer manuellement la table dans SQL avant d’exporter les données. La raison de cette limitation est que les colonnes clés primaires dans SQL ne peut pas être de taille illimitée, mais les colonnes de table Kusto n’ont pas de limites de taille déclarées.

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Azure DB AAD Integrated Authentication Documentation

* [Utilisez l’authentification azure Active Directory pour l’authentification avec SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
* [Extensions d’authentification Azure AD pour les outils Azure SQL DB et SQL DW] (https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**Exemples** 

Dans cet exemple, Kusto exécute la requête, puis exporte le `MySqlTable` premier enregistrement `MyDatabase` produit `myserver`par la requête à la table dans la base de données dans le serveur .

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

Dans cet exemple, Kusto exécute la requête, puis exporte le `MySqlTable` premier enregistrement `MyDatabase` produit `myserver`par la requête à la table dans la base de données dans le serveur .
Si le tableau cible n’existe pas dans la base de données cible, il est créé.

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```
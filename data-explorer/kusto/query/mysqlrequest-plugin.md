---
title: plug-in mysql_request-Azure Explorateur de données
description: Cet article décrit mysql_request plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c44935a98110cd47f2a40bb261659e12627860c0
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422083"
---
# <a name="mysql_request-plugin-preview"></a>plug-in mysql_request (version préliminaire)

::: zone pivot="azuredataexplorer"

Le `mysql_request` plug-in envoie une requête SQL à un point de terminaison réseau MySQL Server et retourne le premier ensemble de lignes dans les résultats. La requête peut retourner plus d’un ensemble de lignes, mais seul le premier ensemble de lignes est rendu disponible pour le reste de la requête Kusto.

> [!IMPORTANT]
> Le `mysql_request` plug-in est en mode Aperçu et est désactivé par défaut.
> Pour activer le plug-in, exécutez la [ `.enable plugin mysql_request` commande](../management/enable-plugin.md). Pour voir quels plug-ins sont activés, utilisez [. afficher les commandes de gestion de plug-in](../management/show-plugins.md).

## <a name="syntax"></a>Syntaxe

`evaluate``mysql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *SqlParameters* ]`)`

## <a name="arguments"></a>Arguments

Nom | Type | Description | Obligatoire ou facultatif |
---|---|---|---
| *ConnectionString* | `string` literal | Indique la chaîne de connexion qui pointe vers le point de terminaison réseau du serveur MySQL. Consultez la page [authentification](#username-and-password-authentication) et comment spécifier le [point de terminaison réseau](#specify-the-network-endpoint). | Obligatoire |
| *SqlQuery* | `string` literal | Indique la requête qui doit être exécutée sur le point de terminaison SQL. Doit retourner un ou plusieurs ensembles de lignes, mais seul le premier est rendu disponible pour le reste de la requête Kusto. | Obligatoire|
| *SqlParameters* | Valeur constante de type `dynamic` | Contient des paires clé-valeur à passer comme paramètres avec la requête. | Facultatif |

## <a name="set-callout-policy"></a>Définir la stratégie de légende

Le plug-in fait des légendes à la base de donne MySql. Assurez-vous que la [stratégie de légende](../management/calloutpolicy.md) du cluster autorise les appels de type `mysql` au *MySqlDbUri* cible.

L’exemple suivant montre comment définir la stratégie de légende pour MySQL DB. Il est recommandé de limiter la stratégie de légende à des points de terminaison spécifiques ( `my_endpoint1` , `my_endpoint2` ).

```kusto
[
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint1\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint2\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  }
]
```

L’exemple suivant montre une commande de stratégie ALTER Callout pour `mysql` *CalloutType* :

```kusto
.alter cluster policy callout @'[{"CalloutType": "mysql", "CalloutUriRegex": "\\.mysql\\.database\\.azure\\.com", "CanCall": true}]'
```

## <a name="username-and-password-authentication"></a>Authentification du nom d’utilisateur et du mot de passe

Le plug-in mysql_request prend en charge uniquement l’authentification du nom d’utilisateur et du mot de passe sur le point de terminaison du serveur MySQL et ne s’intègre pas Azure Active Directory à l' 

Le nom d’utilisateur et le mot de passe sont fournis dans la chaîne de connexion à l’aide des paramètres suivants :

`User ID=...; Password=...;`
    
> [!WARNING]
> Les informations confidentielles ou protégées doivent être obscurcies à partir des chaînes de connexion et des requêtes afin qu’elles soient omises de tout suivi Kusto. Pour plus d’informations, consultez [littéraux de chaîne obscurcis](scalar-data-types/string.md#obfuscated-string-literals).

## <a name="encryption-and-server-validation"></a>Chiffrement et validation du serveur

Pour des raisons de sécurité, `SslMode` est défini sans condition `Required` lors de la connexion à un point de terminaison réseau MySQL Server. Par conséquent, le SQL Server doit être configuré avec un certificat de serveur SSL/TLS valide.

## <a name="specify-the-network-endpoint"></a>Spécifier le point de terminaison réseau

Spécifiez le point de terminaison réseau SQL dans le cadre de la chaîne de connexion.

**Syntaxe**  :

`Server``=` *Nom de domaine complet* [ `Port` `=` *port* ]

Où :

* *FQDN* est le nom de domaine complet du point de terminaison.
* *Port* est le port TCP du point de terminaison. Par défaut, `3306` est supposé.

## <a name="examples"></a>Exemples


### <a name="sql-query-to-azure-mysql-db"></a>Requête SQL vers Azure MySQL DB

L’exemple suivant envoie une requête SQL à une base de données Azure MySQL DB. Il récupère tous les enregistrements de `[dbo].[Table]` , puis traite les résultats.

> [!NOTE]
> Cet exemple ne doit pas être utilisé comme recommandation pour filtrer ou projeter des données de cette manière. Les requêtes SQL doivent être construites pour retourner le plus petit jeu de données possible, car l’optimiseur Kusto ne tente pas actuellement d’optimiser les requêtes entre Kusto et SQL.

```kusto
evaluate sql_request(
    'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-authentication-with-username-and-password"></a>Authentification SQL avec nom d’utilisateur et mot de passe

L’exemple suivant est identique au précédent, mais l’authentification SQL est effectuée par le nom d’utilisateur et le mot de passe. Pour des fins de confidentialité, nous utilisons des chaînes brouillées.

```kusto
evaluate sql_request(
   'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-query-to-azure-sql-db-with-modifications"></a>Requête SQL vers Azure SQL DB avec modifications

L’exemple suivant envoie une requête SQL à une base de données Azure SQL DB en extrayant tous les enregistrements de `[dbo].[Table]` , en ajoutant une autre `datetime` colonne, puis traite les résultats du côté Kusto.
Elle spécifie un paramètre SQL ( `@param0` ) à utiliser dans la requête SQL.

```kusto
evaluate mysql_request(
  'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end

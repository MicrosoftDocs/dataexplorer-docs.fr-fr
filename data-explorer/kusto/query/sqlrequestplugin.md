---
title: plug-in sql_request-Azure Explorateur de données
description: Cet article décrit sql_request plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1a6349547d5cf1eb3af5a21f6e8c504573f15e52
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241772"
---
# <a name="sql_request-plugin"></a>sql_request, plug-in

::: zone pivot="azuredataexplorer"

Le `sql_request` plug-in envoie une requête SQL à un point de terminaison de réseau SQL Server et retourne le premier ensemble de lignes dans les résultats.

## <a name="syntax"></a>Syntaxe

  `evaluate``sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *SqlParameters* [ `,` *options*]]`)`

## <a name="arguments"></a>Arguments

* *ConnectionString*: `string` littéral indiquant la chaîne de connexion qui pointe vers le point de terminaison du réseau SQL Server. Consultez [méthodes d’authentification valides](#authentication) et comment spécifier le [point de terminaison réseau](#specify-the-network-endpoint).

* *SqlQuery*: `string` littéral indiquant la requête à exécuter sur le point de terminaison SQL. Doit retourner un ou plusieurs ensembles de lignes, mais seul le premier est rendu disponible pour le reste de la requête Kusto.

* *SqlParameters*: valeur constante de type `dynamic` qui contient des paires clé-valeur à passer comme paramètres avec la requête. facultatif.
  
* *Options*: valeur constante de type `dynamic` qui contient des paramètres plus avancés en tant que paires clé-valeur. Actuellement, seul `token` peut être défini, pour transmettre un jeton d’accès Azure ad fourni par l’appelant, qui est transféré au point de terminaison SQL pour l’authentification. facultatif.

## <a name="examples"></a>Exemples

L’exemple suivant envoie une requête SQL à une base de données Azure SQL DB. Elle récupère tous les enregistrements de `[dbo].[Table]` , puis traite les résultats du côté Kusto. L’authentification réutilise le jeton de Azure AD de l’utilisateur appelant. 

> [!NOTE]
> Cet exemple ne doit pas être utilisé comme recommandation pour filtrer ou projeter des données de cette manière. Les requêtes SQL doivent être construites pour retourner le plus petit jeu de données possible, car l’optimiseur Kusto ne tente pas actuellement d’optimiser les requêtes entre Kusto et SQL.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

L’exemple suivant est identique au précédent, à ceci près que l’authentification SQL est effectuée par nom d’utilisateur/mot de passe. Pour des fins de confidentialité, nous utilisons ici des chaînes masquées.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Initial Catalog=Fabrikam;'
    h'User ID=USERNAME;'
    h'Password=PASSWORD;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

L’exemple suivant envoie une requête SQL à une base de données Azure SQL DB en extrayant tous les enregistrements de `[dbo].[Table]` , en ajoutant une autre `datetime` colonne, puis traite les résultats du côté Kusto.
Elle spécifie un paramètre SQL ( `@param0` ) à utiliser dans la requête SQL.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

## <a name="authentication"></a>Authentification

Le plug-in sql_request prend en charge trois méthodes d’authentification pour le point de terminaison de SQL Server :

### <a name="azure-ad-integrated-authentication"></a>Authentification intégrée à l’Azure AD 

`Authentication="Active Directory Integrated"`

  L’authentification intégrée à l’Azure AD est la méthode recommandée. Cette méthode permet à l’utilisateur ou à l’application de s’authentifier via Azure AD à Kusto. Le même jeton est ensuite utilisé pour accéder au point de terminaison du réseau SQL Server.

### <a name="usernamepassword-authentication"></a>Authentification par nom d’utilisateur/mot de passe

`User ID=...; Password=...;`

  La prise en charge de l’authentification par nom d’utilisateur et mot de passe est fournie lorsque l’authentification Azure AD intégrée ne peut pas être effectuée. Évitez cette méthode, dans la mesure du possible, car les informations secrètes sont envoyées par le biais de Kusto.

### <a name="azure-ad-access-token"></a>Azure AD jeton d’accès

`dynamic({'token': h"eyJ0..."})`

   Avec la méthode d’authentification par jeton d’accès Azure AD, l’appelant génère le jeton d’accès, qui est transféré par Kusto au point de terminaison SQL. La chaîne de connexion ne doit pas inclure d’informations d’authentification telles que `Authentication` , `User ID` ou `Password` . Au lieu de cela, le jeton d’accès est transmis en tant que `token` propriété dans l' `Options` argument du plug-in sql_request.
     
> [!WARNING]
> Les chaînes de connexion et les requêtes qui incluent des informations confidentielles ou des informations qui doivent être protégées doivent être obscurcies pour être omises de tout suivi Kusto.
> Pour plus d’informations, consultez [littéraux de chaîne obscurcis](scalar-data-types/string.md#obfuscated-string-literals).

## <a name="encryption-and-server-validation"></a>Chiffrement et validation du serveur

Les propriétés de connexion suivantes sont forcées lors de la connexion à un point de terminaison de réseau SQL Server, pour des raisons de sécurité.

* `Encrypt` a la valeur sans `true` condition.
* `TrustServerCertificate` a la valeur sans `false` condition.

Par conséquent, le SQL Server doit être configuré avec un certificat de serveur SSL/TLS valide.

## <a name="specify-the-network-endpoint"></a>Spécifier le point de terminaison réseau

La spécification du point de terminaison de réseau SQL dans le cadre de la chaîne de connexion est obligatoire.
La syntaxe appropriée est :

`Server``=` `tcp:` *Nom de domaine complet* [ `,` *port*]

Où :

* *FQDN* est le nom de domaine complet du point de terminaison.
* *Port* est le port TCP du point de terminaison. Par défaut, `1433` est supposé.

> [!NOTE]
> Les autres formes de spécification du point de terminaison réseau ne sont pas prises en charge.
> Vous ne pouvez pas omettre, par exemple, le préfixe, `tcp:` même s’il est possible de le faire en utilisant les bibliothèques clientes SQL par programmation.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end

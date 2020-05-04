---
title: plug-in sql_request-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit sql_request plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 725021ad8089d7e9ad4f897bd5a1c68f6912bf7a
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737281"
---
# <a name="sql_request-plugin"></a>plug-in sql_request

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `,` *SqlQuery* `,` *Options* *ConnectionString* `,` ConnectionString SqlQuery [SqlParameters [options]] *SqlParameters* `(``)`

Le `sql_request` plug-in envoie une requête SQL à un point de terminaison de réseau SQL Server et retourne le premier ensemble de lignes dans les résultats.

**Arguments**

* *ConnectionString*: `string` littéral indiquant la chaîne de connexion qui pointe vers le point de terminaison du réseau SQL Server. Voir ci-dessous pour connaître les méthodes d’authentification valides et spécifier le point de terminaison réseau.

* *SqlQuery*: `string` littéral indiquant la requête à exécuter sur le point de terminaison SQL. Doit retourner un ou plusieurs ensembles de lignes, mais seul le premier est rendu disponible pour le reste de la requête Kusto.

* *SqlParameters*: valeur constante de type `dynamic` qui contient des paires clé-valeur à passer comme paramètres avec la requête. facultatif.
  
* *Options*: valeur constante de type `dynamic` qui contient des paramètres plus avancés en tant que paires clé-valeur. Actuellement, `token` seul peut être défini, pour transmettre un jeton d’accès AAD fourni par l’appelant, qui est transféré au point de terminaison SQL pour l’authentification. facultatif.

**Exemples**

L’exemple suivant envoie une requête SQL à une base de données Azure SQL DB en extrayant tous `[dbo].[Table]`les enregistrements de, puis traite les résultats du côté Kusto. L’authentification réutilise le jeton AAD de l’utilisateur appelant.

Remarque : cet exemple ne doit pas être considéré comme une recommandation pour filtrer/projeter les données de cette manière. Il est généralement préférable que les requêtes SQL soient construites pour retourner le plus petit jeu de données possible, car l’optimiseur Kusto ne tente pas d’optimiser les requêtes entre Kusto et SQL.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

L’exemple suivant est identique au précédent, à ceci près que l’authentification SQL est effectuée par nom d’utilisateur/mot de passe. Notez que, pour la confidentialité, nous utilisons ici des chaînes obscurcies.

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

**Authentification**

Le plug-in sql_request prend en charge trois méthodes d’authentification pour le point de terminaison de SQL Server :

* **Authentification intégrée AAD** (`Authentication="Active Directory Integrated"`) : il s’agit de la méthode recommandée, dans laquelle l’utilisateur ou l’application s’authentifie via AAD sur Kusto, et le même jeton est utilisé pour accéder au point de terminaison du réseau SQL Server.

* **Authentification par nom d’utilisateur/mot de passe** (`User ID=...; Password=...;`) : la prise en charge de cette méthode est fournie lorsque l’authentification intégrée AAD ne peut pas être effectuée. Évitez cette méthode, dans la mesure du possible, car les informations secrètes sont envoyées par le biais de Kusto.

* **Jeton d’accès AAD** (`dynamic({'token': h"eyJ0..."})`) : avec cette méthode d’authentification, le jeton d’accès est généré par l’appelant et transféré par Kusto au point de terminaison SQL. La chaîne de connexion ne doit pas inclure d' `Authentication`informations `User ID`d’authentification `Password`telles que, ou. Au lieu de cela, le jeton d' `token` accès est transmis `Options` en tant que propriété dans l’argument du plug-in sql_request.
     
> [!WARNING]
> Les chaînes de connexion et les requêtes qui incluent des informations confidentielles ou des informations qui doivent être protégées doivent être obscurcies afin qu’elles soient omises de tout suivi Kusto.
> Pour plus d’informations, consultez [littéraux de chaîne obscurcis](scalar-data-types/string.md#obfuscated-string-literals) .

**Chiffrement et validation du serveur**

Les propriétés de connexion suivantes sont forcées lors de la connexion à un point de terminaison de réseau SQL Server, pour des raisons de sécurité :

* `Encrypt`a la valeur `true` sans condition.
* `TrustServerCertificate`a la valeur `false` sans condition.

Par conséquent, le SQL Server doit être configuré avec un certificat de serveur SSL/TLS valide.

**Spécification du point de terminaison réseau**

La spécification du point de terminaison de réseau SQL dans le cadre de la chaîne de connexion est obligatoire.
La syntaxe appropriée est :

`Server``=` *FQDN* `,` *Port*Nom de domaine complet [port] `tcp:`

Où :

* *FQDN* est le nom de domaine complet du point de terminaison.

* *Port* est le port TCP du point de terminaison. Par défaut, `1433` est supposé.

> [!NOTE]
> Les autres formes de spécification du point de terminaison réseau ne sont pas prises en charge.
> Vous ne pouvez pas omettre, par exemple, `tcp:` le préfixe, même s’il est possible de le faire en utilisant les bibliothèques clientes SQL par programmation.



::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end

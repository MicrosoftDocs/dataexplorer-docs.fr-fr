---
title: sql_request plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit sql_request plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f0c0837c6bb8e4dcd3cf2e28af18d02c19edb676
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766179"
---
# <a name="sql_request-plugin"></a>plug-in sql_request

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `,` *Options*`,` *ConnectionString* `,` *SqlQuery* ConnectionString SqlQuery [ *SqlParameters* [ Options ]] `(``)`

Le `sql_request` plugin envoie une requête SQL à un point de terminaison du réseau SQL Server et renvoie le premier jeu de ligne dans les résultats.

**Arguments**

* *ConnectionString*: `string` Un littéral indiquant la chaîne de connexion qui pointe vers le point de terminaison du réseau SQL Server. Voir ci-dessous pour les méthodes valides d’authentification et comment spécifier le point de terminaison du réseau.

* *SqlQuery*: `string` Un littéral indiquant la requête qui doit être exécutée contre le critère de terminaison SQL. Doit retourner un ou plusieurs rames, mais seul le premier est mis à disposition pour le reste de la requête Kusto.

* *SqlParameters*: Une valeur `dynamic` constante de type qui détient les paires de valeur clé à passer comme paramètres avec la requête. facultatif.
  
* *Options*: Une valeur `dynamic` constante de type qui contient des paramètres plus avancés en tant que paires de valeur clé. Actuellement, `token` on ne peut le régler que pour passer un jeton d’accès AAD fourni par l’appelant qui est transmis au point de terminaison SQL pour l’authentification. facultatif.

**Exemples**

L’exemple suivant envoie une requête SQL à une base de données `[dbo].[Table]`Azure SQL DB récupérant tous les enregistrements à partir de , puis traite les résultats du côté kusto. L’authentification réutilise le jeton AAD de l’utilisateur d’appel.

Remarque : Cet exemple ne doit pas être considéré comme une recommandation de filtrer/projeter les données de cette manière. Il est généralement préférable que des requêtes SQL seront construites pour retourner le plus petit ensemble de données possible, car actuellement l’optimiseur Kusto ne tente pas d’optimiser les requêtes entre Kusto et SQL.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

L’exemple suivant est identique à celui précédent, sauf que l’authentification SQL se fait par nom d’utilisateur/mot de passe. Notez que pour la confidentialité, nous utilisons des cordes obscurcies ici.

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

Le plugin sql_request prend en charge trois méthodes d’authentification au point de terminaison SQL Server :

* **Authentification intégrée AAD** (`Authentication="Active Directory Integrated"`): C’est la méthode préférée, dans laquelle l’utilisateur ou l’application authentifie via AAD à Kusto, et le même jeton est ensuite utilisé pour accéder au critère de terminaison du réseau SQL Server.

* **Authentification du nom d’utilisateur/mot de passe** ()`User ID=...; Password=...;`: La prise en charge de cette méthode est fournie lorsque l’authentification intégrée AAD ne peut pas être effectuée. Évitez cette méthode, si possible, que les informations secrètes sont envoyées par Kusto.

* **AAD access token** (`dynamic({'token': h"eyJ0..."})`): Avec cette méthode d’authentification, le jeton d’accès est généré par l’appelant et transmis par Kusto au point de terminaison SQL. La chaîne de connexion ne `Authentication`doit `User ID`pas `Password`inclure des informations d’authentification comme , , ou . Au lieu de cela, `token` le jeton d’accès est passé comme propriété dans l’argument `Options` du plugin sql_request.
     
> [!WARNING]
> Les chaînes de connexion et les requêtes qui comprennent des informations confidentielles ou des informations qui doivent être gardées doivent être obscurcies de sorte qu’elles soient omises de tout traçage Kusto.
> Voir [les littérals de cordes obscurcis](scalar-data-types/string.md#obfuscated-string-literals) pour plus de détails.

**Chiffrement et validation du serveur**

Les propriétés de connexion suivantes sont forcées lors de la connexion à un point de terminaison du réseau SQL Server, pour des raisons de sécurité :

* `Encrypt`est réglé `true` sans condition.
* `TrustServerCertificate`est réglé `false` sans condition.

Par conséquent, le serveur SQL doit être configuré avec un certificat serveur SSL/TLS valide.

**Spécifier le critère de terminaison du réseau**

Il est obligatoire de spécifier le critère d’évaluation du réseau SQL dans le cadre de la chaîne de connexion.
La syntaxe appropriée est :

`Server``=` *FQDN* `,` *Port*FQDN [ Port ] `tcp:`

Où :

* *FQDN* est le nom de domaine entièrement qualifié du point de terminaison.

* *Port* est le port TCP du point de terminaison. Par défaut, `1433` est supposé.

> [!NOTE]
> D’autres formes de spécifier le critère de terminaison du réseau ne sont pas prises en charge.
> On ne peut pas omettre, par exemple, le préfixe `tcp:` même s’il est possible de le faire lors de l’utilisation des bibliothèques client SQL programmatique.



::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end

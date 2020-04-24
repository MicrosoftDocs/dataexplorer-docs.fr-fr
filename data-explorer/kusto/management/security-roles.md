---
title: Gestion des rôles de sécurité - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des rôles de sécurité dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea56911ff2ad320d1070da2a4b92d94f060273cf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520092"
---
# <a name="security-roles-management"></a>Gestion des rôles de sécurité

> [!IMPORTANT]
> Avant de modifier les règles d’autorisation sur votre cluster Kusto, lisez ce qui suit : Autorisation basée sur le rôle de contrôle 
> [d’accès](../management/access-control/role-based-authorization.md) [Kusto](../management/access-control/index.md) 

Cet article décrit les commandes de contrôle utilisées pour gérer les rôles de sécurité.
Les rôles de sécurité définissent les principaux de sécurité (utilisateurs et applications) qui ont les autorisations d’opérer sur une ressource sécurisée comme une base de données ou une table, et quelles opérations sont autorisées. Par exemple, les directeurs qui ont le `database viewer` rôle de sécurité pour une base de données spécifique peuvent interroger et afficher toutes les entités de cette base de données (à l’exception des tableaux restreints).

Le rôle de sécurité peut être associé aux directeurs de sécurité ou aux groupes de sécurité (qui peuvent avoir d’autres directeurs de sécurité ou d’autres groupes de sécurité). Lorsqu’un principal de sécurité tente de faire une opération sur une ressource sécurisée, le système vérifie que le principal est associé à au moins un rôle de sécurité qui accorde des autorisations pour effectuer cette opération sur la ressource. C’est ce qu’on appelle un **contrôle d’autorisation**. L’échec du contrôle d’autorisation annule l’opération.

**Syntaxe**

Syntaxe des commandes de gestion des rôles de sécurité :

*Verb* *SecurableObjectType* *SecurableObjectName* *Role* [`(` *ListOfPrincipals* `)` [*Description*]]

* *Verb* indique le genre d’action `.drop`à `.set`effectuer: `.show`, , `.add`, et .

    |*Verbe* |Description                                  |
    |-------|---------------------------------------------|
    |`.show`|Retourne la valeur ou les valeurs actuelles.         |
    |`.add` |Ajoute un ou plusieurs directeurs au rôle.     |
    |`.drop`|Supprime un ou plusieurs directeurs d’école du rôle.|
    |`.set` |Définit le rôle à la liste spécifique des directeurs d’école, en supprimant tous les précédents (le cas échéant).|

* *SecurableObjectType* est le genre d’objet dont le rôle est spécifié.

    |*Objecttype titrable*|Description|
    |---------------------|-----------|
    |`database`|La base de données spécifiée|
    |`table`|Le tableau spécifié|

* *SecurableObjectName* est le nom de l’objet.

* *Le rôle* est le nom du rôle pertinent.

    |*Rôle*      |Description|
    |------------|-----------|
    |`principals`|Peut apparaître seulement comme `.show` faisant partie d’un verbe; retourne la liste des principes qui peuvent affecter l’objet titrable.|
    |`admins`    |Avoir le contrôle sur l’objet titrable, y compris la possibilité de le visualiser, de le modifier et de supprimer l’objet et tous les sous-objets.|
    |`users`     |Peut voir l’objet titrable, et créer de nouveaux objets en dessous.|
    |`viewers`   |Peut voir l’objet titrable.|
    |`unrestrictedviewers`|Au niveau de la base de données seulement, permet de `viewers` `users`visualiser les tables restreintes (qui ne sont pas exposés à «normal» et ).|
    |`ingestors` |Au niveau de la base de données seulement, permettre l’ingestion de données dans toutes les tables.|
    |`monitors`  ||

* *ListOfPrincipals* est une liste facultative, délimitée par virgule des principaux identificateurs de sécurité (valeurs de type `string`).

* *La description* est une `string` valeur facultative de type qui est stockée aux côtés de l’association, à des fins de vérification future.

## <a name="example"></a>Exemple

La commande de contrôle suivante répertorie tous `StormEvents` les principaux de sécurité qui ont un certain accès à la table dans la base de données :

```kusto
.show table StormEvents principals
```

Voici les résultats potentiels de cette commande :

|Role |PrincipalType (principalType) |PrincipalDisplayName (en) |PrincipalObjectId |PrincipalFQN (en) 
|---|---|---|---|---
|Base de données Apsty Admin |Utilisateur AAD |Mark Smith |cd709aed-a26c-e3953dec735e |aadusermsmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>Gestion des rôles de sécurité des bases de données

`.set``database` *DatabaseName* *Role* `none` Role`skip-results`[ ]

`.set``database` *DatabaseName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

`.add``database` *DatabaseName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

`.drop``database` *DatabaseName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

La première commande retire tous les principes du rôle. Le second supprime tous les directeurs d’école du rôle et établit un nouvel ensemble de principes. Le troisième ajoute de nouveaux directeurs d’école au rôle sans supprimer les mandants existants. Le dernier supprime les directeurs indiqués des rôles et garde les autres.

Où :

* *DatabaseName* est le nom de la base de données dont le rôle de sécurité est modifié.

* *Le* rôle `admins` `ingestors`est: `users`, `viewers`, `monitors`, `unrestrictedviewers`, ou .

* *Le principal* est un ou plusieurs directeurs d’école. Consultez [les directeurs d’école et les fournisseurs d’identité](./access-control/principals-and-identity-providers.md) pour savoir comment spécifier ces directeurs d’école.

* `skip-results`, s’il est fourni, demande que la commande ne renvoie pas la liste mise à jour des directeurs de base de données.

* *Description*, si fourni, est le texte qui sera associé `.show` à la modification et récupéré par la commande correspondante.

<!-- TODO: Need more examples for the public syntax. Until then we're keeping this internal -->


## <a name="managing-table-security-roles"></a>Gestion des rôles de sécurité de table

`.set``table` *Rôle de Nom de la* *Role* `none` table [`skip-results`]

`.set``table` *TableauName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

`.add``table` *TableauName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

`.drop``table` *TableauName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

La première commande retire tous les principes du rôle. Le second supprime tous les directeurs d’école du rôle et établit un nouvel ensemble de principes. Le troisième ajoute de nouveaux directeurs d’école au rôle sans supprimer les mandants existants. Le dernier supprime les directeurs indiqués des rôles et garde les autres.

Où :

* *TableName* est le nom de la table dont le rôle de sécurité est modifié.

* *Le* rôle `admins` `ingestors`est : ou .

* *Le principal* est un ou plusieurs directeurs d’école. Consultez [les directeurs d’école et les fournisseurs d’identité](./access-control/principals-and-identity-providers.md) pour savoir comment spécifier ces directeurs d’école.

* `skip-results`, s’il est fourni, demande que la commande ne renvoie pas la liste mise à jour des directeurs de table.

* *Description*, si fourni, est le texte qui sera associé `.show` à la modification et récupéré par la commande correspondante.

**Exemple**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-function-security-roles"></a>Gestion des rôles de sécurité de la fonction

`.set``function` *Rôle FunctionName* *Role* `none` [`skip-results`]

`.set``function` *FunctionName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

`.add``function` *FunctionName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

`.drop``function` *FunctionName* *Role* `(` *Principal* `,` *[Principal*...] `)` [`skip-results`] [*Description*]

La première commande retire tous les principes du rôle. Le second supprime tous les directeurs d’école du rôle et établit un nouvel ensemble de principes. Le troisième ajoute de nouveaux directeurs d’école au rôle sans supprimer les mandants existants. Le dernier supprime les directeurs indiqués des rôles et garde les autres.

Où :

* *FunctionName* est le nom de la fonction dont le rôle de sécurité est modifié.

* *Le* rôle `admin`est toujours .

* *Le principal* est un ou plusieurs directeurs d’école. Consultez [les directeurs d’école et les fournisseurs d’identité](./access-control/principals-and-identity-providers.md) pour savoir comment spécifier ces directeurs d’école.

* `skip-results`, s’il est fourni, demande que la commande ne renvoie pas la liste mise à jour des principes de fonction.

* *Description*, si fourni, est le texte qui sera associé `.show` à la modification et récupéré par la commande correspondante.

**Exemple**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```


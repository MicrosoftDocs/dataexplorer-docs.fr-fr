---
title: Gestion des rôles de sécurité-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la gestion des rôles de sécurité dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4bda122b589e3ba297b3e7c350d15687da6ee123
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057000"
---
# <a name="security-roles-management"></a>Gestion des rôles de sécurité

> [!IMPORTANT]
> Avant de modifier les règles d’autorisation sur vos clusters Kusto, lisez les informations suivantes : [vue d’ensemble du contrôle d’accès Kusto](../management/access-control/index.md)  
>  [autorisation basée sur les rôles](../management/access-control/role-based-authorization.md) 

Cet article décrit les commandes de contrôle utilisées pour gérer les rôles de sécurité.
Les rôles de sécurité définissent les principaux de sécurité (utilisateurs et applications) qui ont des autorisations pour fonctionner sur une ressource sécurisée, telle qu’une base de données ou une table, et quelles opérations sont autorisées. Par exemple, les principaux qui ont le `database viewer` rôle de sécurité pour une base de données spécifique peuvent interroger et afficher toutes les entités de cette base de données (à l’exception des tables restreintes).

Le rôle de sécurité peut être associé à des principaux de sécurité ou à des groupes de sécurité (qui peuvent avoir d’autres entités de sécurité ou d’autres groupes de sécurité). Lorsqu’un principal de sécurité tente d’effectuer une opération sur une ressource sécurisée, le système vérifie que le principal est associé à au moins un rôle de sécurité qui accorde des autorisations pour effectuer cette opération sur la ressource. C’est ce qu’on appelle une **vérification d’autorisation**. L’échec de la vérification d’autorisation interrompt l’opération.

**Syntaxe**

Syntaxe des commandes de gestion des rôles de sécurité :

*Verbe* *SecurableObjectType* *SecurableObjectName* *role* [ `(` *ListOfPrincipals* `)` [*Description*]]

* *Verbe* indique le type d’action à effectuer : `.show` , `.add` , `.drop` et `.set` .

    |*Verbe* |Description                                  |
    |-------|---------------------------------------------|
    |`.show`|Retourne la ou les valeurs actuelles.         |
    |`.add` |Ajoute un ou plusieurs principaux au rôle.     |
    |`.drop`|Supprime un ou plusieurs principaux du rôle.|
    |`.set` |Définit le rôle sur la liste spécifique des principaux, en supprimant tous ceux précédents (le cas échéant).|

* *SecurableObjectType* est le genre d’objet dont le rôle est spécifié.

    |*SecurableObjectType*|Description|
    |---------------------|-----------|
    |`database`|Base de données spécifiée|
    |`table`|Table spécifiée|
    |`materialized-view`| [Vue matérialisée](materialized-views/materialized-view-overview.md) spécifiée| 

* *SecurableObjectName* est le nom de l’objet.

* *Role* est le nom du rôle approprié.

    |*Rôle*      |Description|
    |------------|-----------|
    |`principals`|Peut apparaître uniquement dans le cadre d’un `.show` verbe ; retourne la liste des principaux qui peuvent affecter l’objet sécurisable.|
    |`admins`    |Contrôlez l’objet sécurisable, notamment la possibilité d’afficher, de modifier et de supprimer l’objet et tous les sous-objets.|
    |`users`     |Peut afficher l’objet sécurisable et créer des objets en dessous.|
    |`viewers`   |Peut afficher l’objet sécurisable.|
    |`unrestrictedviewers`|Au niveau de la base de données uniquement, permet d’afficher les tables restreintes (qui ne sont pas exposées à « normal » `viewers` et `users` ).|
    |`ingestors` |Au niveau de la base de données uniquement, autorisez l’ingestion des données dans toutes les tables.|
    |`monitors`  ||

* *ListOfPrincipals* est une liste facultative délimitée par des virgules d’identificateurs de principal de sécurité (valeurs de type `string` ).

* *Description* est une valeur facultative de type `string` qui est stockée en même temps que l’Association, à des fins d’audit futures.

## <a name="example"></a>Exemple

La commande de contrôle suivante répertorie tous les principaux de sécurité qui ont un accès à la table `StormEvents` dans la base de données :

```kusto
.show table StormEvents principals
```

Voici les résultats potentiels de cette commande :

|Role |PrincipalType |PrincipalDisplayName |PrincipalObjectId |PrincipalFQN 
|---|---|---|---|---
|Admin Apsty de la base de données |Utilisateur Azure AD |Marquer Smith |cd709aed-a26c-e3953dec735e |aaduser =msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>Gestion des rôles de sécurité de base de données

`.set``database` *DatabaseName* *Rôle* DatabaseName `none` [ `skip-results` ]

`.set`Le `database` principal du rôle *DatabaseName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add`Le `database` principal du rôle *DatabaseName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop`Le `database` principal du rôle *DatabaseName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

La première commande supprime tous les principaux du rôle. La seconde supprime tous les principaux du rôle et définit un nouvel ensemble de principaux. Le troisième ajoute de nouveaux principaux au rôle sans supprimer les principaux existants. La dernière supprime les principaux indiqués des rôles et conserve les autres.

Où :

* *DatabaseName* est le nom de la base de données dont le rôle de sécurité est en cours de modification.

* Le *rôle* est : `admins` ,,,, `ingestors` `monitors` `unrestrictedviewers` `users` ou `viewers` .

* *Principal* est un ou plusieurs principaux. Consultez [principaux et fournisseurs d’identité](./access-control/principals-and-identity-providers.md) pour savoir comment spécifier ces principaux.

* `skip-results`, s’il est fourni, demande que la commande ne retourne pas la liste mise à jour des principaux de base de données.

* La *Description*, si elle est fournie, est le texte qui sera associé à la modification et récupéré par la `.show` commande correspondante.

<!-- TODO: Need more examples for the public syntax. Until then we're keeping this internal -->


## <a name="managing-table-security-roles"></a>Gestion des rôles de sécurité de table

`.set``table` *TableName* , *rôle* `none` [ `skip-results` ]

`.set``table`Principal du rôle *TableName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add``table`Principal du rôle *TableName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop``table`Principal du rôle *TableName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

La première commande supprime tous les principaux du rôle. La seconde supprime tous les principaux du rôle et définit un nouvel ensemble de principaux. Le troisième ajoute de nouveaux principaux au rôle sans supprimer les principaux existants. La dernière supprime les principaux indiqués des rôles et conserve les autres.

Où :

* *TableName* est le nom de la table dont le rôle de sécurité est en cours de modification.

* Le *rôle* est : `admins` ou `ingestors` .

* *Principal* est un ou plusieurs principaux. Consultez [principaux et fournisseurs d’identité](./access-control/principals-and-identity-providers.md) pour savoir comment spécifier ces principaux.

* `skip-results`, s’il est fourni, demande que la commande ne retourne pas la liste mise à jour des principaux de table.

* La *Description*, si elle est fournie, est le texte qui sera associé à la modification et récupéré par la `.show` commande correspondante.

**Exemple**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-materialized-view-security-roles"></a>Gestion des rôles de sécurité vue matérialisée

`.show``materialized-view` *MaterializedViewName*`principals`

`.set``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(` *Principal* `,[` *Principal...*`])`

`.add``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(` *Principal* `,[` *Principal...*`])`

`.drop``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(` *Principal* `,[` *Principal...*`])`

Où :

* *MaterializedViewName* est le nom de la vue matérialisée dont le rôle de sécurité est en cours de modification
* *Principal* est un ou plusieurs principaux. Voir [principaux et fournisseurs d’identité](./access-control/principals-and-identity-providers.md)

## <a name="managing-function-security-roles"></a>Gestion des rôles de sécurité des fonctions

`.set``function` *FunctionName* *Rôle* nomfonction `none` [ `skip-results` ]

`.set``function`Entité du rôle *nomfonction* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add``function`Entité du rôle *nomfonction* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop``function`Entité du rôle *nomfonction* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

La première commande supprime tous les principaux du rôle. La seconde supprime tous les principaux du rôle et définit un nouvel ensemble de principaux. Le troisième ajoute de nouveaux principaux au rôle sans supprimer les principaux existants. La dernière supprime les principaux indiqués des rôles et conserve les autres.

Où :

* *Nomfonction* est le nom de la fonction dont le rôle de sécurité est en cours de modification.

* Le *rôle* est toujours `admin` .

* *Principal* est un ou plusieurs principaux. Consultez [principaux et fournisseurs d’identité](./access-control/principals-and-identity-providers.md) pour savoir comment spécifier ces principaux.

* `skip-results`, s’il est fourni, demande que la commande ne retourne pas la liste mise à jour des principaux de fonction.

* La *Description*, si elle est fournie, est le texte qui sera associé à la modification et récupéré par la `.show` commande correspondante.

**Exemple**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```


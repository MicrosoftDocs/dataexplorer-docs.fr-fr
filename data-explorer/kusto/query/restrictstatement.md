---
title: Restriction de la déclaration - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la déclaration de Restriction dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: cbd21c01956f817c5db19a93104028dba2b2b2b4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765990"
---
# <a name="restrict-statement"></a>Restrict, instruction

::: zone pivot="azuredataexplorer"

L’énoncé de restriction limite l’ensemble des entités de table/vue qui sont visibles pour les énoncés de requête qui le suivent. Par exemple, dans une base`A`de `B`données qui comprend deux tableaux (, `B` ), l’application peut empêcher `A` le reste de la requête d’accéder et seulement "voir" une forme limitée de table en utilisant une vue.

Le scénario principal de l’énoncé de restriction est pour les applications de niveau intermédiaire qui acceptent les requêtes des utilisateurs et qui veulent appliquer un mécanisme de sécurité au niveau des lignes sur ces requêtes. L’application de niveau intermédiaire peut préfixer la requête de l’utilisateur avec un **modèle logique,** un ensemble `T | where UserId == "..."`de déclarations de laisser définir les vues qui limitent l’accès de l’utilisateur aux données (par exemple, ). Au fur et à mesure que la dernière déclaration est ajoutée, elle restreint seulement l’accès de l’utilisateur au modèle logique.

**Syntaxe**

`restrict``access` `,` [EntitySpecifier [...]]*EntitySpecifier* `to` `(``)`

Où *EntitySpecifier* est l’un des:
* Un identifiant défini par une déclaration de laisser comme une vue tabulaire.
* Une référence de tableau (semblable à celle utilisée par un communiqué syndical).
* Un modèle défini par une déclaration de modèle.

Toutes les tables, vues tabulaires ou motifs qui ne sont pas spécifiés par l’énoncé de restriction deviennent « invisibles » pour le reste de la requête. 

**Remarques**

L’énoncé de restriction peut être utilisé pour restreindre l’accès aux entités d’une autre base de données ou d’un autre cluster (les cartes sauvages ne sont pas prises en charge dans les noms de cluster).

**Arguments**

L’énoncé de restriction peut obtenir un ou plusieurs paramètres qui définissent la restriction permissive lors de la résolution du nom de l’entité. L’entité peut être :
- [laisser](./letstatement.md) la `restrict` déclaration apparaître avant la déclaration. 

```kusto
// Limit access to 'Test' let statement only
let Test = () { print x=1 };
restrict access to (Test);
```

- [Tables](../management/tables.md) ou [fonctions définies](../management/functions.md) dans les métadonnées de base de données.

```kusto
// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
// and other database 'DB2' has Table2 defined in the metadata
 
restrict access to (database().Table1, database().Func1, database('DB2').Table2);
```

- Modèles Wildcard qui peuvent correspondre à des multiples de [déclarations de laisser](./letstatement.md) ou des tables / fonctions  

```kusto
let Test1 = () { print x=1 };
let Test2 = () { print y=1 };
restrict access to (*);
// Now access is restricted to Test1, Test2 and no tables/functions are accessible.

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database().*);
// Now access is restricted to all tables/functions of the current database ('DB2' is not accessible).

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database('DB2').*);
// Now access is restricted to all tables/functions of the database 'DB2'
```


**Exemples**

L’exemple suivant montre comment une application de niveau intermédiaire peut prépendrer la requête d’un utilisateur avec un modèle logique qui empêche l’utilisateur d’interroger les données d’un autre utilisateur.

```kusto
// Assume the database has a single table, UserData,
// with a column called UserID and other columns that hold
// per-user private information.
//
// The middle-tier application generates the following statements.
// Note that "username@domain.com" is something the middle-tier application
// derives per-user as it authenticates the user.
let RestrictedData = view () { Data | where UserID == "username@domain.com" };
restrict access to (RestrictedData);
// The rest of the query is something that the user types.
// This part can only reference RestrictedData; attempting to reference Data
// will fail.
RestrictedData | summarize IrsLovesMe=sum(Salary) by Year, Month
```

```kusto
// Restricting access to Table1 in the current database (database() called without parameters)
restrict access to (database().Table1);
Table1 | count

// Restricting acess to Table1 in the current database and Table2 in database 'DB2'
restrict access to (database().Table1, database('DB2').Table2);
union 
    (Table1),
    (database('DB2').Table2))
| count

// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end

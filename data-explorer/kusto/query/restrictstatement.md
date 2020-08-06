---
title: Instruction Restrict-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’instruction Restrict dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 52cec808795024bd58b6a4ef6cf08e5b700c0e33
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803622"
---
# <a name="restrict-statement"></a>Restrict, instruction

::: zone pivot="azuredataexplorer"

L’instruction Restrict limite l’ensemble des entités de table ou de vue qui sont visibles pour les instructions de requête qui la suivent. Par exemple, dans une base de données qui comprend deux tables ( `A` , `B` ), l’application peut empêcher le reste de la requête d’accéder à `B` et de « voir » une forme limitée de table à `A` l’aide d’une vue.

Le scénario principal de l’instruction Restrict est destiné aux applications de couche intermédiaire qui acceptent les requêtes des utilisateurs et souhaitent appliquer un mécanisme de sécurité au niveau des lignes à ces requêtes. L’application de niveau intermédiaire peut préfixer la requête de l’utilisateur avec un **modèle logique**, un ensemble d’instructions Let définissant des vues qui limitent l’accès de l’utilisateur aux données (par exemple, `T | where UserId == "..."` ). Lorsque la dernière instruction est ajoutée, elle limite l’accès de l’utilisateur au modèle logique uniquement.

> [!NOTE]
> L’instruction Restrict peut être utilisée pour restreindre l’accès aux entités d’une autre base de données ou d’un autre cluster (les caractères génériques ne sont pas pris en charge dans les noms de cluster).

## <a name="syntax"></a>Syntaxe

`restrict``access` `to` `(` [*EntitySpecifier* [ `,` ...]]`)`

Où *EntitySpecifier* est l’un des éléments suivants :
* Identificateur défini par une instruction Let comme vue tabulaire.
* Référence de table (semblable à celle utilisée par une instruction Union).
* Modèle défini par une déclaration de modèle.

Toutes les tables, vues tabulaires ou modèles qui ne sont pas spécifiés par l’instruction Restrict deviennent « invisibles » pour le reste de la requête. 

## <a name="arguments"></a>Arguments

L’instruction Restrict peut obtenir un ou plusieurs paramètres qui définissent la restriction permissive lors de la résolution de noms de l’entité. L’entité peut être :
* [instruction Let](./letstatement.md) qui apparaît avant l' `restrict` instruction. 

  ```kusto
  // Limit access to 'Test' let statement only
  let Test = () { print x=1 };
  restrict access to (Test);
  ```

* [Tables](../management/tables.md) ou [fonctions](../management/functions.md) définies dans les métadonnées de la base de données.

    ```kusto
    // Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
    // and other database 'DB2' has Table2 defined in the metadata
    
    restrict access to (database().Table1, database().Func1, database('DB2').Table2);
    ```

* Modèles de caractères génériques pouvant correspondre à des multiples d' [instructions Let](./letstatement.md) ou de tables/fonctions  

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

## <a name="examples"></a>Exemples

L’exemple suivant montre comment une application de couche intermédiaire peut ajouter une requête d’utilisateur à un modèle logique qui empêche l’utilisateur d’interroger les données d’un autre utilisateur.

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

// Restricting access to Table1 in the current database and Table2 in database 'DB2'
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

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end

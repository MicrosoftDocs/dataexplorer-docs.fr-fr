---
title: Curseurs de base de données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les curseurs de base de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90ec677a7eaf1f326509828b5415b022742fd9ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521316"
---
# <a name="database-cursors"></a>Curseurs de base de données

Un **curseur de base de données** est un objet au niveau de la base de données qui permet d’interroger une base de données plusieurs fois et d’obtenir des résultats cohérents même s’il y a des opérations continues d’appendice/de conservation des données qui se déroulent en parallèle avec les requêtes.

Les curseurs de base de données sont conçus pour aborder deux scénarios importants :

* La possibilité de répéter la même requête plusieurs fois et d’obtenir les mêmes résultats, tant que la requête indique le « même ensemble de données ».

* La possibilité d’effectuer une requête « exactement une fois » (une requête qui ne fait que « voir » les données qu’une requête précédente n’a pas vu parce que les données n’étaient pas disponibles à l’époque).
   Cela permet, par exemple, d’itérer à travers toutes les données nouvellement arrivées dans un tableau sans crainte de traiter le même enregistrement deux fois ou de sauter des enregistrements par erreur.

Le curseur de base de données est représenté dans la `string`langue de requête comme une valeur scalaire de type . La valeur réelle doit être considérée comme opaque et il n’y a aucun support pour toute opération autre que l’économie de sa valeur et / ou en utilisant les fonctions de curseur noté ci-dessous.

## <a name="cursor-functions"></a>Fonctions curseur

Kusto fournit trois fonctions pour aider à mettre en œuvre les deux scénarios ci-dessus :

* [cursor_current):](../query/cursorcurrent.md)Utilisez cette fonction pour récupérer la valeur actuelle du curseur de base de données.
   Vous pouvez utiliser cette valeur comme argument aux deux autres fonctions.
   Cette fonction a également `current_cursor()`un synonyme, .

* [cursor_after (rhs:string)](../query/cursorafterfunction.md): Cette fonction spéciale peut être utilisée sur les enregistrements de table qui ont activé la [politique IngestionTime.](ingestiontime-policy.md) Il renvoie une valeur `bool` scalaire de `ingestion_time()` type indiquant si la `rhs` valeur du curseur de base de données de l’enregistrement vient après la valeur du curseur de base de données.

* [cursor_before_or_at (rhs:string)](../query/cursorbeforeoratfunction.md): Cette fonction spéciale peut être utilisée sur les enregistrements de table qui ont activé la [politique IngestionTime.](ingestiontime-policy.md) Il renvoie une valeur `bool` scalaire de `ingestion_time()` type indiquant si la `rhs` valeur du curseur de base de données de l’enregistrement vient après la valeur du curseur de base de données.

Les deux fonctions`cursor_after` `cursor_before_or_at`spéciales ( et ) ont également un effet secondaire: Quand ils sont utilisés, Kusto émettra la **valeur actuelle du curseur de base de données** à l’ensemble `@ExtendedProperties` de résultat de la requête. Le nom de propriété pour `Cursor`le curseur est, et sa valeur est un seul `string`. Par exemple :

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>Restrictions

Les curseurs de base de données ne peuvent être utilisés qu’avec des tableaux pour lesquels la [stratégie IngestionTime](ingestiontime-policy.md) a été activée. Chaque enregistrement dans un tel tableau est associé à la valeur du curseur de base de données qui était en vigueur lorsque l’enregistrement a été ingéré, et donc la fonction [ingestion_time))](../query/ingestiontimefunction.md) peut être utilisée.

L’objet curseur de base de données ne contient aucune valeur significative à moins que la base de données ait au moins une table qui a une [stratégie IngestionTime](ingestiontime-policy.md) définie.
En outre, il est seulement garanti que cette valeur est mise à jour comme nécessaire par l’historique de l’ingestion dans de telles tables et les requêtes exécutent que la référence de ces tables. Il pourrait, ou non, être mis à jour dans d’autres cas.

Le processus d’ingestion engage d’abord les données (de sorte qu’elles sont disponibles pour la requête), et seulement alors attribue une valeur de curseur réelle à chaque enregistrement. Cela signifie que si l’on tente de demander des données immédiatement après l’achèvement de l’ingestion à l’aide d’un curseur de base de données, les résultats pourraient ne pas encore incorporer les derniers enregistrements ajoutés, parce qu’ils n’ont pas encore été attribués la valeur du curseur. De même, la récupération de la valeur actuelle du curseur de base de données à plusieurs reprises pourrait retourner la même valeur (même si l’ingestion a été faite entre les deux) parce que seule l’engagement curseur mettra à jour sa valeur.

## <a name="example-processing-of-records-exactly-once"></a>Exemple : Traitement des dossiers exactement une fois

Supposons `Employees` la `[Name, Salary]`table avec schéma .
Pour traiter continuellement de nouveaux enregistrements au fur et à mesure qu’ils sont ingérés dans la table, utilisez la procédure suivante :

```kusto
// [Once] Enable the IngestionTime policy on table Employees
.set table Employees policy ingestiontime true

// [Once] Get all the data that the Employees table currently holds 
Employees | where cursor_after('')

// The query above will return the database cursor value in
// the @ExtendedProperties result set. Lets assume that it returns
// the value '636040929866477946'

// [Many] Get all the data that was added to the Employees table
// since the previous query was run using the previously-returned
// database cursor 
Employees | where cursor_after('636040929866477946') // -> 636040929866477950

Employees | where cursor_after('636040929866477950') // -> 636040929866479999

Employees | where cursor_after('636040929866479999') // -> 636040939866479000
```
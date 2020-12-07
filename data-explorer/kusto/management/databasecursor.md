---
title: Curseurs de base de données-Azure Explorateur de données
description: Cet article décrit les curseurs de base de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3a3deb388c5a57f3400eb5fbe24f77a31e48b69c
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303309"
---
# <a name="database-cursors"></a>Curseurs de base de données

Un **curseur de base de données** est un objet de niveau base de données qui vous permet d’interroger plusieurs fois une base de données. Vous obtiendrez des résultats cohérents même si `data-append` des `data-retention` opérations ou se produisent en parallèle avec les requêtes.

Les curseurs de base de données sont conçus pour répondre à deux scénarios importants :

* La possibilité de répéter plusieurs fois la même requête et d’obtenir les mêmes résultats, tant que la requête indique « même jeu de données ».

* La possibilité de créer une requête « exactement une fois ». Cette requête ne voit que les données qu’une requête précédente ne s’est pas affichée, car les données n’étaient pas disponibles.
   La requête vous permet d’effectuer des itérations, par exemple, à travers toutes les données nouvellement arrivées dans une table sans crainte de traiter le même enregistrement deux fois ou en ignorant les enregistrements par erreur.

Le curseur de base de données est représenté dans le langage de requête sous la forme d’une valeur scalaire de type `string` . La valeur réelle doit être considérée comme opaque et il n’y a aucune prise en charge pour toute opération autre que d’enregistrer sa valeur ou d’utiliser les fonctions de curseur indiquées ci-dessous.

## <a name="cursor-functions"></a>Fonctions curseur

Kusto fournit trois fonctions pour vous aider à implémenter les deux scénarios ci-dessus :

* [cursor_current ()](../query/cursorcurrent.md): utilisez cette fonction pour récupérer la valeur actuelle du curseur de la base de données.
   Vous pouvez utiliser cette valeur comme argument pour les deux autres fonctions.
   Cette fonction a également un synonyme, `current_cursor()` .

* [cursor_after (RHS : String)](../query/cursorafterfunction.md): cette fonction spéciale peut être utilisée sur les enregistrements de table pour lesquels la [stratégie IngestionTime](ingestiontime-policy.md) est activée. Elle retourne une valeur scalaire de type `bool` indiquant si la valeur du `ingestion_time()` curseur de la base de données de l’enregistrement vient après la `rhs` valeur du curseur de la base de données.

* [cursor_before_or_at (RHS : String)](../query/cursorbeforeoratfunction.md): cette fonction spéciale peut être utilisée sur les enregistrements de table pour lesquels la [stratégie IngestionTime](ingestiontime-policy.md) est activée. Elle retourne une valeur scalaire de type `bool` indiquant si la valeur du `ingestion_time()` curseur de la base de données de l’enregistrement est antérieure ou au niveau `rhs` de la valeur du curseur de la base de données.

Les deux fonctions spéciales ( `cursor_after` et `cursor_before_or_at` ) ont également un effet secondaire : quand elles sont utilisées, Kusto émet la **valeur actuelle du curseur de base de données** dans le `@ExtendedProperties` jeu de résultats de la requête. Le nom de la propriété pour le curseur est `Cursor` , et sa valeur est un unique `string` . 

Par exemple :

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>Restrictions

Les curseurs de base de données ne peuvent être utilisés qu’avec les tables pour lesquelles la [stratégie IngestionTime](ingestiontime-policy.md) a été activée. Chaque enregistrement dans une telle table est associé à la valeur du curseur de base de données qui était en vigueur lors de l’ingestion de l’enregistrement.
Par conséquent, la fonction [ingestion_time ()](../query/ingestiontimefunction.md) peut être utilisée.

L’objet curseur de base de données ne contient aucune valeur significative, sauf si la base de données a au moins une table qui a une [stratégie IngestionTime](ingestiontime-policy.md) définie.
Cette valeur est garantie pour la mise à jour, en fonction des besoins de l’historique d’ingestion, dans ces tables et les requêtes exécutées, qui font référence à ces tables. Elle peut, ou non, être mise à jour dans d’autres cas.

Le processus d’ingestion valide d’abord les données, afin qu’elles soient disponibles pour l’interrogation, et attribue uniquement une valeur de curseur réelle à chaque enregistrement. Si vous tentez de rechercher des données immédiatement après la fin de l’ingestion à l’aide d’un curseur de base de données, il se peut que les résultats n’intègrent pas encore les derniers enregistrements ajoutés, car la valeur de curseur n’a pas encore été affectée. En outre, la récupération de la valeur du curseur de base de données en cours peut retourner plusieurs fois la même valeur, même si l’ingestion a été effectuée dans between, car seule une validation de curseur peut mettre à jour sa valeur.

## <a name="example-processing-records-exactly-once"></a>Exemple : traitement d’enregistrements exactement une fois

Pour une table `Employees` avec schéma `[Name, Salary]` , pour traiter continuellement de nouveaux enregistrements à mesure qu’ils sont ingérés dans la table, utilisez le processus suivant :

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

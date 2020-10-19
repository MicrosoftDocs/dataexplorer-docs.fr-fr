---
title: array_sort_desc ()-Azure Explorateur de données
description: Cet article décrit array_sort_desc () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 09/22/2020
ms.openlocfilehash: 83be80a7eb440e73fd19f213839cef7d58ec1512
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169633"
---
# <a name="array_sort_desc"></a>array_sort_desc ()

Reçoit un ou plusieurs tableaux. Trie le premier tableau dans l’ordre décroissant. Classe les tableaux restants pour qu’ils correspondent au premier tableau réorganisé.

## <a name="syntax"></a>Syntaxe

`array_sort_desc(`*matrice1*[,..., *argumentN*]`)`

`array_sort_desc(`*matrice1*[,..., *argumentN*] `,` *nulls_last*`)`

Si *nulls_last* n’est pas fourni, la valeur par défaut `true` est utilisée.

## <a name="arguments"></a>Arguments

* *matrice1... Tableaun*: tableaux d’entrée.
* *nulls_last*: valeur booléenne indiquant si `null` s doit être le dernier

## <a name="returns"></a>Retours

Retourne le même nombre de tableaux que dans l’entrée, avec le premier tableau trié dans l’ordre croissant, et les tableaux restants pour qu’ils correspondent au premier tableau réorganisé.

`null` est retourné pour chaque tableau dont la longueur est différente de la première.

Si un tableau contient des éléments de types différents, il est trié dans l’ordre suivant :

* Éléments numériques, `datetime` et `timespan`
* Éléments de chaîne
* Éléments GUID
* Tous les autres éléments

## <a name="example-1---sorting-two-arrays"></a>Exemple 1 : tri de deux tableaux

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let array1 = dynamic([1,3,4,5,2]);
let array2 = dynamic(["a","b","c","d","e"]);
print array_sort_desc(array1,array2)
```

|`array1_sorted`|`array2_sorted`|
|---|---|
|[5, 4, 3, 2, 1]|["d", "c", "b", "e", "a"]|

> [!Note]
> Les noms des colonnes de sortie sont générés automatiquement, en fonction des arguments de la fonction. Pour assigner des noms différents aux colonnes de sortie, utilisez la syntaxe suivante : `... | extend (out1, out2) = array_sort_desc(array1,array2)`

## <a name="example-2---sorting-substrings"></a>Exemple 2 : tri des sous-chaînes

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Names = "John,Paul,George,Ringo";
let SortedNames = strcat_array(array_sort_desc(split(Names, ",")), ",");
print result = SortedNames
```

|`result`|
|---|
|Ringo, Paul, John, George|

## <a name="example-3---combining-summarize-and-array_sort_desc"></a>Exemple 3 : combinaison d’un résumé et d’un array_sort_desc

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',   datetime(2019-07-15),   "user1",
    'ls',      datetime(2019-07-02),   "user1",
    'dir',     datetime(2019-07-22),   "user1",
    'mkdir',   datetime(2019-07-14),   "user1",
    'rm',      datetime(2019-07-27),   "user1",
    'pwd',     datetime(2019-07-25),   "user1",
    'rm',      datetime(2019-07-23),   "user2",
    'pwd',     datetime(2019-07-25),   "user2",
]
| summarize timestamps = make_list(command_time), commands = make_list(command) by user_id
| project user_id, commands_in_chronological_order = array_sort_desc(timestamps, commands)[1]
```

|`user_id`|`commands_in_chronological_order`|
|---|---|
|user1|[<br>  « RM »,<br>  « pwd »,<br>  « dir »,<br>  « chmod »,<br>  « mkdir »,<br>  LS<br>]|
|user2|[<br>  « pwd »,<br>  RM<br>]|

> [!Note]
> Si vos données peuvent contenir des `null` valeurs, utilisez [make_list_with_nulls](make-list-with-nulls-aggfunction.md) au lieu de [make_list](makelist-aggfunction.md).

## <a name="example-4---controlling-location-of-null-values"></a>Exemple 4 : contrôle de l’emplacement des `null` valeurs

Par défaut, `null` les valeurs sont placées en dernier dans le tableau trié. Toutefois, vous pouvez le contrôler explicitement en ajoutant une `bool` valeur comme dernier argument à `array_sort_desc()` .

Exemple avec comportement par défaut :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]))
```

|`print_0`|
|---|
|[« Yellow », « Green », « Blue », NULL, NULL]|

Exemple avec comportement non défini par défaut :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]), false)
```

|`print_0`|
|---|
|[null, NULL, « Yellow », « Green », « Blue »]|

## <a name="see-also"></a>Voir aussi

Pour trier le premier tableau dans l’ordre croissant, utilisez [array_sort_asc ()](arraysortascfunction.md).

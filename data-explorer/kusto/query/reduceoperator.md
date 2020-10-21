---
title: opérateur de réduction-Azure Explorateur de données
description: Cet article décrit l’opérateur de réduction dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ef5e42dc9c41426cd66dbf4d857ec0d2c32e2ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252121"
---
# <a name="reduce-operator"></a>opérateur reduce

Regroupe un ensemble de chaînes en fonction de la similarité des valeurs.

```kusto
T | reduce by LogMessage with threshold=0.1
```

Pour chaque groupe de ce type, il génère un **modèle** qui décrit le mieux le groupe (éventuellement en utilisant le caractère astérisque ( `*` ) pour représenter des **count** caractères génériques), le nombre de valeurs dans le groupe et un **représentant** du groupe (l’une des valeurs d’origine dans le groupe).

## <a name="syntax"></a>Syntaxe

*T* `|` `reduce` [ `kind` `=` *ReduceKind*] `by` *expr* [ `with` [ `threshold` `=` *seuil*] [ `,` `characters` `=` *caractères*]]

## <a name="arguments"></a>Arguments

* *Expr*: expression qui prend la valeur d’une `string` valeur.
* *Seuil*: `real` littéral de la plage (0.. 1). La valeur par défaut est 0,1. Pour les entrées volumineuses, le seuil doit être bas. 
* *Caractères*: `string` littéral contenant une liste de caractères à ajouter à la liste des caractères qui ne comcoupent pas un terme. (Par exemple, si vous souhaitez `aaa=bbbb` et `aaa:bbb` pour chaque terme entier, plutôt que d’arrêter sur `=` et `:` , utilisez `":="` comme littéral de chaîne.)
* *ReduceKind*: spécifie la version de réduction. La seule valeur valide pour l’heure est `source` .

## <a name="returns"></a>Retours

Cet opérateur retourne une table avec trois colonnes ( `Pattern` , `Count` et `Representative` ), et autant de lignes qu’il y a de groupes. `Pattern` est la valeur de modèle pour le groupe, avec l' `*` utilisation d’un caractère générique (représentant des chaînes d’insertion arbitraires), `Count` compte le nombre de lignes dans l’entrée de l’opérateur qui sont représentées par ce modèle, et `Representative` représente une valeur de l’entrée qui se trouve dans ce groupe.

Si `[kind=source]` est spécifié, l’opérateur ajoute la `Pattern` colonne à la structure de table existante.
Notez que la syntaxe d’un schéma de cette version peut être soumise à des modifications ultérieures.

Par exemple, le résultat de `reduce by city` peut inclure : 

|Modèle     |Nombre |Representative|
|------------|------|--------------|
| San *      | 5182 |Bernard San   |
| Saint *    | 2846 |Saint Lucy    |
| Moscow     | 3726 |Moscow        |
| \* -on- \* | 2730 |Un-à-un  |
| Paris      | 2716 |Paris         |

Autre exemple avec une création de jetons personnalisée :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|Modèle         |Nombre|Representative   |
|----------------|-----|-----------------|
|MachineLearning|1 000 |MachineLearningX4|

## <a name="examples"></a>Exemples

L’exemple suivant montre comment il est possible d’appliquer l' `reduce` opérateur à une entrée « expurgée », dans laquelle les GUID de la colonne réduite sont remplacés avant la réduction

```kusto
// Start with a few records from the Trace table.
Trace | take 10000
// We will reduce the Text column which includes random GUIDs.
// As random GUIDs interfere with the reduce operation, replace them all
// by the string "GUID".
| extend Text=replace(@"[[:xdigit:]]{8}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{12}", @"GUID", Text)
// Now perform the reduce. In case there are other "quasi-random" identifiers with embedded '-'
// or '_' characters in them, treat these as non-term-breakers.
| reduce by Text with characters="-_"
```

## <a name="see-also"></a>Voir aussi

[autocluster](./autoclusterplugin.md)

**Notes**

L’implémentation de l' `reduce` opérateur est en grande partie basée sur la documentation d' [un algorithme de clustering de données pour les modèles d’exploration de données des journaux des événements](https://ristov.github.io/publications/slct-ipom03-web.pdf), par Risto Vaarandi.

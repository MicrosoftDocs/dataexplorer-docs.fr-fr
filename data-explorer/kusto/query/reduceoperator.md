---
title: réduire l’opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit réduire l’opérateur dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33d4ac202b61fdaa1b92291407cdd2783d947c6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510504"
---
# <a name="reduce-operator"></a>opérateur reduce

Regroupe un ensemble de chaînes en fonction de la similitude des valeurs.

```kusto
T | reduce by LogMessage with threshold=0.1
```

Pour chaque groupe, il produit un **modèle** qui décrit le mieux le`*`groupe (éventuellement en utilisant le caractère astérix ( ) pour représenter les wildcards), un **compte** du nombre de valeurs dans le groupe, et un **représentant** du groupe (l’une des valeurs originales du groupe).

**Syntaxe**

*T* `|` `kind` `characters` `=` `threshold` `=` *Threshold*`,` `with` *ReduceKind* `by` *Expr* *Characters*[ `=` ReduceKind ] Expr [ [ Seuil ] [ Personnages ] ] `reduce`

**Arguments**

* *Expr*: Une expression qui `string` évalue à une valeur.
* *Seuil*: `real` Un littéral dans la gamme (0..1). Le défaut est de 0,1. Pour les entrées volumineuses, le seuil doit être bas. 
* *Personnages*: `string` Un littéral contenant une liste de caractères à ajouter à la liste des personnages qui ne cassent pas un terme. (Par exemple, si `aaa=bbbb` `aaa:bbb` vous voulez et à chacun être `=` `:`un `":="` terme entier, plutôt que de rompre et , utiliser comme la chaîne littérale.)
* *ReduceKind*: Specifie la saveur de réduction. La seule valeur valable pour `source`le moment est .

**Retourne**

Cet opérateur retourne une table`Pattern` `Count`avec `Representative`trois colonnes ( , , et ), et autant de lignes qu’il ya des groupes. `Pattern`est la valeur de modèle `*` pour le groupe, avec être utilisé `Count` comme une wildcard (représentant les chaînes d’insertion arbitraires), compte combien de lignes dans l’entrée à l’opérateur sont représentés par ce modèle, et `Representative` est une valeur de l’entrée qui tombe dans ce groupe.

Si `[kind=source]` elle est spécifiée, `Pattern` l’opérateur ajoutera la colonne à la structure de table existante.
Notez que la syntaxe d’un schéma de cette saveur pourrait être soumis à des changements futurs.

Par exemple, le résultat de `reduce by city` peut inclure : 

|Modèle     |Count |Representative|
|------------|------|--------------|
| San *      | 5182 |Saint-Bernard   |
| Saint *    | 2846 |Sainte Lucie    |
| Moscow     | 3726 |Moscow        |
| \* -on- \* | 2730 |Un -sur- Un  |
| Paris      | 2716 |Paris         |

Autre exemple avec la jetonisation personnalisée :

```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|Modèle         |Count|Representative   |
|----------------|-----|-----------------|
|MachineLearning|1 000 |MachineLearningX4|

**Exemples**

L’exemple suivant montre comment `reduce` l’on peut appliquer l’opérateur à une entrée « aseptisée », dans laquelle les GUIDs dans la colonne réduite sont remplacés avant de réduire

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

**Voir aussi**

[autocluster](./autoclusterplugin.md)

**Remarques**

La mise `reduce` en œuvre de l’opérateur est en grande partie basée sur le papier [A Data Clustering Algorithm for Mining Patterns From Event Logs](https://ristov.github.io/publications/slct-ipom03-web.pdf), par Risto Vaarandi.
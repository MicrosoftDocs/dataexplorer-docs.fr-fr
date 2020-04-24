---
title: Déclarations d’expression tabulaires - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les déclarations d’expression Tabulaires dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1209f96ed99de79d3fa6ac00e3a115a00a6248e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506611"
---
# <a name="tabular-expression-statements"></a>Instructions d’expression tabulaire

L’énoncé d’expression tabulaire est ce que les gens ont habituellement à l’esprit quand ils parlent de requêtes. Cette déclaration apparaît généralement en dernier dans la liste des relevés, et son entrée et sa sortie se composent de tableaux ou d’ensembles de données tabulaires.

Kusto utilise un modèle de flux de données pour l’énoncé d’expression tabulaire. La structure typique d’une déclaration d’expression tabulaire est une composition de sources de *données tabulaires* (comme les tableaux Kusto), *des opérateurs de données tabulaires* (tels que des filtres et des projections) et des opérateurs potentiellement de *rendu.* La composition est représentée par`|`le caractère du tuyau (), donnant à la déclaration une forme très régulière qui représente visuellement le flux de données tabulaires de gauche à droite.
Chaque opérateur accepte un ensemble de données tabulaires " à partir du tuyau », et des entrées supplémentaires (y compris d’autres ensembles de données tabulaires) du corps de l’opérateur, puis émet un ensemble de données tabulaires à l’opérateur suivant qui suit :   

*source1* `|` *operator1* `|` *operator2* `|` *renderInstruction*

Dans l’exemple suivant, `Logs` la source est (une référence à un `where` tableau dans la base de données actuelle), le premier opérateur est `count` (qui filtrent les enregistrements de son entrée en fonction de certains prédicat par enregistrement), et le deuxième opérateur est (qui compte le nombre d’enregistrements dans son ensemble de données d’entrée):

```kusto
Logs | where Timestamp > ago(1d) | count
```

Dans l’exemple plus `join` complexe suivant, l’opérateur est utilisé pour combiner les `Logs` enregistrements de deux ensembles de `Events` données d’entrée : l’un qui est un filtre sur la table, et l’autre qui est un filtre sur la table.

```kusto
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
```

## <a name="tabular-data-sources"></a>Sources de données tabulaires

Une **source de données tabulaire** produit des ensembles d’enregistrements, à traiter par les opérateurs de données **tabulaires**. Kusto soutient un certain nombre de ces sources :

* Références de tableau (qui se réfèrent à un tableau Kusto, dans la base de données contextuelle ou dans une autre base de données/cluster.)
* L’opérateur de [la gamme](rangeoperator.md)tabulaire .
* [L’opérateur d’impression](printoperator.md).
* Une invocation d’une fonction qui renvoie une table.
* Une [table littérale](datatableoperator.md) ("datatable").
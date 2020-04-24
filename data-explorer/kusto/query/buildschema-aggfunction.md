---
title: buildschema() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit buildschema() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d0faceae970034ae96ced2b8958af4f16cb5dff4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517287"
---
# <a name="buildschema-aggregation-function"></a>buildschema() (fonction d’agrégation)

Retourne le schéma minimal qui admet toutes les valeurs de *DynamicExpr*.

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `buildschema(` *DynamicExpr*`)`

**Arguments**

* *DynamicExpr*: Expression qui sera utilisée pour le calcul de l’agrégation. Le type de `dynamic`colonne de paramètre doit être . 

**Retourne**

La valeur maximale *d’Expr* dans l’ensemble du groupe.

> [!TIP] 
> Si `buildschema(json_column)` vous offrez une erreur de syntaxe : *est-ce `json_column` que votre chaîne plutôt qu’un objet dynamique ?* Si oui, vous `buildschema(parsejson(json_column))`devez utiliser .

**Exemple**

Supposons que la colonne d’entrée a trois valeurs dynamiques :

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

Le schéma résultant serait :

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Le schéma fournit les informations suivantes :

* L’objet racine est un conteneur avec quatre propriétés nommées x, y, z et t.
* La propriété appelée « x » peut être de type « int » ou « string ».
* La propriété appelée « y » peut être de type « double », ou un autre conteneur avec une propriété appelée « w » de type « string ».
* Le mot-clé ``indexer`` indique que « z » et « t » sont des tableaux.
* Chaque élément du tableau « z » est un entier ou une chaîne.
* « t » est un tableau de chaînes.
* Chaque propriété est implicitement facultative et tout tableau peut être vide.

### <a name="schema-model"></a>Modèle de schéma

La syntaxe du schéma retourné est la suivante :

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Ils sont équivalents à un sous-ensemble des annotations de type TypeScript, codées comme une valeur dynamique Kusto. En Typescript, l’exemple de schéma serait le suivant :

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }
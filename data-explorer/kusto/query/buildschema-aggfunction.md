---
title: buildschema () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit buildschema () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2520849508c9cef829d7c8c07f22d3f8c64cfcea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348936"
---
# <a name="buildschema-aggregation-function"></a>buildschema () (fonction d’agrégation)

Retourne le schéma minimal qui admet toutes les valeurs de *DynamicExpr*.

* Peut être utilisé uniquement dans le contexte de l’agrégation, à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `buildschema(` *DynamicExpr*`)`

## <a name="arguments"></a>Arguments

* *DynamicExpr*: expression utilisée pour le calcul de l’agrégation. Le type de colonne de paramètre doit être `dynamic` . 

## <a name="returns"></a>Retourne

Valeur maximale de *`Expr`* dans le groupe.

> [!TIP] 
> Si `buildschema(json_column)` génère une erreur de syntaxe : *votre chaîne est-elle `json_column` une chaîne plutôt qu’un objet dynamique ?* Utilisez ensuite `buildschema(parsejson(json_column))` .

## <a name="example"></a>Exemple

Supposons que la colonne d’entrée comporte trois valeurs dynamiques.

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
* Propriété appelée « x » qui peut être de type « int » ou de type « String ».
* Propriété appelée « y » qui peut être de type « double », ou un autre conteneur avec une propriété appelée « w » de type « String ».
* Le mot-clé ``indexer`` indique que « z » et « t » sont des tableaux.
* Chaque élément du tableau « z » est de type « int » ou de type « String ».
* « t » est un tableau de chaînes.
* Chaque propriété est implicitement facultative et tout tableau peut être vide.

### <a name="schema-model"></a>Modèle de schéma

La syntaxe du schéma retourné est la suivante :

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Les valeurs sont équivalentes à un sous-ensemble des annotations de type dactylographié, encodées sous la forme d’une valeur dynamique Kusto. En Typescript, l’exemple de schéma serait le suivant :

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }
    

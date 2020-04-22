---
title: Questions sur les meilleures pratiques - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les meilleures pratiques de Requête dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 3458c2fcd7fab3345f65393d7e9a13e844088a9f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663855"
---
# <a name="query-best-practices"></a>Meilleures pratiques relatives aux requêtes 

## <a name="general"></a>Général

Il ya plusieurs "Dos and Don’ts" que vous pouvez suivre pour vous faire courir requête plus vite.

### <a name="do"></a>À faire

*   Utiliser les filtres de temps en premier. Kusto est très optimisé pour utiliser des filtres à retards.
*   Lors de l’utilisation d’opérateurs de chaînes :
    *   Préférez `has` l’opérateur plus `contains` lors de la recherche de jetons complets. `has`est plus performant car il n’a pas à chercher des sous-cordes.
    *   Préférez utiliser des opérateurs sensibles aux cas le cas échéant, car ils sont plus performants. Par exemple, `==` préférez `=~` `in` utiliser `in~`plus `contains_cs` `contains` , plus, et `contains` / `contains_cs` plus `has` / `has_cs`(mais si vous pouvez éviter tout à fait et utiliser , c’est encore mieux).
*   Préférez regarder dans une colonne `*` spécifique plutôt que d’utiliser (recherche texte complète sur toutes les colonnes)
*   Si vous constatez que la plupart de vos requêtes traitent de l’extraction de champs à partir [d’objets dynamiques](./scalar-data-types/dynamic.md) à travers des millions de lignes, envisagez de matérialiser cette colonne au moment de l’ingestion. De cette façon, vous ne paiez qu’une seule fois pour l’extraction de la colonne.  
*   Si vous `let` avez une déclaration dont vous utilisez la valeur plus d’une fois, envisagez d’utiliser la [fonction matérialisée](./materializefunction.md) (voir quelques [pratiques exemplaires](#materialize-function) sur l’utilisation `materialize()`).

### <a name="dont"></a>À ne pas faire

*   N’essayez pas de `limit [small number]` nouvelles `count` requêtes sans ou à la fin.
    Exécution de requêtes non liées sur l’ensemble de données inconnus peut donner GBs de résultats à retourner au client, ce qui entraîne une réponse lente et le cluster étant occupé.
*   Si vous constatez que vous appliquez des conversions (JSON, chaîne, etc.) plus d’un milliard d’enregistrements - remodelez votre requête pour réduire la quantité de données introduites dans la conversion.
*   N’utilisez `tolower(Col) == "lowercasestring"` pas pour faire des comparaisons insensibles cas. Utilisez plutôt `Col =~ "lowercasestring"`.
    *   Si vos données sont déjà en minuscules (ou uppercase), alors évitez d’utiliser des comparaisons insensibles aux cas, et utilisez `Col == "lowercasestring"` (ou) `Col == "UPPERCASESTRING"`à la place.
*   Ne filtrez pas sur une colonne calculée si vous pouvez filtrer sur une colonne de table. En d’autres `T | extend _value = <expression> | where predicate(_value)`termes: `T | where predicate(<expression>)`au lieu de , faire: .

## <a name="summarize-operator"></a>opérateur summarize

*   Lorsque le groupe par les clés de l’opérateur de résumé sont avec une haute cardinalité (meilleure pratique: plus d’un million), il est recommandé d’utiliser [l’hint.strategy-shuffle](./shufflequery.md).

## <a name="join-operator"></a>opérateur join

*   Lorsque vous utilisez [l’opérateur de jointure,](./joinoperator.md)sélectionnez la table avec moins de lignes pour être la première (gauche-la plupart). 
*   Lorsque vous utilisez les données [de l’opérateur de jointure](./joinoperator.md) à travers les clusters, exécutez la requête sur le côté « droit » de la jointure (où se trouve la plupart des données).
*   Lorsque le côté gauche est petit (jusqu’à 100.000 enregistrements) et le côté droit est grand, utilisez [hint.strategy.broadcast](./broadcastjoin.md).
*   Lorsque les deux côtés de la jointure sont trop grands et la clé de jointure est avec une haute cardinalité, utilisez [hint.strategy-shuffle](./shufflequery.md).
    
## <a name="parse-operator-and-extract-function"></a>analyser l’opérateur et la fonction d’extrait()

*   [l’opérateur d’analyse](./parseoperator.md) (mode simple) est utile lorsque les valeurs de la colonne cible contiennent des chaînes qui partagent toutes le même format ou le même modèle.
Par exemple, pour une `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`colonne avec des valeurs comme , `parse` lors de `extract()` l’extraction des valeurs de chaque champ, utilisez l’opérateur au lieu de plusieurs relevés.
*   [la fonction extraite()](./extractfunction.md) est utile lorsque les cordes analysées ne suivent pas toutes le même format ou le même motif.
Dans de tels cas, extraire les valeurs requises en utilisant un REGEX.

## <a name="materialize-function"></a>fonction matérialisée ()

*   Lors de l’utilisation de la [fonction matérialisée ,,](./materializefunction.md)essayez de pousser tous les opérateurs possibles qui permettra de réduire l’ensemble de données matérialisées et conserve toujours la sémantique de la requête. Par exemple, filtres ou projets uniquement des colonnes requises.
    
    **Exemple :**

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* Le filtre sur le texte est mutuel et peut être poussé à l’expression matérialisée.
    La requête n’a `Timestamp` `Text`besoin `Resource1` `Resource2` que de ces colonnes, , et il est donc recommandé de projeter ces colonnes à l’intérieur de l’expression matérialisée.
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   Si les filtres ne sont pas identiques comme dans cette requête:  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   Il pourrait être utile (lorsque le filtre combiné réduit le résultat matérialisé de façon `or` drastique) pour combiner les deux filtres sur le résultat matérialisé par une expression logique comme dans la requête ci-dessous. Cependant, gardez les filtres dans chaque jambe d’union pour préserver la sémantique de la requête :
     
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```
    
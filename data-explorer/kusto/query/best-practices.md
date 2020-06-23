---
title: Meilleures pratiques pour les requêtes-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les meilleures pratiques relatives aux requêtes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: e6db181188250d28689cca64762e13973f2f33f8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128816"
---
# <a name="best-practices"></a>meilleures pratiques recommandées. 

## <a name="general"></a>Général

Il existe plusieurs « dos et not » que vous pouvez suivre pour accélérer l’exécution des requêtes.

### <a name="do"></a>À faire

*   Utiliser les filtres de temps en premier. Kusto est hautement optimisé pour utiliser des filtres de temps.
*   Quand vous utilisez des opérateurs de chaîne :
    *   Préférer l' `has` opérateur `contains` à lors de la recherche de jetons complets. `has`est plus performant, car il n’a pas besoin de rechercher des sous-chaînes.
    *   Utilisez les opérateurs qui respectent la casse le cas échéant, car ils sont plus performants. Par exemple, préférez l’utilisation de la combinaison de plus, de plus et de plus `==` `=~` `in` `in~` `contains_cs` `contains` (mais si vous pouvez éviter `contains` / `contains_cs` `has` / `has_cs` d’utiliser, c’est encore mieux).
*   Préférez chercher dans une colonne spécifique plutôt que d’utiliser `*` (recherche en texte intégral dans toutes les colonnes)
*   Si vous constatez que la plupart de vos requêtes traitent de l’extraction de champs d' [objets dynamiques](./scalar-data-types/dynamic.md) sur des millions de lignes, envisagez de matérialiser cette colonne au moment de l’ingestion. De cette façon, vous payez une seule fois pour l’extraction de colonne.  
*   Si vous utilisez une `let` instruction à plusieurs reprises, envisagez d’utiliser la [fonction matérialiser ()](./materializefunction.md) (consultez les [meilleures pratiques](#materialize-function) relatives à l’utilisation de `materialize()` ).

### <a name="dont"></a>À ne pas faire

*   N’essayez pas de nouvelles requêtes sans `limit [small number]` ou `count` à la fin.
    L’exécution de requêtes indépendantes sur un jeu de données inconnu peut générer des Go de résultats à retourner au client, ce qui entraîne une réponse lente et le cluster occupé.
*   Si vous constatez que vous appliquez des conversions (JSON, String, etc.) sur 1 milliard enregistrements, remodelez votre requête pour réduire la quantité de données chargées dans la conversion.
*   N’utilisez pas `tolower(Col) == "lowercasestring"` pour effectuer des comparaisons non sensibles à la casse. Utilisez plutôt `Col =~ "lowercasestring"`.
    *   Si vos données sont déjà en minuscules (ou en majuscules), évitez d’utiliser des comparaisons sans respect de la casse et utilisez `Col == "lowercasestring"` (ou) à la `Col == "UPPERCASESTRING"` place.
*   Ne filtrez pas sur une colonne calculée si vous pouvez filtrer sur une colonne de table. En d’autres termes : au lieu de `T | extend _value = <expression> | where predicate(_value)` , do : `T | where predicate(<expression>)` .

## <a name="summarize-operator"></a>opérateur summarize

*   Lorsque les clés Group by de l’opérateur de synthèse sont avec une cardinalité élevée (meilleures pratiques : plus de 1 million), il est recommandé d’utiliser l' [indicateur. Strategy = en lecture](./shufflequery.md)seule.

## <a name="join-operator"></a>opérateur join

*   Lorsque vous utilisez l' [opérateur de jointure](./joinoperator.md), sélectionnez la table contenant moins de lignes que la première (la plus à gauche). 
*   Lorsque vous utilisez des données d' [opérateur de jointure](./joinoperator.md) sur des clusters, exécutez la requête sur le côté « droite » de la jointure (où se trouvent la plupart des données).
*   Lorsque le côté gauche est petit (jusqu’à 100 000 enregistrements) et que le côté droit est Big, utilisez [hint. Strategy = Broadcast](./broadcastjoin.md).
*   Lorsque les deux côtés de la jointure sont trop volumineux et que la clé de jointure est avec une cardinalité élevée, utilisez [hint. Strategy = en lecture](./shufflequery.md)seule.
    
## <a name="parse-operator-and-extract-function"></a>opérateur parse et fonction Extract ()

*   l' [opérateur d’analyse](./parseoperator.md) (mode simple) est utile lorsque les valeurs de la colonne cible contiennent des chaînes qui partagent toutes le même format ou modèle.
Par exemple, pour une colonne avec des valeurs telles que `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."` , lors de l’extraction des valeurs de chaque champ, utilisez l' `parse` opérateur au lieu de plusieurs `extract()` instructions.
*   la [fonction Extract ()](./extractfunction.md) est utile lorsque les chaînes analysées ne suivent pas toutes le même format ou modèle.
Dans ce cas, extrayez les valeurs requises à l’aide d’une expression régulière.

## <a name="materialize-function"></a>matérialise () (fonction)

*   Quand vous utilisez la [fonction matérialiser ()](./materializefunction.md), essayez d’envoyer tous les opérateurs possibles qui réduiront le jeu de données matérialisées et conserve la sémantique de la requête. Par exemple, les filtres, ou projetent uniquement les colonnes requises.
    
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

* Le filtre sur le texte est mutuel et peut faire l’objet d’un push vers l’expression de matérialisation.
    La requête n’a besoin que de ces colonnes `Timestamp` , `Text` , `Resource1` et `Resource2` il est donc recommandé de projeter ces colonnes à l’intérieur de l’expression matérialisée.
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   Si les filtres ne sont pas identiques comme dans cette requête :  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   Cela peut être utile (lorsque le filtre combiné réduit considérablement le résultat matérialisé) pour combiner les deux filtres sur le résultat matérialisé par une `or` expression logique comme dans la requête ci-dessous. Toutefois, conservez les filtres dans chaque jambe d’Union pour préserver la sémantique de la requête :
     
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
    
---
title: Meilleures pratiques relatives aux requêtes – Azure Data Explorer
description: Cet article décrit les meilleures pratiques relatives aux requêtes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: 87154368a033afe1da7669e71e269081865b689d
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359911"
---
# <a name="query-best-practices"></a>Meilleures pratiques relatives aux requêtes

Voici plusieurs meilleures pratiques à suivre pour accélérer l’exécution de votre requête.

|Action  |Utilisation  |N’utilisez pas  |Notes  |
|---------|---------|---------|---------|
| **Filtres de temps** | Utiliser les filtres de temps en premier. ||Kusto est hautement optimisé pour utiliser des filtres de temps.| 
|**Opérateurs de chaîne**      | Utilisez l’opérateur `has`     | N’utilisez pas `contains`     | Lorsque vous recherchez des jetons complets, `has` fonctionne mieux, car il ne recherche pas de sous-chaînes.   |
|**Opérateurs respectant la casse**     |  Utilisez `==`.       | N’utilisez pas `=~`       |  Utilisez des opérateurs respectant la casse quand c’est possible.       |
| | Utilisez `in`. | N’utilisez pas `in~`|
|  | Utilisez `contains_cs`.         | N’utilisez pas `contains`        | Si vous pouvez utiliser `has`/`has_cs` et n’utilisez pas `contains`/`contains_cs`, c’est encore mieux. |
| **Recherche de texte**    |    Recherchez dans une colonne spécifique     |    N’utilisez pas `*`    |   `*` effectue une recherche en texte intégral dans toutes les colonnes.    |
| **Extraire les champs d’[objets dynamiques](./scalar-data-types/dynamic.md) dans plusieurs millions de lignes**    |  Matérialisez votre colonne au moment de l’ingestion si la plupart de vos requêtes extraient des champs d’objets dynamiques dans des millions de lignes.      |         | De cette façon, vous ne payez qu’une seule fois pour l’extraction de colonne.    |
| **Rechercher les clés/valeurs rares dans des [objets dynamiques](./scalar-data-types/dynamic.md)**    |  Utilisez `MyTable | where DynamicColumn has "Rare value" | where DynamicColumn.SomeKey == "Rare value"`. | N’utilisez pas `MyTable | where DynamicColumn.SomeKey == "Rare value"` | De cette façon, vous filtrez la plupart des enregistrements et vous effectuez l’analyse JSON seulement pour le reste. |
| Instruction **`let` avec une valeur que vous utilisez plusieurs fois** | Utiliser la [fonction materialize()](./materializefunction.md) |  |   Pour plus d’informations sur l’utilisation de la fonction `materialize()`, consultez [materialize()](materializefunction.md).|
| **Appliquer des conversions sur plus de 1 milliard d’enregistrements**| Remodelez votre requête pour réduire la quantité de données chargées dans la conversion.| Ne convertissez pas de grandes quantités de données si vous pouvez l’éviter. | |
| **Nouvelles requêtes** | Utilisez `limit [small number]` ou `count` à la fin. | |     L’exécution de requêtes indépendantes sur un ensemble de données inconnu peut engendrer des Go de résultats à retourner au client, entraînant une réponse lente et un cluster occupé.|
| **Comparaisons ne respectant pas la casse** | Utilisez `Col =~ "lowercasestring"`. | N’utilisez pas `tolower(Col) == "lowercasestring"` |
| **Comparer des données déjà en minuscules (ou en majuscules)** | `Col == "lowercasestring"` (ou `Col == "UPPERCASESTRING"`) | Évitez d’utiliser des comparaisons ne respectant pas la casse.||
| **Filtrage sur les colonnes** |  Filtrez sur une colonne de table.|Ne filtrez pas sur une colonne calculée. | |
| | Utilisez `T | where predicate(<expression>)`. | N’utilisez pas `T | extend _value = <expression> | where predicate(_value)` ||
| **opérateur summarize** |  Utilisez [hint.strategy=shuffle](./shufflequery.md) quand les `group by keys` de l’opérateur summarize présentent une cardinalité élevée. | | Une cardinalité élevée est idéalement supérieure à 1 million.|
|**[opérateur join](./joinoperator.md)** | Sélectionnez la table contenant moins de lignes comme première table (la plus à gauche dans la requête). ||
| Jointure entre clusters |Entre clusters, exécutez la requête sur le côté « droit » de la jointure, où se trouvent la plupart des données. ||
|Joindre quand le côté gauche est petit et le côté droit est grand | Utilisez [hint.strategy=broadcast](./broadcastjoin.md) || Petit fait référence à jusqu’à 100 000 enregistrements. |
|Joindre quand les deux côtés sont trop grands | Utilisez [hint.strategy=shuffle](./shufflequery.md) || Utilisez lorsque la clé de jointure présente une cardinalité élevée.|
|**Extraire des valeurs d’une colonne contenant des chaînes partageant les mêmes format ou modèle**|  Utilisez l’[opérateur parse](./parseoperator.md) | N’utilisez pas plusieurs instructions `extract()`.  | Par exemple, des valeurs telles que `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`
|**[Fonction extract()](./extractfunction.md)**| Utilisez-la lorsque les chaînes analysées ne suivent pas toutes les mêmes format ou modèle.| |Extrayez les valeurs requises à l’aide d’une expression régulière.|
| **[Fonction materialize()](./materializefunction.md)** | Transmettez tous les opérateurs possibles qui réduiront le jeu de données matérialisées tout en conservant la sémantique de la requête. | |Par exemple, filtrez, ou projetez uniquement les colonnes requises.


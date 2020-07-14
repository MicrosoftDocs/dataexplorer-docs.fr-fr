---
title: Meilleures pratiques pour les requêtes-Azure Explorateur de données
description: Cet article décrit les meilleures pratiques relatives aux requêtes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 301917f363176fb8e3bbf2fe4286a86b7a5674ea
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280519"
---
# <a name="query-best-practices"></a>Meilleures pratiques relatives aux requêtes

Voici plusieurs bonnes pratiques à suivre pour accélérer l’exécution de votre requête.

|Action  |Utilisation  |N’utilisez pas  |Notes  |
|---------|---------|---------|---------|
| **Filtres de temps** | Utiliser les filtres de temps en premier. ||Kusto est hautement optimisé pour utiliser des filtres de temps.| 
|**Opérateurs de chaîne**      | Utiliser l' `has` opérateur     | N’utilisez pas`contains`     | Lorsque vous recherchez des jetons complets, `has` fonctionne mieux, car il ne recherche pas de sous-chaînes.   |
|**Opérateurs respectant la casse**     |  Utilisez `==`.       | N’utilisez pas`=~`       |  Utilisez les opérateurs respectant la casse lorsque cela est possible.       |
| | Utilisez `in`. | N’utilisez pas`in~`|
|  | Utilisez `contains_cs`.         | N’utilisez pas`contains`        | Si vous pouvez utiliser `has` / `has_cs` et ne pas utiliser `contains` / `contains_cs` , c’est encore mieux. |
| **Recherche de texte**    |    Rechercher dans une colonne spécifique     |    N’utilisez pas`*`    |   `*`effectue une recherche en texte intégral dans toutes les colonnes.    |
| **Extraire des champs d' [objets dynamiques](./scalar-data-types/dynamic.md) sur des millions de lignes**    |  Matérialisez votre colonne au moment de l’ingestion si la plupart de vos requêtes extraient des champs d’objets dynamiques entre des millions de lignes.      |         | De cette façon, vous ne payez qu’une seule fois pour l’extraction de colonne.    |
| **`let`instruction avec une valeur que vous utilisez plusieurs fois** | Utiliser la [fonction matérialiser ()](./materializefunction.md) |  |   Pour plus d’informations sur l’utilisation de `materialize()` , consultez [matérialiser ()](materializefunction.md).|
| **Appliquer des conversions sur plus de 1 milliard enregistrements**| Remodelez votre requête pour réduire la quantité de données chargées dans la conversion.| Ne convertissez pas de grandes quantités de données si elles peuvent être évitées. | |
| **Nouvelles requêtes** | Utilisez `limit [small number]` ou `count` à la fin. | |     L’exécution de requêtes indépendantes sur des jeux de données inconnus peut générer des Go de résultats à retourner au client, ce qui entraîne une réponse lente et un cluster occupé.|
| **Comparaisons ne respectant pas la casse** | Utilisez `Col =~ "lowercasestring"`. | N’utilisez pas`tolower(Col) == "lowercasestring"` |
| **Comparer les données déjà en minuscules (ou en majuscules)** | `Col == "lowercasestring"` (ou `Col == "UPPERCASESTRING"`) | Évitez d’utiliser des comparaisons ne respectant pas la casse.||
| **Filtrage sur les colonnes** |  Filtrez sur une colonne de table.|Ne filtrez pas sur une colonne calculée. | |
| | Utilisez `T | where predicate(<expression>)`. | N’utilisez pas`T | extend _value = <expression> | where predicate(_value)` ||
| **opérateur summarize** |  Utilisez l' [indicateur. Strategy = en lecture](./shufflequery.md) seule lorsque le `group by keys` de l’opérateur de synthèse est avec une cardinalité élevée. | | Une cardinalité élevée est idéalement supérieure à 1 million.|
|**[opérateur join](./joinoperator.md)** | Sélectionnez la table contenant moins de lignes que la première (la plus à gauche dans la requête). ||
| Jointure entre les clusters |Entre les clusters, exécutez la requête sur le côté « droite » de la jointure, où se trouvent la plupart des données. ||
|Joindre si le côté gauche est petit et que la partie droite est grande | Use [hint. Strategy = diffusion](./broadcastjoin.md) || Petit fait référence à jusqu’à 100 000 enregistrements. |
|Joindre lorsque les deux côtés sont trop volumineux | Use [hint. Strategy = lecture aléatoire](./shufflequery.md) || À utiliser lorsque la clé de jointure a une cardinalité élevée.|
|**Extraire des valeurs sur une colonne avec des chaînes partageant le même format ou le même modèle**|  Utiliser l' [opérateur d’analyse](./parseoperator.md) | N’utilisez pas plusieurs `extract()` instructions.  | Par exemple, des valeurs telles que`"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`
|**[fonction Extract ()](./extractfunction.md)**| À utiliser lorsque les chaînes analysées ne suivent pas toutes le même format ou modèle.| |Extrayez les valeurs requises à l’aide d’une expression régulière.|
| **[matérialise () (fonction)](./materializefunction.md)** | Transmettent tous les opérateurs possibles qui réduiront le jeu de données matérialisées tout en gardant la sémantique de la requête. | |Par exemple, les filtres, ou projetent uniquement les colonnes requises.

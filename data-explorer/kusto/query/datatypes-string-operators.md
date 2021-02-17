---
title: Opérateurs String - Azure Data Explorer
description: Cet article décrit les opérateurs String dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 9e8197d3af25da0b0e2488b4a5c70f5cfa2dde17
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528069"
---
# <a name="string-operators"></a>Opérateurs String

Kusto offre un large éventail d'opérateurs de requête pour rechercher des types de données String. L'article suivant explique comment les termes de type String sont indexés, répertorie les opérateurs de requête String, et donne des conseils pour optimiser les performances.

## <a name="understanding-string-terms"></a>Présentation des termes de type String

Kusto indexe toutes les colonnes, y compris les colonnes de type `string`. Des index multiples sont générés pour ces colonnes, en fonction des données réelles. Ces index ne sont pas directement exposés, mais ils sont utilisés dans les requêtes avec les opérateurs `string` qui comportent `has` dans leur nom, comme `has`, `!has`, `hasprefix`, `!hasprefix`. La sémantique de ces opérateurs est dictée par la manière dont la colonne est encodée. Au lieu d'établir une correspondance standard entre substrings, ces opérateurs font correspondre des *termes*.

### <a name="what-is-a-term"></a>Qu'est-ce qu'un terme ? 

Par défaut, chaque valeur `string` se décompose en séquences maximales de caractères alphanumériques ASCII, et chacune de ces séquences est transformée en un terme.
Par exemple, dans la `string` suivante, `Kusto`, `KustoExplorerQueryRun` sont des termes, tandis que `ad67d136`, `c1db`, `4f9f`, `88ef`, `d94f3b6b0b5a` sont des substrings.

```
Kusto: ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;KustoExplorerQueryRun
```

Kusto génère un index composé de tous les termes d’*au moins trois caractères*, et cet index est utilisé par les opérateurs de chaîne `has`, `!has` et ainsi de suite.  Si la requête recherche un terme de moins de trois caractères ou utilise un opérateur `contains`, elle recommence à analyser les valeurs de la colonne. L’analyse est beaucoup plus lente que la recherche du terme dans l’index des termes.

> [!NOTE]
> Dans EngineV2, un terme se compose de quatre caractères ou plus.

## <a name="operators-on-strings"></a>Opérateurs utilisés sur les chaînes

> [!NOTE]
> Les abréviations suivantes sont utilisées dans le tableau ci-dessous :
> * CD = côté droit de l'expression
> * CG = côté gauche de l'expression
> 
> Les opérateurs dotés du suffixe `_cs` respectent la casse.

> [!NOTE]
> Les opérateurs insensibles à la casse sont actuellement pris en charge uniquement pour le texte ASCII. Pour une comparaison non-ASCII, utilisez la fonction [tolower()](tolowerfunction.md).

Opérateur        |Description                                                       |Respecte la casse|Exemple (génère `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Égal à                                                            |Oui           |`"aBc" == "aBc"`
`!=`            |Non égal à                                                        |Oui           |`"abc" != "ABC"`
`=~`            |Égal à                                                            |Non            |`"abc" =~ "ABC"`
`!~`            |Non égal à                                                        |Non            |`"aBc" !~ "xyz"`
`has`           |Le terme de droite est un terme entier dans le terme de gauche     |Non            |`"North America" has "america"`
`!has`          |Le terme de droite n'est pas un terme entier à gauche                                     |Non            |`"North America" !has "amer"` 
[`has_all`](has-all-operator.md)       |Identique à `has`, mais fonctionne sur tous les éléments                    |Non            |`"North and South America" has_all("south", "north")`
[`has_any`](has-anyoperator.md)       |Identique à `has` mais fonctionne sur tous les éléments                    |Non            |`"North America" has_any("south", "north")`
`has_cs`        |Le terme de droite est un terme entier à gauche                                        |Oui           |`"North America" has_cs "America"`
`!has_cs`       |Le terme de droite n'est pas un terme entier à gauche                                     |Oui           |`"North America" !has_cs "amer"` 
`hasprefix`     |Le terme de droite est un préfixe à gauche                                       |Non            |`"North America" hasprefix "ame"`
`!hasprefix`    |Le terme de droite n'est pas un préfixe à gauche                                   |Non            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |Le terme de droite est un préfixe à gauche                                       |Oui           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |Le terme de droite n'est pas un préfixe à gauche                                   |Oui           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |Le terme de droite est un suffixe à gauche                                       |Non            |`"North America" hassuffix "ica"`
`!hassuffix`    |Le terme de droite n'est pas un suffixe à gauche                                   |Non            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |Le terme de droite est un suffixe à gauche                                       |Oui           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |Le terme de droite n'est pas un suffixe à gauche                                   |Oui           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS apparaît comme une sous-séquence de LHS                                |Non            |`"FabriKam" contains "BRik"`
`!contains`     |RHS ne figure pas dans LHS                                         |Non            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS apparaît comme une sous-séquence de LHS                                |Oui           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS ne figure pas dans LHS                                         |Oui           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS est une sous-séquence initiale de LHS                              |Non            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS n’est pas une sous-séquence initiale de LHS                          |Non            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS est une sous-séquence initiale de LHS                              |Oui           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS n’est pas une sous-séquence initiale de LHS                          |Oui           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS est une sous-séquence fermante de LHS                               |Non            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS n’est pas une sous-séquence fermante de LHS                           |Non            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS est une sous-séquence fermante de LHS                               |Oui           |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`  |RHS n’est pas une sous-séquence fermante de LHS                           |Oui           |`"Fabrikam" !endswith_cs "brik"`
`matches regex` |LHS contient une correspondance pour RHS                                      |Oui           |`"Fabrikam" matches regex "b.*k"`
[`in`](inoperator.md)            |Égal à l’un des éléments                                     |Oui           |`"abc" in ("123", "345", "abc")`
[`!in`](inoperator.md)           |N’est égal à aucun des éléments                                 |Oui           |`"bca" !in ("123", "345", "abc")`
`in~`           |Égal à l’un des éléments                                     |Non            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |N’est égal à aucun des éléments                                 |Non            |`"bca" !in~ ("123", "345", "ABC")`


> [!TIP]
> Tous les opérateurs qui contiennent `has` effectuent des recherches sur des *termes* indexés de quatre caractères ou plus, et non sur des correspondances de substrings. Un terme est créé en décomposant la chaîne en séquences de caractères alphanumériques ASCII. Voir [Présentation des termes de type String](#understanding-string-terms).

## <a name="performance-tips"></a>Conseils sur les performances

Pour de meilleures performances, lorsque deux opérateurs effectuent la même tâche, utilisez celui qui respecte la casse.
Par exemple :

* au lieu de `=~`, utilisez `==`
* au lieu de `in~`, utilisez `in`
* au lieu de `contains`, utilisez `contains_cs`

Pour des résultats plus rapides, si vous recherchez la présence d'un symbole ou d'un mot alphanumérique délimité par des caractères non alphanumériques, ou le début ou la fin d'un champ, utilisez `has` ou `in`. 
`has` effectue la recherche plus rapidement que `contains`, `startswith` ou `endswith`.

Par exemple, la première de ces requêtes sera plus rapide :

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

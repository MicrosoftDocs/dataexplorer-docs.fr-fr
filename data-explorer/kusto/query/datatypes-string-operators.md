---
title: Opérateurs de chaîne-Azure Explorateur de données
description: Cet article décrit les opérateurs de chaîne dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.openlocfilehash: c2a841bc78c8f17ac3a929b2541b08d5db682da1
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92175511"
---
# <a name="string-operators"></a>Opérateurs de chaîne

Kusto offre un large éventail d’opérateurs de requête pour rechercher des types de données de chaîne. L’article suivant décrit comment les termes de chaîne sont indexés, répertorie les opérateurs de requête de chaîne et donne des conseils pour optimiser les performances.

## <a name="understanding-string-terms"></a>Comprendre les termes de chaîne

Kusto indexe toutes les colonnes, y compris les colonnes de type `string` . Plusieurs index sont générés pour ces colonnes, en fonction des données réelles. Ces index ne sont pas directement exposés, mais ils sont utilisés dans les requêtes avec les `string` opérateurs qui ont dans le `has` cadre de leur nom, par exemple,,, `has` `!has` `hasprefix` `!hasprefix` . La sémantique de ces opérateurs est dictée par la manière dont la colonne est encodée. Au lieu d’exécuter une correspondance de sous-chaîne « Plain », ces opérateurs correspondent aux *termes*.

### <a name="what-is-a-term"></a>Qu’est-ce qu’un terme ? 

Par défaut, chaque `string` valeur est divisée en séquences maximales de caractères ASCII alphanumériques, et chacune de ces séquences est transformée en un terme.
Par exemple, dans les cas suivants `string` , les termes sont `Kusto` , `WilliamGates3rd` et les sous-chaînes suivantes : `ad67d136` , `c1db` , `4f9f` , `88ef` , `d94f3b6b0b5a` .

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto crée un index de terme composé de tous les termes qui contiennent *quatre caractères ou plus*, et cet index est utilisé par `has` , `!has` , et ainsi de suite. Si la requête recherche un terme d’une taille inférieure à quatre caractères, ou utilise un `contains` opérateur, Kusto rétablit l’analyse des valeurs de la colonne si elle ne peut pas déterminer de correspondance. Cette méthode est beaucoup plus lente que la recherche du terme dans l’index de terme.

## <a name="operators-on-strings"></a>Opérateurs sur les chaînes

> [!NOTE]
> Les abréviations suivantes sont utilisées dans le tableau ci-dessous :
> * RHS = partie droite de l’expression
> * LHS = côté gauche de l’expression

Opérateur        |Description                                                       |Respecte la casse|Exemple (génère `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Égal à                                                            |Oui           |`"aBc" == "aBc"`
`!=`            |Non égal à                                                        |Oui           |`"abc" != "ABC"`
`=~`            |Égal à                                                            |Non            |`"abc" =~ "ABC"`
`!~`            |Non égal à                                                        |Non            |`"aBc" !~ "xyz"`
`has`           |Le terme de droite est un terme entier dans le terme de gauche     |Non            |`"North America" has "america"`
`!has`          |RHS n’est pas un terme complet dans LHS                                     |Non            |`"North America" !has "amer"` 
`has_cs`        |RHS est un terme complet dans LHS                                        |Oui           |`"North America" has_cs "America"`
`!has_cs`       |RHS n’est pas un terme complet dans LHS                                     |Oui           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS est un préfixe de terme dans LHS                                       |Non            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS n’est pas un préfixe de terme dans LHS                                   |Non            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS est un préfixe de terme dans LHS                                       |Oui           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS n’est pas un préfixe de terme dans LHS                                   |Oui           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS est un suffixe de terme dans LHS                                       |Non            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS n’est pas un suffixe de terme dans LHS                                   |Non            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS est un suffixe de terme dans LHS                                       |Oui           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS n’est pas un suffixe de terme dans LHS                                   |Oui           |`"North America" !hassuffix_cs "icA"`
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
`in`            |Égal à l’un des éléments                                     |Oui           |`"abc" in ("123", "345", "abc")`
`!in`           |N’est égal à aucun des éléments                                 |Oui           |`"bca" !in ("123", "345", "abc")`
`in~`           |Égal à l’un des éléments                                     |Non            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |N’est égal à aucun des éléments                                 |Non            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |Identique à, `has` mais fonctionne sur l’un des éléments                    |Non            |`"North America" has_any("south", "north")`

> [!TIP]
> Tous les opérateurs qui contiennent `has` des recherches sur des *termes* indexés de quatre caractères ou plus, et non sur des correspondances de sous-chaînes. Un terme est créé en fractionnant la chaîne en séquences de caractères ASCII alphanumériques. Consultez [comprendre les termes de chaîne](#understanding-string-terms).

## <a name="performance-tips"></a>Conseils sur les performances

Pour de meilleures performances, lorsque deux opérateurs effectuent la même tâche, utilisez la casse.
Par exemple :

* au lieu de `=~` , utilisez `==`
* au lieu de `in~` , utilisez `in`
* au lieu de `contains` , utilisez `contains_cs`

Pour accélérer les résultats, si vous testez la présence d’un symbole ou d’un mot alphanumérique lié par des caractères non alphanumériques, ou le début ou la fin d’un champ, utilisez `has` ou `in` . 
`has` fonctionne plus rapidement que `contains` , `startswith` ou `endswith` .

Par exemple, la première de ces requêtes s’exécutera plus rapidement :

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

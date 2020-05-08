---
title: Opérateurs de chaîne-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les opérateurs de chaîne dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e16d90c8307392536b5971758ce7df15a9ea1b46
ms.sourcegitcommit: ef009294b386cba909aa56d7bd2275a3e971322f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977131"
---
# <a name="string-operators"></a>Opérateurs de chaîne

Le tableau suivant récapitule les opérateurs sur les chaînes :

Opérateur        |Description                                                       |Respecte la casse|Exemple (génère `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Égal à                                                            |Oui           |`"aBc" == "aBc"`
`!=`            |Non égal à                                                        |Oui           |`"abc" != "ABC"`
`=~`            |Égal à                                                            |Non            |`"abc" =~ "ABC"`
`!~`            |Non égal à                                                        |Non            |`"aBc" !~ "xyz"`
`has`           |Le terme de droite est un terme entier dans le terme de gauche     |Non             |`"North America" has "america"`
`!has`          |Le terme de droite n’est pas un terme entier dans le terme de gauche                                     |Non             |`"North America" !has "amer"` 
`has_cs`        |Le terme de droite est un terme entier dans le terme de gauche     |Oui           |`"North America" has_cs "America"`
`!has_cs`       |Le terme de droite n’est pas un terme entier dans le terme de gauche                                     |Oui           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS est un préfixe de terme dans LHS                                       |Non             |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS n’est pas un préfixe de terme dans LHS                                   |Non             |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS est un préfixe de terme dans LHS                                       |Oui           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS n’est pas un préfixe de terme dans LHS                                   |Oui           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS est un suffixe de terme dans LHS                                       |Non             |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS n’est pas un suffixe de terme dans LHS                                   |Non             |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS est un suffixe de terme dans LHS                                       |Oui           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS n’est pas un suffixe de terme dans LHS                                   |Oui           |`"North America" !hassuffix_cs "icA"`
`contains`      |Le terme de droite est une sous-séquence du terme de gauche                                |Non             |`"FabriKam" contains "BRik"`
`!contains`     |Le terme de droite n’est pas une sous-séquence du terme de gauche                                         |Non             |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Le terme de droite est une sous-séquence du terme de gauche                                |Oui           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Le terme de droite n’est pas une sous-séquence du terme de gauche                                         |Oui           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS est une sous-séquence initiale de LHS                              |Non             |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS n’est pas une sous-séquence initiale d’LHS                          |Non             |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS est une sous-séquence initiale de LHS                              |Oui           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS n’est pas une sous-séquence initiale d’LHS                          |Oui           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS est une sous-séquence fermante de LHS                               |Non             |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS n’est pas une sous-séquence de fermeture de LHS                           |Non             |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS est une sous-séquence fermante de LHS                               |Oui           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS n’est pas une sous-séquence de fermeture de LHS                           |Oui           |`"Fabrikam" !endswith "brik"`
`matches regex` |Le terme de gauche contient une correspondance du terme de droite                                      |Oui           |`"Fabrikam" matches regex "b.*k"`
`in`            |Égal à l’un des éléments                                     |Oui           |`"abc" in ("123", "345", "abc")`
`!in`           |N’est égal à aucun des éléments                                 |Oui           |`"bca" !in ("123", "345", "abc")`
`in~`           |Égal à l’un des éléments                                     |Non             |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |N’est égal à aucun des éléments                                 |Non             |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |Identique à `has` , mais fonctionne sur l’un des éléments                    |Non             |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>Conseils sur les performances

Pour de meilleures performances, lorsque deux opérateurs effectuent la même tâche, utilisez la casse.
Par exemple :

* au lieu `=~`de, utilisez`==`
* au lieu `in~`de, utilisez`in`
* au lieu `contains`de, utilisez`contains_cs`

Pour accélérer les résultats, si vous testez la présence d’un symbole ou d’un mot alphanumérique lié par des caractères non alphanumériques (ou le début ou la fin d’un champ) `has` , `in`utilisez ou. `has`est plus rapide `contains`que `startswith`, ou `endswith`.

Par exemple, la première de ces requêtes s’exécute plus rapidement :

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>Comprendre les termes de chaîne

Par défaut, Kusto indexe toutes les colonnes, y compris les `string`colonnes de type.
En fait, plusieurs index sont générés pour ces colonnes, en fonction des données réelles. Pour l’utilisateur, ces index ne sont pas directement exposés (à l’exception `string` de leur effet positif sur les performances des requêtes bien évidemment), à l’exception `has` des opérateurs qui ont dans leur `has`nom `!has`: `hasprefix`, `!hasprefix`,,, etc. Ces opérateurs sont spéciaux dans le sens où leur sémantique est dictée par la manière dont la colonne est encodée ; au lieu d’exécuter une correspondance de sous-chaîne « Plain », ces opérateurs correspondent aux **termes**.

Pour comprendre la correspondance à base de termes, il faut d’abord comprendre ce qu’est un terme. Par défaut, Kusto divise chaque `string` valeur en séquences maximales de caractères ASCII alphanumériques, et chacune d’entre elles est transformée en un terme. Par exemple, dans les cas `string`suivants, les termes `Kusto`sont `WilliamGates3rd`, et les sous-chaînes suivantes : `ad67d136`, `c1db`, `4f9f`, `88ef`, `d94f3b6b0b5a`:

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Par défaut, Kusto crée un index de terme composé de tous les termes qui contiennent **quatre caractères ou plus**, et cet index est `has`utilisé `!has`par,, etc. lors de la recherche de termes qui comportent également quatre caractères ou plus. (Si la requête recherche un terme d’une taille inférieure à quatre caractères, ou utilise un `contains` opérateur par exemple, Kusto rétablit l’analyse des valeurs de la colonne s’il ne peut pas déterminer une correspondance, ce qui est beaucoup plus lent que la recherche du terme dans le terme index.)


---
title: Opérateurs de chaîne-Azure Explorateur de données
description: Cet article décrit les opérateurs de chaîne dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6718ad614166d5328dcd412d09405c1db8cc14dc
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265080"
---
# <a name="string-operators"></a>Opérateurs de chaîne

Le tableau suivant récapitule les opérateurs sur les chaînes :

Opérateur        |Description                                                       |Respecte la casse|Exemple (génère `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Égal à                                                            |Yes           |`"aBc" == "aBc"`
`!=`            |Non égal à                                                        |Yes           |`"abc" != "ABC"`
`=~`            |Égal à                                                            |Non            |`"abc" =~ "ABC"`
`!~`            |Non égal à                                                        |Non            |`"aBc" !~ "xyz"`
`has`           |Le terme de droite est un terme entier dans le terme de gauche     |No            |`"North America" has "america"`
`!has`          |RHS n’est pas un terme complet dans LHS                                     |No            |`"North America" !has "amer"` 
`has_cs`        |Le terme de droite est un terme entier dans le terme de gauche     |Yes           |`"North America" has_cs "America"`
`!has_cs`       |RHS n’est pas un terme complet dans LHS                                     |Yes           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS est un préfixe de terme dans LHS                                       |No            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS n’est pas un préfixe de terme dans LHS                                   |No            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS est un préfixe de terme dans LHS                                       |Yes           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS n’est pas un préfixe de terme dans LHS                                   |Yes           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS est un suffixe de terme dans LHS                                       |No            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS n’est pas un suffixe de terme dans LHS                                   |No            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS est un suffixe de terme dans LHS                                       |Yes           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS n’est pas un suffixe de terme dans LHS                                   |Yes           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS apparaît comme une sous-séquence de LHS                                |No            |`"FabriKam" contains "BRik"`
`!contains`     |RHS ne figure pas dans LHS                                         |No            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS apparaît comme une sous-séquence de LHS                                |Yes           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS ne figure pas dans LHS                                         |Yes           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS est une sous-séquence initiale de LHS                              |No            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS n’est pas une sous-séquence initiale de LHS                          |No            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS est une sous-séquence initiale de LHS                              |Yes           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS n’est pas une sous-séquence initiale de LHS                          |Yes           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS est une sous-séquence fermante de LHS                               |No            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS n’est pas une sous-séquence fermante de LHS                           |No            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS est une sous-séquence fermante de LHS                               |Yes           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS n’est pas une sous-séquence fermante de LHS                           |Yes           |`"Fabrikam" !endswith "brik"`
`matches regex` |Le terme de gauche contient une correspondance du terme de droite                                      |Yes           |`"Fabrikam" matches regex "b.*k"`
`in`            |Égal à l’un des éléments                                     |Oui           |`"abc" in ("123", "345", "abc")`
`!in`           |N’est égal à aucun des éléments                                 |Oui           |`"bca" !in ("123", "345", "abc")`
`in~`           |Égal à l’un des éléments                                     |No            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |N’est égal à aucun des éléments                                 |No            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |Identique à, `has` mais fonctionne sur l’un des éléments                    |No            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>Conseils sur les performances

Pour de meilleures performances, lorsque deux opérateurs effectuent la même tâche, utilisez la casse.
Par exemple :

* au lieu de `=~` , utilisez`==`
* au lieu de `in~` , utilisez`in`
* au lieu de `contains` , utilisez`contains_cs`

Pour accélérer les résultats, si vous testez la présence d’un symbole ou d’un mot alphanumérique lié par des caractères non alphanumériques, ou le début ou la fin d’un champ, utilisez `has` ou `in` . 
`has`fonctionne plus rapidement que `contains` , `startswith` ou `endswith` .

Par exemple, la première de ces requêtes s’exécutera plus rapidement :

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>Comprendre les termes de chaîne

Par défaut, Kusto indexe toutes les colonnes, y compris les colonnes de type `string` .
Plusieurs index sont générés pour ces colonnes, en fonction des données réelles. Ces index ne sont pas directement exposés (à l’exception de leur effet positif sur les performances des requêtes), à l’exception des `string` opérateurs qui ont dans `has` leur nom, tels que,,, `has` `!has` `hasprefix` `!hasprefix` .
Ces opérateurs sont spéciaux, car leur sémantique est dictée par la manière dont la colonne est encodée. Au lieu d’exécuter une correspondance de sous-chaîne « Plain », ces opérateurs correspondent aux **termes**.

Pour comprendre la correspondance à base de termes, vous devez d’abord comprendre ce qu’est un terme. Par défaut, chaque `string` valeur est divisée en séquences maximales de caractères ASCII alphanumériques, et chacune de ces séquences est transformée en un terme.

Par exemple, dans les cas suivants `string` , les termes sont `Kusto` , `WilliamGates3rd` et les sous-chaînes suivantes : `ad67d136` , `c1db` , `4f9f` , `88ef` , `d94f3b6b0b5a` .

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Par défaut, Kusto crée un index de terme composé de tous les termes qui contiennent **quatre caractères ou plus**, et cet index est utilisé par `has` , `!has` , et ainsi de suite, lors de la recherche de termes qui comportent également quatre caractères ou plus.
Si la requête recherche un terme d’une taille inférieure à quatre caractères, ou utilise un `contains` opérateur, Kusto rétablit l’analyse des valeurs de la colonne si elle ne peut pas déterminer de correspondance. Cette méthode est beaucoup plus lente que la recherche du terme dans l’index de terme.

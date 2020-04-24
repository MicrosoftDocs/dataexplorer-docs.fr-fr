---
title: Opérateurs de cordes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les opérateurs de cordes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91955ef5877e6c054917f70c655fc4b7cd3f277b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516471"
---
# <a name="string-operators"></a>Opérateurs de chaîne

Le tableau suivant résume les opérateurs sur les cordes :

Opérateur        |Description                                                       |Respecte la casse|Exemple (génère `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Égal à                                                            |Oui           |`"aBc" == "aBc"`
`!=`            |Non égal à                                                        |Oui           |`"abc" != "ABC"`
`=~`            |Égal à                                                            |Non            |`"abc" =~ "ABC"`
`!~`            |Non égal à                                                        |Non            |`"aBc" !~ "xyz"`
`has`           |Le terme de droite est un terme entier dans le terme de gauche     |Non            |`"North America" has "america"`
`!has`          |Le terme de droite n’est pas un terme entier dans le terme de gauche                                     |Non            |`"North America" !has "amer"` 
`has_cs`        |Le terme de droite est un terme entier dans le terme de gauche     |Oui           |`"North America" has_cs "America"`
`!has_cs`       |Le terme de droite n’est pas un terme entier dans le terme de gauche                                     |Oui           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS est un préfixe terme dans LHS                                       |Non            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS n’est pas un préfixe terme dans LHS                                   |Non            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS est un préfixe terme dans LHS                                       |Oui           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS n’est pas un préfixe terme dans LHS                                   |Oui           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS est un terme suffixe dans LHS                                       |Non            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS n’est pas un suffixe terme dans LHS                                   |Non            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS est un terme suffixe dans LHS                                       |Oui           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS n’est pas un suffixe terme dans LHS                                   |Oui           |`"North America" !hassuffix_cs "icA"`
`contains`      |Le terme de droite est une sous-séquence du terme de gauche                                |Non            |`"FabriKam" contains "BRik"`
`!contains`     |Le terme de droite n’est pas une sous-séquence du terme de gauche                                         |Non            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Le terme de droite est une sous-séquence du terme de gauche                                |Oui           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Le terme de droite n’est pas une sous-séquence du terme de gauche                                         |Oui           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS est une sous-séquence initiale de LHS                              |Non            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS n’est pas un subsequence initial de LHS                          |Non            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS est une sous-séquence initiale de LHS                              |Oui           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS n’est pas un subsequence initial de LHS                          |Oui           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS est une sous-séquence fermante de LHS                               |Non            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS n’est pas un subséquence de fermeture de LHS                           |Non            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS est une sous-séquence fermante de LHS                               |Oui           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS n’est pas un subséquence de fermeture de LHS                           |Oui           |`"Fabrikam" !endswith "brik"`
`matches regex` |Le terme de gauche contient une correspondance du terme de droite                                      |Oui           |`"Fabrikam" matches regex "b.*k"`
`in`            |Égal à l’un des éléments                                     |Oui           |`"abc" in ("123", "345", "abc")`
`!in`           |N’est égal à aucun des éléments                                 |Oui           |`"bca" !in ("123", "345", "abc")`
`in~`           |Égal à l’un des éléments                                     |Non            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |N’est égal à aucun des éléments                                 |Non            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |Même `has` chose que mais travaille sur l’un des éléments                    |Non            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>Conseils sur les performances

Pour de meilleures performances, lorsqu’il y a deux opérateurs qui exécutent la même tâche, utilisez le cas sensible.
Par exemple :

* au `=~`lieu de , utiliser`==`
* au `in~`lieu de , utiliser`in`
* au `contains`lieu de , utiliser`contains_cs`

Pour des résultats plus rapides, si vous testez la présence d’un symbole ou d’un mot alphanumérique qui `has` `in`est lié par des caractères non alphanumériques (ou le début ou la fin d’un champ), utilisez ou . `has`fonctionne plus `contains`vite `startswith`que `endswith`, ou .

Par exemple, la première de ces requêtes s’exécute plus rapidement :

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>Comprendre les termes de la chaîne

Par défaut, Kusto indexe toutes les `string`colonnes, y compris les colonnes de type .
En fait, plusieurs index sont conçus pour ces colonnes, selon les données réelles. Pour l’utilisateur, ces index ne sont pas directement exposés (autre que leur effet `string` positif `has` sur les performances `has`de `!has` `hasprefix`requête `!hasprefix`bien sûr) à l’exception des opérateurs qui ont dans le cadre de leur nom: , , , , etc. Ces opérateurs sont spéciaux en ce sens que leur sémantique est dictée par la façon dont la colonne est codée; au lieu de faire un "plain" match de sous-corde, ces opérateurs correspondent **aux termes**.

Pour comprendre le match basé sur le terme, il faut d’abord comprendre ce qu’est un terme. Par défaut, Kusto `string` divise chaque valeur en séquences maximales de caractères alphanumériques ASCII, et chacun d’eux est transformé en terme. Par exemple, dans `string`les termes `Kusto` `WilliamGates3rd`suivants , les termes sont `ad67d136` `c1db`, `4f9f` `88ef`, `d94f3b6b0b5a`et les sous-cordes suivantes: , , , , :

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Par défaut, Kusto construit un indice terme composé de tous les termes qui `has` `!has`sont quatre caractères ou plus, et cet index est utilisé par , , etc lorsque vous cherchez des termes qui sont également quatre caractères ou plus. (Si la requête recherche un terme qui est plus `contains` petit que quatre caractères, ou utilise un opérateur par exemple, Kusto reviendra à la numérisation des valeurs dans la colonne si elle ne peut pas déterminer une correspondance, qui est beaucoup plus lent que de regarder le terme dans l’indice terme.)


---
title: Noms d’entités - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les noms d’entités dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: 2fecbd538a53d8eb02e55b0bd1def2186467b019
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509416"
---
# <a name="entity-names"></a>Noms d’entité

Les entités Kusto (bases de données, tableaux, colonnes et fonctions stockées; clusters sont une exception) sont nommées. Le nom d’une entité identifie l’entité, et est garanti d’être unique dans la portée de son conteneur étant donné son type.
(Ainsi, par exemple, deux tables dans la même base de données ne peuvent pas avoir le même nom, mais une table et une base de données peuvent avoir le même nom parce qu’ils ne sont pas dans la même portée, et un tableau et une fonction stockée peuvent avoir le même nom parce qu’ils ne sont pas du même type d’entité.)

Les noms d’entités sont **sensibles aux cas** pour la résolution des `ThisTable` `thisTABLE`objectifs (donc, par exemple, vous ne pouvez pas vous référer à un tableau appelé comme ).

Les noms d’entités sont un exemple **d’identifiants**. D’autres identificateurs incluent les noms des paramètres aux fonctions et lier un nom par une [instruction de laisser](../letstatement.md).

## <a name="entity-pretty-names"></a>Entité joli noms

Certaines entités (comme les bases de données) peuvent avoir, en plus de leur nom d’entité, un **joli nom**. Les jolis noms peuvent être utilisés pour référencer l’entité dans les requêtes (comme les noms d’entité), mais, contrairement aux noms d’entités, les jolis noms ne sont pas nécessairement uniques dans le contexte de leur conteneur. Quand un conteneur a plusieurs entités avec le même joli nom, le joli nom ne peut pas être utilisé pour référencer l’entité.

Les jolis noms permettent aux applications de niveau intermédiaire de cartographier automatiquement les noms d’entités (tels que les UUID) aux noms qui sont lisibles par l’homme à des fins d’affichage et de référencement.

## <a name="identifier-naming-rules"></a>Règles de nommage d’identification

<!-- TODO: This section should be reviewed and moved to its own page -->

Les identifiants sont utilisés pour nommer diverses entités (entités ou autres).
Les noms d’identifiant valides suivent ces règles :
* Ils ont entre 1 et 1024 caractères de long.
* Ils peuvent contenir des lettres,`_`des chiffres, des`.`soulignes (`-`), des espaces, des points ( ), et des tirets ( ).
  * Les identifiants composés uniquement de lettres, de chiffres et de soulignes ne nécessitent pas de citation lorsque l’identifiant est référencé.
  * Les identificateurs contenant enfin un (espaces, points ou tirets) nécessitent une citation (voir ci-dessous).
* Ils sont sensibles aux cas.

## <a name="identifier-quoting"></a>Citation d’identification

Les identifiants identiques à certains mots clés de langage de requête, ou ayant l’un des caractères spéciaux notés ci-dessus, exigent de citer quand ils sont référencés directement par une requête :

|Texte de la requête         |Commentaires                          |
|-------------------|----------------------------------|
| `entity`          |Les noms`entity`d’entités ( ) qui n’incluent pas les caractères spéciaux ou la carte à un mot clé de langue ne nécessitent aucune citation|
|`['entity-name']`  |Les noms d’entités `-`qui incluent des `['` caractères spéciaux (ici : ) doivent être cités à l’aide et `']` à l’utilisation et à l’utilisation `["` et`"]`|
|`["where"]`        |Les noms d’entités qui sont des `['` `']` mots `["` clés de langue doivent être cités à l’aide et à l’utilisation et à l’utilisation et`"]`|

## <a name="naming-your-entities-to-avoid-collisions-with-kusto-language-keywords"></a>Nommer vos entités pour éviter les collisions avec les mots clés en langue Kusto

Comme le langage de requête Kusto comprend un certain nombre de mots clés qui ont les mêmes règles de nommage que les identificateurs, il est possible d’avoir des noms d’entité qui sont en fait des mots clés, mais ensuite se référer à ces noms devient difficile (il faut les citer).

Alternativement, on pourrait vouloir choisir des noms d’entité qui sont garantis de ne jamais "collide" avec un mot clé Kusto. Les garanties suivantes sont faites :

1. Le langage de requête Kusto ne définira pas`A` un `Z`mot clé qui commence par une majuscule (à ).
2. Le langage de requête Kusto ne définira pas`_`un mot clé qui commence par un seul soulignement ( ).nous
3. Le langage de requête Kusto ne définira pas`_`un mot clé qui se termine par un seul soulignement ().

Le langage de requête Kusto réserve tous les identificateurs`__`qui commencent ou se terminent par une séquence de deux caractères de soulignement (); les utilisateurs ne peuvent pas définir ces noms pour leur propre usage.









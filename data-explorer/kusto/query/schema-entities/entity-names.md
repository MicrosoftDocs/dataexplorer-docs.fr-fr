---
title: Noms d’entité-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les noms d’entités dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: a155accc2072e59ef74a1f5f39ff33e652493666
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324344"
---
# <a name="entity-names"></a>Noms d’entité

Les entités Kusto (bases de données, tables, colonnes et fonctions stockées) sont nommées. Le nom d’une entité identifie l’entité et est garanti comme étant unique dans la portée de son conteneur en fonction de son type.
(Ainsi, par exemple, deux tables dans la même base de données ne peuvent pas avoir le même nom, mais une table et une base de données peuvent avoir le même nom, car elles ne sont pas dans la même étendue, et une table et une fonction stockée peuvent avoir le même nom, car elles ne sont pas du même type d’entité.)

Les noms d’entités respectent la **casse** à des fins de résolution (par exemple, vous ne pouvez pas faire référence à une table appelée `ThisTable` `thisTABLE` ).

Les noms d’entités sont un exemple d' **identificateurs**. Les autres identificateurs incluent les noms des paramètres des fonctions et la liaison d’un nom par le biais d’une [instruction Let](../letstatement.md).

## <a name="entity-pretty-names"></a>Noms de l’entité

Certaines entités (telles que des bases de données) peuvent avoir, en plus de leur nom d’entité, un **nom convivial**. Les noms convivials peuvent être utilisés pour faire référence à l’entité dans des requêtes (comme les noms d’entités), mais contrairement aux noms d’entités, les noms plutôt uniques ne sont pas nécessairement uniques dans le contexte de leur conteneur. Lorsqu’un conteneur a plusieurs entités avec le même nom convivial, le nom convivial ne peut pas être utilisé pour faire référence à l’entité.

Les noms assez évocateurs permettent aux applications de couche intermédiaire de mapper automatiquement des noms d’entité (tels que des UUID) à des noms lisibles par l’utilisateur à des fins d’affichage et de référencement.

## <a name="identifier-naming-rules"></a>Règles d’attribution de noms aux identificateurs

Les identificateurs sont utilisés pour nommer différentes entités (entités ou autres).
Les noms d’identificateurs valides suivent les règles suivantes :
* Ils ont une longueur comprise entre 1 et 1024 caractères.
* Ils peuvent contenir des lettres, des chiffres, des traits de soulignement ( `_` ), des espaces, des points ( `.` ) et des tirets ( `-` ).
  * Les identificateurs composés uniquement de lettres, de chiffres et de traits de soulignement ne nécessitent pas de guillemets lorsque l’identificateur est référencé.
  * Les identificateurs contenant au dernier (des espaces, des points ou des tirets) requièrent des guillemets (voir ci-dessous).
* Ils respectent la casse.

## <a name="identifier-quoting"></a>Identificateur Quotation

Les identificateurs qui sont identiques à certains mots clés du langage de requête ou qui ont l’un des caractères spéciaux indiqués ci-dessus, nécessitent un guillemet lorsqu’ils sont directement référencés par une requête :

|Texte de la requête         |Commentaires                          |
|-------------------|----------------------------------|
| `entity`          |Les noms d’entité ( `entity` ) qui n’incluent pas de caractères spéciaux ou ne correspondent pas à un mot clé de langage ne nécessitent pas de guillemets.|
|`['entity-name']`  |Les noms d’entités qui incluent des caractères spéciaux (ici : `-` ) doivent être entre guillemets à l’aide `['` de et `']` ou avec `["` et `"]`|
|`["where"]`        |Les noms d’entités qui sont des mots clés de langage doivent être mis entre guillemets avec `['` et `']` ou à l’aide `["` de et `"]`|

## <a name="naming-your-entities-to-avoid-collisions-with-kusto-language-keywords"></a>Attribution d’un nom à vos entités pour éviter les collisions avec les mots clés du langage Kusto

Comme le langage de requête Kusto comprend un certain nombre de mots clés qui ont les mêmes règles d’affectation de noms que les identificateurs, il est possible d’avoir des noms d’entité qui sont en fait des mots clés, mais la référence à ces noms devient alors difficile (l’un doit les citer).

Vous pouvez également choisir des noms d’entité qui sont assurés de ne jamais « entrer en conflit » avec un mot clé Kusto. Les garanties suivantes sont effectuées :

1. Le langage de requête Kusto ne définit pas un mot clé qui commence par une lettre majuscule ( `A` à `Z` ).
2. Le langage de requête Kusto ne définit pas un mot clé qui commence par un trait de soulignement simple ( `_` ). us
3. Le langage de requête Kusto ne définit pas un mot clé qui se termine par un trait de soulignement simple ( `_` ).

Le langage de requête Kusto réserve tous les identificateurs qui commencent ou se terminent par une séquence de deux caractères de soulignement ( `__` ); les utilisateurs ne peuvent pas définir de tels noms pour leur propre utilisation.









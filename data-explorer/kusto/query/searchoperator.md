---
title: opérateur de recherche-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur de recherche dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: edd35e5e259666e8ce4360c072aaac6717e6f8c3
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071871"
---
# <a name="search-operator"></a>opérateur search

L’opérateur de recherche fournit une expérience de recherche dans plusieurs tables ou plusieurs colonnes.

## <a name="syntax"></a>Syntaxe

* [*TabularSource* `|` ] `search`[ `kind=` *CaseSensitivity*] [ `in` `(` *TableSources* `)` ] *SearchPredicate*

## <a name="arguments"></a>Arguments

* *TabularSource*: expression tabulaire facultative qui agit comme source de données à rechercher, par exemple un nom de table, un opérateur d' [Union](unionoperator.md), les résultats d’une requête tabulaire, etc. Ne peut pas apparaître en même temps que la phrase facultative qui comprend *TableSources*.

* *CaseSensitivity*: indicateur facultatif qui contrôle le comportement de tous les `string` opérateurs scalaires en ce qui concerne le respect de la casse. Les valeurs valides sont les deux synonymes `default` et `case_insensitive` (qui est la valeur par défaut pour les opérateurs tels que, à la différence de la `has` casse) et `case_sensitive` (qui force tous les opérateurs de ce type à respecter la casse).

* *TableSources*: liste facultative séparée par des virgules de noms de tables « avec caractères génériques » pour participer à la recherche.
  La liste a la même syntaxe que la liste de l' [opérateur Union](unionoperator.md).
  Ne peut pas apparaître en même temps que le *TabularSource*facultatif.

* *SearchPredicate*: prédicat obligatoire qui définit les éléments à rechercher (en d’autres termes, une expression booléenne qui est évaluée pour chaque enregistrement dans l’entrée et qui, si elle retourne `true` , l’enregistrement est sorti.) La syntaxe pour *SearchPredicate* étend et modifie la syntaxe Kusto normale pour les expressions booléennes :

  **Extensions de correspondance de chaîne**: les littéraux de chaîne qui apparaissent comme termes dans le *SearchPredicate* indiquent une correspondance de terme entre toutes les colonnes et le littéral à l’aide `has` `hasprefix` de,, `hassuffix` et les versions inversées ( `!` ) ou respectant la casse ( `sc` ) de ces opérateurs. La décision d’appliquer `has` , `hasprefix` ou `hassuffix` varie selon que le littéral démarre ou se termine (ou les deux) par un astérisque ( `*` ). Les astérisques à l’intérieur du littéral ne sont pas autorisés.

    |Littéral   |Opérateur   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **Restriction de colonne**: par défaut, les extensions de correspondance de chaîne essaient de correspondre à toutes les colonnes du jeu de données. Il est possible de restreindre cette correspondance à une colonne particulière à l’aide de la syntaxe suivante : *ColumnName* `:` *StringLiteral*.

  **Égalité des chaînes**: les correspondances exactes d’une colonne par rapport à une valeur de chaîne (au lieu d’une correspondance de termes) peuvent être effectuées à l’aide de la syntaxe *ColumnName* `==` *StringLiteral*.

  **Autres expressions booléennes**: toutes les expressions booléennes Kusto régulières sont prises en charge par la syntaxe.
    Par exemple, `"error" and x==123` signifie : Rechercher les enregistrements qui contiennent le terme `error` dans l’une de leurs colonnes et qui ont la valeur `123` dans la `x` colonne.

  **Correspondance Regex**: la correspondance d’expression régulière est *Column* indiquée à l’aide de `matches regex` la syntaxe de colonne *StringLiteral* , où *StringLiteral* est le modèle Regex.

Notez que si *TabularSource* et *TableSources* sont tous les deux omis, la recherche est effectuée sur toutes les tables et vues non restreintes de la base de données dans l’étendue.

## <a name="summary-of-string-matching-extensions"></a>Résumé des extensions de correspondance de chaîne

  |# |Syntaxe                                 |Signification (équivalente `where` )           |Commentaires|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab.*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |Toutes les comparaisons de chaînes respectent la casse|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>Remarques

**Contrairement** à l' [opérateur Find](findoperator.md), l' `search` opérateur ne prend pas en charge les éléments suivants :

1. `withsource=`: La sortie inclut toujours une colonne appelée `$table` de type `string` dont la valeur est le nom de la table à partir de laquelle chaque enregistrement a été récupéré (ou un nom généré par le système si la source n’est pas une table mais une expression composite).
2. `project=`, `project-smart` : Le schéma de sortie est équivalent au `project-smart` schéma de sortie.

## <a name="examples"></a>Exemples

```kusto
// 1. Simple term search over all unrestricted tables and views of the database in scope
search "billg"

// 2. Like (1), but looking only for records that match both terms
search "billg" and ("steveb" or "satyan")

// 3. Like (1), but looking only in the TraceEvent table
search in (TraceEvent) and "billg"

// 4. Like (2), but performing a case-sensitive match of all terms
search "BillB" and ("SteveB" or "SatyaN")

// 5. Like (1), but restricting the match to some columns
search CEO:"billg" or CSA:"billg"

// 6. Like (1), but only for some specific time limit
search "billg" and Timestamp >= datetime(1981-01-01)

// 7. Searches over all the higher-ups
search in (C*, TF) "billg" or "davec" or "steveb"

// 8. A different way to say (7). Prefer to use (7) when possible
union C*, TF | search "billg" or "davec" or "steveb"
```

## <a name="performance-tips"></a>Conseils pour les performances

  |# |Conseil                                                                                  |Prefer                                        |Over                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| Préférer utiliser un opérateur unique `search` sur plusieurs opérateurs consécutifs `search`|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| Préférer le filtre à l’intérieur de l' `search` opérateur                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||

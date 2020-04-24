---
title: opérateur de recherche - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de recherche dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de63aa3fde421996809334b8a746ea4dee8cb026
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509195"
---
# <a name="search-operator"></a>opérateur search

L’opérateur de recherche offre une expérience de recherche multi-tables/multi-colonnes.

## <a name="syntax"></a>Syntaxe

* [*TabularSource* `|`] `search` [`kind=`*CaseSensitivity*`in` `(`] [ *TableSources*`)`] *SearchPredicate*

## <a name="arguments"></a>Arguments

* *TabularSource*: Une expression tabulaire facultative qui agit comme source de données à rechercher, comme un nom de table, un [opérateur syndical,](unionoperator.md)les résultats d’une requête tabulaire, etc. Impossible d’apparaître avec l’expression facultative qui comprend *TableSources*.

* *CaseSensitivity*: Un drapeau optionnel `string` qui contrôle le comportement de tous les opérateurs scalaires en ce qui concerne la sensibilité au cas. Les valeurs valides sont `default` `case_insensitive` les deux synonymes et `has`(qui est le défaut pour `case_sensitive` les opérateurs tels que , à savoir être insensible au cas) et (qui oblige tous ces opérateurs en mode de correspondance sensible aux cas).

* *TableSources*: Une liste facultative séparée par virgule de noms de table « wildcarded » pour participer à la recherche.
  La liste a la même syntaxe que la liste de [l’opérateur syndical](unionoperator.md).
  Impossible d’apparaître avec l’option *TabularSource*.

* *SearchPredicate*: Un prédicat obligatoire qui définit ce qu’il faut rechercher (en d’autres termes, une expression `true`Boolean qui est évaluée pour chaque enregistrement dans l’entrée et que, s’il revient, l’enregistrement est produit.) La syntaxe *pour SearchPredicate* étend et modifie la syntaxe Kusto normale pour les expressions Boolean :

  **Extensions assorties de**cordes : Les littérals de cordes qui apparaissent comme termes `has`dans `hasprefix` `hassuffix`le *SearchPredicate* indiquent un terme match entre toutes les colonnes et l’utilisation littérale, , , et les versions inversées (`!`) ou sensibles aux cas (`sc`) de ces opérateurs. La décision de `has` `hasprefix`s’appliquer, , ou `hassuffix` dépend de si le littéal commence`*`ou se termine (ou les deux) par un astérisque ( ). Les astérisques à l’intérieur de la littéral ne sont pas autorisés.

    |Littéral   |Opérateur   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **Restriction de colonne**: Par défaut, les extensions de correspondance des chaînes tentent de correspondre à toutes les colonnes de l’ensemble de données. Il est possible de limiter cette correspondance à une colonne particulière en utilisant la syntaxe suivante: *ColumnName*`:`*StringLiteral*.

  **Égalité des cordes**: Les correspondances exactes d’une colonne contre une valeur de chaîne (au lieu d’un term-match) peuvent être faites à l’aide de la syntaxe *ColumnName*`==`*StringLiteral*.

  **Autres expressions Boolean**: Toutes les expressions régulières de Kusto Boolean sont soutenues par la syntaxe.
    Par `"error" and x==123` exemple, signifie : rechercher des `error` enregistrements qui ont le terme `123` dans `x` l’une de leurs colonnes, et qui ont la valeur dans la colonne.

  **Match Regex**: L’appariement d’expression régulière est indiqué à l’aide de la syntaxe *StringLiteral* *de colonne,* `matches regex` où *StringLiteral* est le modèle regex.

Notez que si *TabularSource* et *TableSources* sont omis, la recherche est effectuée sur toutes les tables et vues sans restriction de la base de données dans la portée.

## <a name="summary-of-string-matching-extensions"></a>Résumé des extensions assorties de cordes

  |# |Syntaxe                                 |Signification (équivalent `where`)           |Commentaires|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab\w*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |Toutes les comparaisons de chaînes sont sensibles aux cas|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>Notes

**Contrairement à** l’opérateur [de recherche,](findoperator.md)l’opérateur `search` ne prend pas en charge ce qui suit :

1. `withsource=`: La sortie comprendra toujours `$table` une `string` colonne appelée de type dont la valeur est le nom de table à partir duquel chaque enregistrement a été récupéré (ou un nom généré par le système si la source n’est pas une table mais une expression composite).
2. `project=`, `project-smart`: Le schéma de `project-smart` sortie est équivalent au schéma de sortie.

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
  | 1| Préférez utiliser un `search` seul opérateur `search` sur plusieurs opérateurs consécutifs|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| Préférez filtrer `search` à l’intérieur de l’opérateur                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||

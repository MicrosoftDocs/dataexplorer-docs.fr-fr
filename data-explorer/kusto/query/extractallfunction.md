---
title: extract_all ()-Azure Explorateur de données
description: Cet article décrit extract_all () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e52f90b911331bca6374318869d3f8ebf262d81f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348069"
---
# <a name="extract_all"></a>extract_all()

Obtenir toutes les correspondances pour une [expression régulière](./re2.md) à partir d’une chaîne de texte.
Si vous le souhaitez, récupérez un sous-ensemble de groupes correspondants.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

## <a name="syntax"></a>Syntaxe

`extract_all(`*expression régulière* `,` [*captureGroups* `,` ] *texte*`)`

## <a name="arguments"></a>Arguments

|Argument        |Description                                  |Obligatoire ou facultatif  |
|----------------|---------------------------------------------|----------------------|
|regex           | [Expression régulière](./re2.md). L’expression doit avoir au moins un groupe de capture, et inférieure ou égale à 16 groupes de capture                                                         |Obligatoire              |
|captureGroups   |Constante de tableau dynamique qui indique le groupe de capture à extraire. Les valeurs valides sont comprises entre 1 et le nombre de groupes de capture dans l’expression régulière. Les groupes de capture nommés sont également autorisés (voir les [exemples](#examples))|Facultatif         |
|text            |`string`À rechercher                         |Obligatoire              |

## <a name="returns"></a>Retourne

* Si *Regex* trouve une correspondance dans le *texte*: retourne le tableau dynamique, y compris toutes les correspondances par rapport aux groupes de capture *captureGroups*spécifiés, ou tous les groupes de capture dans l' *expression régulière*.
* Si le nombre de *captureGroups* est 1 : le tableau retourné a une seule dimension de valeurs correspondantes.
* Si le nombre de *captureGroups* est supérieur à 1 : le tableau retourné est une collection à deux dimensions de correspondances à valeurs multiples par sélection *captureGroups* , ou tous les groupes de capture présents dans l' *expression régulière* si *captureGroups* est omis.
* Si aucune correspondance n’est trouvée : `null` .

## <a name="examples"></a>Exemples

### <a name="extract-a-single-capture-group"></a>Extraire un groupe de capture unique

Retourne la représentation en octets hexadécimaux (deux chiffres hexadécimaux) du GUID.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[« 82 », « B8 », « to », « 2D », « DF », « a7 », « 4b », « D1 », « 8F », « 63 », « 24 », « ad », « 26 », « D3 », « 14 », « 49 »]|

### <a name="extract-several-capture-groups"></a>Extraire plusieurs groupes de capture 

Utilise une expression régulière avec trois groupes de capture pour fractionner chaque composant GUID en première lettre, dernière lettre et tout ce qui se trouve au milieu.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id)
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "FA", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]|

### <a name="extract-a-subset-of-capture-groups"></a>Extraire un sous-ensemble de groupes de capture

Montre comment sélectionner un sous-ensemble de groupes de capture. L’expression régulière correspond à la première lettre, à la dernière lettre et à l’ensemble du reste. Le paramètre *captureGroups* est utilisé pour sélectionner uniquement les première et dernière parties.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "d"], ["d", "7"], ["4", "1"], ["8", "3"], ["2", "9"]|

### <a name="using-named-capture-groups"></a>Utilisation des groupes de capture nommés

Vous pouvez utiliser des groupes de capture nommés de RE2 dans extract_all ().
Le *captureGroups* utilise à la fois des index de groupe de capture et une référence de groupe de capture nommée pour récupérer les valeurs correspondantes.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "FA", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]|

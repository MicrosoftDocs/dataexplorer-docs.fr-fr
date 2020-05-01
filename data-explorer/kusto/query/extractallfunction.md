---
title: extract_all ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit extract_all () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 94bafd3890f57a1379440c5cced3fa349f9055c5
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616420"
---
# <a name="extract_all"></a>extract_all()

Obtenir toutes les correspondances pour une [expression régulière](./re2.md) à partir d’une chaîne de texte.

Si vous le souhaitez, vous pouvez récupérer un sous-ensemble de groupes correspondants.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

**Syntaxe**

`extract_all(`*Regex* `,` [*captureGroups*`,`] *texte*`)`

**Arguments**

* *Regex*: [expression régulière](./re2.md). L’expression régulière doit avoir au moins un groupe de capture, et inférieure ou égale à 16 groupes de capture.
* *captureGroups*: (facultatif). Constante de tableau dynamique indiquant le groupe de capture à extraire. Les valeurs valides sont comprises entre 1 et nombre de groupes de capture dans l’expression régulière. Les groupes de capture nommés sont également autorisés (voir la section exemples).
* *Text*: `string` à rechercher.

**Retourne**

Si *Regex* trouve une correspondance dans le *texte*: retourne le tableau dynamique, y compris toutes les correspondances avec les groupes de capture *captureGroups* (ou tous les groupes de capture dans l' *expression régulière*) indiqués.
Si le nombre de *captureGroups* est 1 : le tableau retourné a une seule dimension de valeurs correspondantes.
Si le nombre de *captureGroups* est supérieur à 1 : le tableau retourné est une collection à deux dimensions de correspondances à valeurs multiples par sélection *captureGroups* (ou tous les groupes de capture présents dans l' *expression régulière* si *captureGroups* est omis) 

Si aucune correspondance n’est trouvée `null`:. 

**Exemples**

### <a name="extracting-single-capture-group"></a>Extraction d’un groupe de capture unique
L’exemple ci-dessous retourne la représentation hexadécimale en octets (deux chiffres hexadécimaux) du GUID.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[« 82 », « B8 », « to », « 2D », « DF », « a7 », « 4b », « D1 », « 8F », « 63 », « 24 », « ad », « 26 », « D3 », « 14 », « 49 »]|

### <a name="extracting-several-capture-groups"></a>Extraction de plusieurs groupes de capture 
L’exemple suivant utilise une expression régulière avec 3 groupes de capture pour fractionner chaque GUID dans la première lettre, la dernière lettre et tout ce qui se trouve au milieu.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "FA", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]|

### <a name="extracting-subset-of-capture-groups"></a>Extraction d’un sous-ensemble de groupes de capture

L’exemple suivant montre comment sélectionner un sous-ensemble de groupes de capture : dans ce cas, l’expression régulière correspond à la première lettre, la dernière lettre et la totalité du REST, tandis que le paramètre *captureGroups* est utilisé pour sélectionner uniquement la première et la dernière partie. 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "d"], ["d", "7"], ["4", "1"], ["8", "3"], ["2", "9"]|


### <a name="using-named-capture-groups"></a>Utilisation des groupes de capture nommés

Vous pouvez utiliser des groupes de capture nommés de RE2 dans extract_all (). Dans l’exemple ci-dessous, *captureGroups* utilise à la fois des index de groupe de capture et une référence de groupe de capture nommée pour récupérer les valeurs correspondantes.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "FA", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]|

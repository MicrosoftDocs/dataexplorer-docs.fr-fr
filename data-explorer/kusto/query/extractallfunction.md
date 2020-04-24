---
title: extract_all() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit extract_all() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7623a064e272f8b96ca25cc6af47318e26dfb93
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515417"
---
# <a name="extract_all"></a>extract_all()

Obtenez tous les matchs pour une [expression régulière](./re2.md) à partir d’une chaîne de texte.

En option, un sous-ensemble de groupes correspondants peut être récupéré.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") == dynamic(["123", "567", "789"])
```

**Syntaxe**

`extract_all(`*regex* `,` *[captureGroups*`,`] *texte*`)`

**Arguments**

* *regex*: Une [expression régulière](./re2.md). L’expression régulière doit avoir au moins un groupe de capture, et moins ou égal à 16 groupes de capture.
* *captureGroups*: (facultatif). Une constante de tableau dynamique indiquant le groupe de capture à extraire. Les valeurs valides sont de 1 à un certain nombre de groupes de capture dans l’expression régulière. Les groupes de capture nommés sont également autorisés (voir la section des exemples).
* *texte*: `string` A à rechercher.

**Retourne**

Si *regex* trouve un match dans le *texte*: retourne tableau dynamique y compris tous les matches contre les groupes de capture *indiqués captureGroups* (ou tous des groupes de capture dans le *regex*).
Si le nombre de *captureGroups* est de 1 : le tableau retourné a une dimension unique de valeurs assorties.
Si le nombre de *captureGroups* est supérieur à 1 : le tableau retourné est une collection bidimensionnelle de *correspondances* multi-valeurs par sélection de groupes de capture (ou tous les groupes de capture présents dans le *regex* si *captureGroups* est omis) 

S’il n’y `null`a pas de correspondance: . 

**Exemples**

### <a name="extracting-single-capture-group"></a>Extraction d’un seul groupe de capture
L’exemple ci-dessous renvoie la représentation hex-byte (deux hex-chiffres) du GUID.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82"""""b8","be","2d","df","a7","4b","d1","8f","63","24","ad","26","d3","14","49"]|

### <a name="extracting-several-capture-groups"></a>Extraction de plusieurs groupes de capture 
L’exemple suivant utilise une expression régulière avec 3 groupes de capture pour diviser chaque partie GUID en première lettre, dernière lettre et tout ce qui est au milieu.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8","2b8be2","d"],[d"""""","7"],["4","bd","1"],["8","f6","3"],["2","4ad26d3144","9"]]|

### <a name="extracting-subset-of-capture-groups"></a>Extraction d’un sous-ensemble de groupes de capture

Exemple suivant montre comment sélectionner un sous-ensemble de groupes de capture: dans ce cas, l’expression régulière correspond à la première lettre, la dernière lettre et tout le reste - tandis que le paramètre *captureGroups* est utilisé pour sélectionner seulement la première et la dernière partie. 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8""'''''],[d"'7"],["4","1"],["8","3"],["2","9"]]|


### <a name="using-named-capture-groups"></a>Utilisation de groupes de capture nommés

Vous pouvez utiliser des groupes de capture nommés de RE2 dans extract_all(). Dans l’exemple ci-dessous - les groupes de capture utilise à la fois des indices de groupe de capture et une référence de groupe de capture nommée pour obtenir des valeurs *correspondantes.*

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8","2b8be2","d"],[d"""""","7"],["4","bd","1"],["8","f6","3"],["2","4ad26d3144","9"]]|
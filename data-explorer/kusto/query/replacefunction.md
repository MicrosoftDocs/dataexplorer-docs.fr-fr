---
title: Replace ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Replace () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d9fdcfaad41201cd99796c3f4966593aa7480e49
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243051"
---
# <a name="replace"></a>replace()

Remplace toutes les correspondances d’expression régulière par une autre chaîne.

## <a name="syntax"></a>Syntaxe

`replace(`*expression régulière* `,` *réécrire* `,` *texte*`)`

## <a name="arguments"></a>Arguments

* *Regex*: [expression régulière](https://github.com/google/re2/wiki/Syntax) à rechercher dans le *texte*. Elle peut contenir des groupes de capture entre '('parenthèses')'. 
* *réécriture*: Regex de remplacement pour toute correspondance faite par *matchingRegex*. Utilisez `\0` pour faire référence à la correspondance complète, `\1` pour le premier groupe de capture, `\2` et ainsi de suite pour les groupes de capture suivants.
* *Text*: chaîne.

## <a name="returns"></a>Retours

*text* après le remplacement de toutes les correspondances de *regex* par les évaluations de *rewrite*. Les correspondances ne se chevauchent pas.

## <a name="example"></a>Exemple

L’instruction suivante :

```kusto
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Donne les résultats suivants :

| x    | str | replaced|
|---|---|---|
| 1    | Le nombre est 1.000000  | Le nombre était : 1.000000|
| 2    | Le nombre est 2.000000  | Le nombre était : 2.000000|
| 3    | Le nombre est 3.000000  | Le nombre était : 3.000000|
| 4    | Le nombre est 4.000000  | Le nombre était : 4.000000|
| 5    | Le nombre est 5.000000  | Le nombre était : 5.000000|
 
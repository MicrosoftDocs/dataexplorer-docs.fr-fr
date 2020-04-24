---
title: remplacer() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit remplacer () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 84a741f10172ef418da7d92b8c1ad6ba26593d72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510334"
---
# <a name="replace"></a>replace()

Remplace toutes les correspondances d’expression régulière par une autre chaîne.

**Syntaxe**

`replace(`*réécrire* *le* `,` *texte* `,``)`

**Arguments**

* *regex*: [L’expression régulière](https://github.com/google/re2/wiki/Syntax) de la recherche de *texte*. Elle peut contenir des groupes de capture entre '('parenthèses')'. 
* *réécrire*: Le regex de remplacement pour n’importe quel match fait par *matchingRegex*. Utilisez `\0` pour faire référence à la correspondance complète, `\1` pour le premier groupe de capture, `\2` et ainsi de suite pour les groupes de capture suivants.
* *texte*: Une chaîne.

**Retourne**

*text* après le remplacement de toutes les correspondances de *regex* par les évaluations de *rewrite*. Les correspondances ne se chevauchent pas.

**Exemple**

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
 
---
title: strcmp() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit strcmp() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62ebcbfa71ebf8a29f3a8a1559feb91f96e0a2bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506900"
---
# <a name="strcmp"></a>strcmp()

Compare deux chaînes.

La fonction commence à comparer le premier caractère de chaque chaîne. S’ils sont égaux les uns aux autres, il continue avec les paires suivantes jusqu’à ce que les caractères diffèrent ou jusqu’à ce que la fin de la chaîne plus courte est atteinte.

**Syntaxe**

`strcmp(`*string1* `,` *string2*`)` 

**Arguments**

* *string1*: première chaîne d’entrée à comparer. 
* *string2*: deuxième chaîne d’entrée à comparer.

**Retourne**

Retourne une valeur intégrale indiquant la relation entre les cordes :
* *<0* - le premier personnage qui ne correspond pas a une valeur inférieure dans la chaîne1 que dans la chaîne2
* *0* - le contenu des deux cordes est égal
* *>0* - le premier personnage qui ne correspond pas a une plus grande valeur dans la chaîne1 que dans la chaîne2

**Exemples**

```
datatable(string1:string, string2:string)
["ABC","ABC",
"abc","ABC",
"ABC","abc",
"abcde","abc"]
| extend result = strcmp(string1,string2)
```

|string1|string2|result|
|---|---|---|
|ABC|ABC|0|
|abc|ABC|1|
|ABC|abc|-1|
|Abcde|abc|1|
---
title: strcmp ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit strcmp () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5c1e4cb9a4ad77d0d48f52ca7e47fc6cea06cff4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243735"
---
# <a name="strcmp"></a>strcmp()

Compare deux chaînes.

La fonction commence à comparer le premier caractère de chaque chaîne. S’ils sont égaux, il continue avec les paires suivantes jusqu’à ce que les caractères soient différents ou jusqu’à ce que la fin de la chaîne la plus petite soit atteinte.

## <a name="syntax"></a>Syntaxe

`strcmp(`*Chaîne1* `,` *Chaîne2*`)` 

## <a name="arguments"></a>Arguments

* *Chaîne1*: première chaîne d’entrée pour la comparaison. 
* *Chaîne2*: deuxième chaîne d’entrée pour la comparaison.

## <a name="returns"></a>Retours

Retourne une valeur intégrale indiquant la relation entre les chaînes :
* *<0* -le premier caractère qui ne correspond pas a une valeur inférieure dans chaîne1 que dans chaîne2
* *0* -le contenu des deux chaînes est égal
* *>0* -le premier caractère qui ne correspond pas a une valeur supérieure dans chaîne1 que dans chaîne2

## <a name="examples"></a>Exemples

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
|ABCDE|abc|1|
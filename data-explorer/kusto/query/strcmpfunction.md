---
title: strcmp ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit strcmp () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 11951195d95d956f70d4bfce32d22a9f9c73dc3d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350925"
---
# <a name="strcmp"></a>strcmp()

Compare deux chaînes.

La fonction commence à comparer le premier caractère de chaque chaîne. S’ils sont égaux, il continue avec les paires suivantes jusqu’à ce que les caractères soient différents ou jusqu’à ce que la fin de la chaîne la plus petite soit atteinte.

## <a name="syntax"></a>Syntaxe

`strcmp(`*Chaîne1* `,` *Chaîne2*`)` 

## <a name="arguments"></a>Arguments

* *Chaîne1*: première chaîne d’entrée pour la comparaison. 
* *Chaîne2*: deuxième chaîne d’entrée pour la comparaison.

## <a name="returns"></a>Retourne

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
---
title: Split ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Split () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ce8af3f9d56e4b5c3d388010b2760906a8e3dc4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242423"
---
# <a name="split"></a>split()

Fractionne une chaîne donnée en fonction d’un délimiteur donné et retourne un tableau de chaînes avec les sous-chaînes contenues.

Éventuellement, une sous-chaîne spécifique peut être retournée si elle existe.

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Syntaxe

`split(`*source* `,` *délimiteur* [ `,` *requestedIndex*]`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source qui sera fractionnée en fonction du délimiteur donné.
* *delimiter*: délimiteur utilisé pour fractionner la chaîne source.
* *requestedIndex* : index de base zéro facultatif `int`. S’il est fourni, le tableau de chaînes retourné contient la sous-chaîne demandée si elle existe. 

## <a name="returns"></a>Retours

Un tableau de chaînes qui contient les sous-chaînes de la chaîne source donnée séparées par le délimiteur donné.

## <a name="examples"></a>Exemples

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```
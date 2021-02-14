---
title: split() - Azure Data Explorer | Microsoft Docs
description: Cet article décrit split() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: ed58a1d249a74c02445968e81930e303aeff0d56
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360062"
---
# <a name="split"></a>split()

Fractionne une chaîne donnée en fonction d’un délimiteur donné et retourne un tableau de chaînes avec les sous-chaînes contenues.

Éventuellement, une sous-chaîne spécifique peut être retournée si elle existe.

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Syntaxe

`split(`*source*`,` *delimiter* [`,` *requestedIndex*]`)`

## <a name="arguments"></a>Arguments

* *source* : chaîne source à fractionner en fonction du délimiteur donné.
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
---
title: Split ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Split () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: efd3812086631451b77ca1edd846ec9bd75990fe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351010"
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

## <a name="returns"></a>Retourne

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
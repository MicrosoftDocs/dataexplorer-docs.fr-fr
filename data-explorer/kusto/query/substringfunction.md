---
title: substring() - Azure Data Explorer | Microsoft Docs
description: Cet article décrit substring() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512041"
---
# <a name="substring"></a>substring()

Extrait une sous-chaîne d’une chaîne source, en commençant à un certain index, jusqu’à la fin de la chaîne.

Éventuellement, la longueur de la sous-chaîne demandée peut être spécifiée.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>Syntaxe

`substring(`*source*`,` *startingIndex* [`,` *length*]`)`

## <a name="arguments"></a>Arguments

* *source* : chaîne source dont la sous-chaîne est extraite.
* *startingIndex* : position du caractère de départ (base zéro) de la sous-chaîne demandée.
* *length* : paramètre facultatif qui permet de spécifier le nombre de caractères demandés dans la sous-chaîne. 

**Remarques**

*startingIndex* peut être un nombre négatif, auquel cas la sous-chaîne est récupérée à partir de la fin de la chaîne source.

## <a name="returns"></a>Retours

Une sous-chaîne de la chaîne donnée. La sous-chaîne commence à la position de caractère startingIndex (de base zéro) et continue jusqu’à la fin de la chaîne ou des caractères de longueur si elle est spécifiée.

## <a name="examples"></a>Exemples

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```
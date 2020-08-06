---
title: todynamic (), ToObject ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit todynamic (), ToObject () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: acd1b5328150e61bc81930f94b8ea9e8025e1ebb
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804081"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

Interprète un `string` comme une [valeur JSON](https://json.org/) et retourne la valeur sous la forme [`dynamic`](./scalar-data-types/dynamic.md) . 

Il est préférable d’utiliser la [fonction extractjson ()](./extractjsonfunction.md) lorsque vous devez extraire plus d’un élément d’un objet composé JSON.

Alias de la fonction [parse_json ()](./parsejsonfunction.md) .

> [!NOTE]
> Préférez l’utilisation [de Dynamic ()](./scalar-data-types/dynamic.md) dans la mesure du possible.

## <a name="syntax"></a>Syntaxe

`todynamic(`*json* `)` 
 JSON `toobject(` *JSON*`)`

## <a name="arguments"></a>Arguments

* *JSON*: un document JSON.

## <a name="returns"></a>Retours

Un objet de type `dynamic` spécifié par *json*.

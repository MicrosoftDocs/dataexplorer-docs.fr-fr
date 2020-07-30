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
ms.openlocfilehash: e11a4d450275fb4d596bd9618c20ef6cefcb0531
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350727"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

Interprète un `string` comme une [valeur JSON](https://json.org/) et retourne la valeur sous la forme [`dynamic`](./scalar-data-types/dynamic.md) . 

Il est préférable d’utiliser la [fonction extractjson ()](./extractjsonfunction.md) lorsque vous devez extraire plus d’un élément d’un objet composé JSON.

Alias de la fonction [parse_json ()](./parsejsonfunction.md) .

## <a name="syntax"></a>Syntaxe

`todynamic(`*json* `)` 
 JSON `toobject(` *JSON*`)`

## <a name="arguments"></a>Arguments

* *JSON*: un document JSON.

## <a name="returns"></a>Retourne

Un objet de type `dynamic` spécifié par *json*.

*Remarque*: préférez l’utilisation [de Dynamic ()](./scalar-data-types/dynamic.md) dans la mesure du possible.
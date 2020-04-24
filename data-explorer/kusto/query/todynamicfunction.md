---
title: todynamic(), toobject() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit todynamic(), toobject() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 138b0f978df699817c5dc5c14bafc4c06a95afc7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506152"
---
# <a name="todynamic-toobject"></a>todynamique (), toobject ()

Interprète une `string` valeur [JSON](https://json.org/) et retourne [`dynamic`](./scalar-data-types/dynamic.md)la valeur comme . 

Il est supérieur à l’utilisation [de l’extractjson () fonction](./extractjsonfunction.md) lorsque vous avez besoin d’extraire plus d’un élément d’un objet composé JSON.

Aliases à [parse_json()](./parsejsonfunction.md) fonction.

**Syntaxe**

`todynamic(`*json*`)`
`toobject(`*json json json*`)`

**Arguments**

* *json*: Un document JSON.

**Retourne**

Un objet de type `dynamic` spécifié par *json*.

*Remarque*: Préférez utiliser [la dynamique ()](./scalar-data-types/dynamic.md) lorsque c’est possible.
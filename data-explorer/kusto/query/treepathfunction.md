---
title: TreePath ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit TreePath () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 91e11e6d45f82e6e27ea2da5ca1da945f190b69f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252004"
---
# <a name="treepath"></a>treepath()

Énumère toutes les expressions de chemin qui identifient les feuilles dans un objet dynamique.

`treepath(`*objet dynamique*`)`

## <a name="returns"></a>Retours

Un tableau d’expressions de chemin.

## <a name="examples"></a>Exemples

|Expression|est évalué à|
|---|---|
|`treepath(parse_json('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parse_json('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parse_json('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|
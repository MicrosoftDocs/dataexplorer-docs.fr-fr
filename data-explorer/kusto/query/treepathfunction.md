---
title: TreePath ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit TreePath () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e52caa6da7d5746119e363d1676b39fcd9da0340
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350653"
---
# <a name="treepath"></a>treepath()

Énumère toutes les expressions de chemin qui identifient les feuilles dans un objet dynamique.

`treepath(`*objet dynamique*`)`

## <a name="returns"></a>Retourne

Un tableau d’expressions de chemin.

## <a name="examples"></a>Exemples

|Expression|est évalué à|
|---|---|
|`treepath(parse_json('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parse_json('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parse_json('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|
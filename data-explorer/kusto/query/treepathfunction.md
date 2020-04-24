---
title: treepath() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le chemin des arbres () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 27a0edc61a1d2317427454aaf74531ec395d067d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505693"
---
# <a name="treepath"></a>treepath()

Énumère toutes les expressions de chemin qui identifient les feuilles dans un objet dynamique.

`treepath(`*objet dynamique*`)`

**Retourne**

Un tableau d’expressions de chemin.

**Exemples**

|Expression|est évalué à|
|---|---|
|`treepath(parse_json('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parse_json('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parse_json('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|
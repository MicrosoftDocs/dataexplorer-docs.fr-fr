---
title: format_bytes ()-Azure Explorateur de données
description: Cet article décrit format_bytes () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f19883335149603fe7fc7429dabb62f565a0b402
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347984"
---
# <a name="format_bytes"></a>format_bytes()

Met en forme un nombre en tant que chaîne représentant la taille des données en octets.

```kusto
format_bytes(1024) == '1 KB'"
```

## <a name="syntax"></a>Syntaxe

`format_bytes(`*valeur* [ `,` *précision* [ `,` *unités*]]`)`

## <a name="arguments"></a>Arguments

* `value`: nombre à mettre en forme en tant que taille des données en octets.
* `precision`: (facultatif) nombre de chiffres auxquels la valeur sera arrondie. (la valeur par défaut est 0).
* `units`: (facultatif) unités de la taille des données cible que la mise en forme de la chaîne utilisera ( `Bytes` , `KB` , `MB` , `GB` , `TB` , `PB` ). Si le paramètre est vide, les unités sont sélectionnées automatiquement en fonction de la valeur d’entrée.

## <a name="returns"></a>Retours

Chaîne avec le résultat de format.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|v2|v3|v4|5|
|---|---|---|---|---|
|564 octets|10,1 KO|19 MO|19,08 MO|19541 KO|

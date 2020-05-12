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
ms.openlocfilehash: 4da07433be5a052d71740931d4dedd9df0399f56
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227318"
---
# <a name="format_bytes"></a>format_bytes()

Met en forme un nombre en tant que chaîne représentant la taille des données en octets.

```kusto
format_bytes(1024) == '1 KB'"
```

**Syntaxe**

`format_bytes(`*valeur* [ `,` *précision* [ `,` *unités*]]`)`

**Arguments**

* `value`: nombre à mettre en forme en tant que taille des données en octets.
* `precision`: (facultatif) nombre de chiffres auxquels la valeur sera arrondie. (la valeur par défaut est 0).
* `units`: (facultatif) unités de la taille des données cible que la mise en forme de la chaîne utilisera ( `Bytes` , `KB` , `MB` , `GB` , `TB` , `PB` ). Si le paramètre est vide, les unités sont sélectionnées automatiquement en fonction de la valeur d’entrée.

**Retourne**

Chaîne avec le résultat de format.

**Exemples**

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

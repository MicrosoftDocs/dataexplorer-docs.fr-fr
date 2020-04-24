---
title: format_bytes() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit format_bytes() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5d5bfa234e001f498737b9df88274096f8592f52
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515162"
---
# <a name="format_bytes"></a>format_bytes()

Formats un nombre comme une chaîne représentant la taille des données dans les octets.

```kusto
format_bytes(1024) == '1 KB'"
```

**Syntaxe**

`format_bytes(`*valeur* `,` *[précision* `,` *[unités*]]`)`

**Arguments**

* `value`: un nombre à formater sous forme de taille de données dans les octets.
* `precision`: (facultatif) Nombre de chiffres à qui la valeur sera arrondie. (la valeur par défaut est de 0).
* `units`: (facultatif) Unités de la taille des`Bytes` `KB`données `MB` `GB`cibles `TB` `PB`que le formatage de chaîne utilisera ( , , , , . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . Si le paramètre est vide - les unités seront auto-sélectionnées en fonction de la valeur d’entrée.

**Retourne**

La chaîne avec le résultat du format.

**Exemples**

```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|v2|v3|v4|v5|
|---|---|---|---|---|
|564 Octets|10,1 KB|19 Mo|19,08 Mo|19541 KB|
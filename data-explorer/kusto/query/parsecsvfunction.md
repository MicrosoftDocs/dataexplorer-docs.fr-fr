---
title: parse_csv ()-Azure Explorateur de données
description: Cet article décrit parse_csv () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b61934ec2efbfb22c17fe93a4f3969a1592cefab
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780658"
---
# <a name="parse_csv"></a>parse_csv()

Fractionne une chaîne donnée représentant un seul enregistrement de valeurs séparées par des virgules et retourne un tableau de chaînes avec ces valeurs.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**Syntaxe**

`parse_csv(`*code*`)`

**Arguments**

* *source*: chaîne source représentant un enregistrement unique de valeurs séparées par des virgules.

**Retourne**

Tableau de chaînes qui contient les valeurs de fractionnement.

**Notes**

Les sauts de ligne incorporés, les virgules et les guillemets peuvent être placés dans une séquence d’échappement à l’aide du guillemet double (' "'). Cette fonction ne prend pas en charge plusieurs enregistrements par ligne (seul le premier enregistrement est pris).

**Exemples**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  « AA »,<br>  « b, b, b »,<br>  « CC »,<br>  « Guillemets d’échappement : \" titre \" »,<br>  "line1\nline2"<br>]|

Charge utile CSV avec plusieurs enregistrements :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  « Record1 »,<br>  « a »,<br>  « b »,<br>  "c"<br>]|

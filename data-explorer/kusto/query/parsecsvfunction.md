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
ms.openlocfilehash: eddf893d3c0d8096f57a3f624ddedf753fe35f91
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346352"
---
# <a name="parse_csv"></a>parse_csv()

Fractionne une chaîne donnée représentant un seul enregistrement de valeurs séparées par des virgules et retourne un tableau de chaînes avec ces valeurs.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Syntaxe

`parse_csv(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source représentant un enregistrement unique de valeurs séparées par des virgules.

## <a name="returns"></a>Retourne

Tableau de chaînes qui contient les valeurs de fractionnement.

**Remarques**

Les sauts de ligne incorporés, les virgules et les guillemets peuvent être placés dans une séquence d’échappement à l’aide du guillemet double (' "'). Cette fonction ne prend pas en charge plusieurs enregistrements par ligne (seul le premier enregistrement est pris).

## <a name="examples"></a>Exemples

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

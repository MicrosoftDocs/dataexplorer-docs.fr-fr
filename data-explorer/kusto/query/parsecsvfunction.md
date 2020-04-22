---
title: parse_csv() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_csv () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6645fd0dcc7062a4afb60fb34ade1c519af27294
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663671"
---
# <a name="parse_csv"></a>parse_csv()

Divise une chaîne donnée représentant un seul enregistrement de valeurs séparées de virgule et renvoie un tableau de cordes avec ces valeurs.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**Syntaxe**

`parse_csv(`*Source*`)`

**Arguments**

* *source*: La chaîne source représentant un seul enregistrement de valeurs séparées de virgule.

**Retourne**

Un tableau de chaîne qui contient les valeurs fractionnées.

**Remarques**

Les flux de ligne embarqués, les virgules et les citations peuvent être échappés à l’aide de la double marque de citation (« » ). Cette fonction ne prend pas en charge plusieurs enregistrements par rangée (seul le premier enregistrement est pris).

**Exemples**

```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  "aa",<br>  "b,b,b",<br>  "cc",<br>  "Escaping \"quotes:\"Title ",<br>  "ligne1-nline2"<br>]|

Charge utile CSV avec plusieurs enregistrements:

```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "record1",<br>  "a",<br>  "b",<br>  "c"<br>]|
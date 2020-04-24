---
title: extrait() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’extrait() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 398c79ddcd362f3c09fea18accd082c0eb3b1fc3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515366"
---
# <a name="extract"></a>extract()

Obtient une correspondance pour une [expression régulière](./re2.md) à partir d’une chaîne de texte. 

Optionnellement, convertir le sous-corde extrait au type indiqué.

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

**Syntaxe**

`extract(`*regex* `,` *captureGroup* `,` `,` *texte* [ *typeLiteral*]`)`

**Arguments**

* *regex*: Une [expression régulière](./re2.md).
* *captureGroup*: `int` Une constante positive indiquant le groupe de capture à extraire. Les valeurs sont 0 pour la correspondance entière, 1 pour la valeur mise en correspondance par la première '('parenthèse')' dans l’expression régulière, 2 ou plus pour les parenthèses suivantes.
* *texte*: `string` A à rechercher.
* *typeLiteral*: Un type facultatif littéral (p. ex., `typeof(long)`). Si elle est fournie, la sous-chaîne extraite est convertie dans ce type. 

**Retourne**

Si *regex* trouve une correspondance dans *text* : sous-chaîne correspondant au *captureGroup* indiqué, éventuellement convertie en *typeLiteral*.

Si aucune correspondance n’est trouvée ou si la conversion de type échoue : `null`. 

**Exemples**

Une définition de `Duration` est recherchée dans l’exemple de chaîne `Trace`. La correspondance est convertie en `real`, puis multipliée par une constante de temps (`1s`) pour que `Duration` soit de type `timespan`. Dans cet exemple, elle est égale à 123,45 secondes :

```kusto
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Cet exemple est équivalent à `substring(Text, 2, 4)`:

```kusto
extract("^.{2,2}(.{4,4})", 1, Text)
```
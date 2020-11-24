---
title: Extract ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Extract () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 483c926d60abef120de2a355a6fa040b9608cd7a
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513044"
---
# <a name="extract"></a>extract()

Obtient une correspondance pour une [expression régulière](./re2.md) à partir d’une chaîne de texte. 

Si vous le souhaitez, convertissez la sous-chaîne extraite dans le type indiqué.

```kusto
extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"
```

## <a name="syntax"></a>Syntaxe

`extract(`*expression régulière* `,` *captureGroup* `,` *texte* [ `,` *typeLiteral*]`)`

## <a name="arguments"></a>Arguments

* *Regex*: [expression régulière](./re2.md).
* *captureGroup*: constante positive `int` indiquant le groupe de capture à extraire. Les valeurs sont 0 pour la correspondance entière, 1 pour la valeur mise en correspondance par la première '('parenthèse')' dans l’expression régulière, 2 ou plus pour les parenthèses suivantes.
* *Text*: `string` à rechercher.
* *typeLiteral*: littéral de type facultatif (par exemple, `typeof(long)` ). Si elle est fournie, la sous-chaîne extraite est convertie dans ce type. 

## <a name="returns"></a>Retours

Si *regex* trouve une correspondance dans *text* : sous-chaîne correspondant au *captureGroup* indiqué, éventuellement convertie en *typeLiteral*.

Si aucune correspondance n’est trouvée ou si la conversion de type échoue : `null`. 

## <a name="examples"></a>Exemples

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
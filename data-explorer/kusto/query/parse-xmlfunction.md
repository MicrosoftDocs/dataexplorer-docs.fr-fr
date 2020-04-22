---
title: parse_xml() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_xml() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9743cbcb8d3c25707d97a4a6844f947352e63b0b
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744615"
---
# <a name="parse_xml"></a>parse_xml()

Interprète une `string` valeur XML, convertit la valeur en un JSON et retourne la valeur comme `dynamic`.

**Syntaxe**

`parse_xml(`*xml*`)`

**Arguments**

* *xml*: Une `string`expression de type , représentant une valeur formatée XML.

**Retourne**

Un objet de [type dynamique](./scalar-data-types/dynamic.md) qui est déterminé par la valeur de *xml*, ou nul, si le format XML est invalide.

La conversion du XML en JSON se fait à l’aide de la bibliothèque [xml2json.](https://github.com/Cheedoong/xml2json)

La conversion se fait comme suit :

XML                                |JSON                                            |Accès
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | "e": nul                                  | o.e
`<e>text</e>`                      | "e": "texte"                                | o.e
`<e name="value" />`               | "e":@name"valeur"                     | o.e["@name"]
`<e name="value">text</e>`         | "e": ""@name"valeur", "#text": "texte" | o.e["@name"] o.e["#text"]
`<e> <a>text</a> <b>text</b> </e>` | "e": "a": "texte", "b": "texte"          | o.e.a o.e.b
`<e> <a>text</a> <a>text</a> </e>` | "e": "a": ["texte", "texte"]             | o.e.a[0] o.e.a[1]
`<e> text <a>text</a> </e>`        | "e": "#text": "texte", "a": "texte"      | 1'o.e[#text"] o.e.a

**Remarques**

* La `string` longueur `parse_xml` maximale des entrées est de 128 KB. L’interprétation des cordes plus longues entraînera un objet nul 
* Seuls les nœuds d’élément, les attributs et les nœuds texte seront traduits. Tout le reste sera ignoré
 
**Exemple**

Dans l’exemple suivant, quand `context_custom_metrics` est un élément `string`, le résultat ressemble à ceci : 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<duration>
    <value>118.0</value>
    <count>5.0</count>
    <min>100.0</min>
    <max>150.0</max>
    <stdDev>0.0</stdDev>
    <sampledValue>118.0</sampledValue>
    <sum>118.0</sum>
</duration>
```

puis le CSL Fragment suivant traduit le XML au JSON suivant :

```json
{
    "duration": {
        "value": 118.0,
        "count": 5.0,
        "min": 100.0,
        "max": 150.0,
        "stdDev": 0.0,
        "sampledValue": 118.0,
        "sum": 118.0
    }
}
```

et récupère la valeur `duration` de la fente dans l’objet, et `duration.value` `duration.min` à`118.0` `110.0`partir de cela il récupère deux fentes, et ( et , respectivement).

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```
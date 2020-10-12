---
title: parse_xml ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit parse_xml () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f003c5e9c6733391d61a2130528c9babc4aae67
ms.sourcegitcommit: d157e661de293aa4c2b5ad334a554eda0295bd2c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91886296"
---
# <a name="parse_xml"></a>parse_xml()

Interprète une `string` en tant que valeur XML, convertit la valeur en un JSON et retourne la valeur sous la forme `dynamic` .

## <a name="syntax"></a>Syntaxe

`parse_xml(`*xml*`)`

## <a name="arguments"></a>Arguments

* *XML*: expression de type `string` , représentant une valeur au format XML.

## <a name="returns"></a>Retours

Objet de type [Dynamic](./scalar-data-types/dynamic.md) qui est déterminé par la valeur de *XML*, ou null, si le format XML n’est pas valide.

La conversion s’effectue comme suit :

XML                                |JSON                                            |Accès
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | {"e" : null}                                  | o. e
`<e>text</e>`                      | {"e" : "texte"}                                | o. e
`<e name="value" />`               | {"e" : {" @name " : "valeur"}}                     | o. e [" @name "]
`<e name="value">text</e>`         | {"e" : {" @name " : "valeur", "#text" : "texte"}} | o. e [" @name "] o. e ["#text"]
`<e> <a>text</a> <b>text</b> </e>` | {"e" : {"a" : "texte", "b" : "texte"}}          | o. e. a o. e. b
`<e> <a>text</a> <a>text</a> </e>` | {"e" : {"a" : ["texte", "texte"]}}             | o. e. a [0] o. e. a [1]
`<e> text <a>text</a> </e>`        | {"e" : {"#text" : "texte", "a" : "texte"}}      | 1 'o. e ["#text"] o. e. a

**Remarques**

* La longueur d’entrée maximale `string` pour `parse_xml` est 1 mo (1 048 576 octets). L’interprétation des chaînes plus longue entraînera un objet null
* Seuls les nœuds d’élément, les attributs et les nœuds de texte seront traduits. Tout le reste sera ignoré
 
## <a name="example"></a>Exemple

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

Ensuite, le fragment CSL suivant traduit le code XML au format JSON suivant :

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

et récupèrent la valeur de l' `duration` emplacement dans l’objet et, à partir de ce qu’il récupère deux emplacements, `duration.value` et `duration.min` ( `118.0` `100.0` respectivement).

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

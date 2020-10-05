---
title: bag_remove_keys ()-Azure Explorateur de données
description: Cet article décrit bag_remove_keys () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/04/2020
ms.openlocfilehash: fffbda419ac123af8e2744b76c7acbe560c07ce9
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712341"
---
# <a name="bag_remove_keys"></a>bag_remove_keys ()

Supprime les clés et les valeurs associées d’un `dynamic` conteneur de propriétés.

## <a name="syntax"></a>Syntaxe

`bag_remove_keys(`*conteneur* `, ` *clés*`)`

## <a name="arguments"></a>Arguments

* *Bag*: `dynamic` entrée de sac de propriétés.
* *Keys*: `dynamic` le tableau comprend les clés à supprimer de l’entrée. Les clés font référence au premier niveau du conteneur des propriétés.
La spécification de clés sur les niveaux imbriqués n’est pas prise en charge.

## <a name="returns"></a>Retours

Retourne un `dynamic` conteneur de propriétés sans clés spécifiées et leurs valeurs.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(input:dynamic)
[
    dynamic({'key1' : 123,     'key2': 'abc'}),
    dynamic({'key1' : 'value', 'key3': 42.0}),
]
| extend result=bag_remove_keys(input, dynamic(['key2', 'key4']))
```

|entrée|result|
|---|---|
|{<br>  « Key1 » : 123,<br>  « key2 » : « ABC »<br>}|{<br>  « Key1 » : 123<br>}|
|{<br>  « Key1 » : « value »,<br>  « Key3 » : 42,0<br>}|{<br>  « Key1 » : « value »,<br>  « Key3 » : 42,0<br>}|

---
title: estimate_data_size() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit estimate_data_size() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9664a7c9caf0506cdb7274eed5e143f89c82cebb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515723"
---
# <a name="estimate_data_size"></a>estimate_data_size()

Retourne une taille de données estimée dans les octets des colonnes sélectionnées de l’expression tabulaire.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**Syntaxe**

`estimate_data_size(*)`

`estimate_data_size(`*col1*`, `*col2*`, `...`)`

**Arguments**

* *col1*, *col2*: Sélection de références de colonnes dans l’expression tabulaire source qui sont utilisées pour l’estimation de la taille des données. Pour inclure toutes `*` les colonnes, utilisez la syntaxe (astérisque).

**Retourne**

* La taille estimée des données dans les octets de la taille record. L’estimation est basée sur les types de données et les longueurs des valeurs.

**Exemples**

Calcul de la `estimated_data_size()`taille totale des données à l’aide de :

```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|Total|
|---|
|180|
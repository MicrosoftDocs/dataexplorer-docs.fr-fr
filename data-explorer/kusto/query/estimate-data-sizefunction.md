---
title: estimate_data_size ()-Azure Explorateur de données
description: Cet article décrit estimate_data_size () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f0901bddbfa8854e902ab60197164cf830215948
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224950"
---
# <a name="estimate_data_size"></a>estimate_data_size()

Retourne une estimation de la taille des données, en octets, des colonnes sélectionnées de l’expression tabulaire.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**Syntaxe**

`estimate_data_size(*)`

`estimate_data_size(`*col1* `, ` *col2* `, ` ...`)`

**Arguments**

* *col1*, *col2*: sélection de références de colonnes dans l’expression tabulaire source utilisée pour l’estimation de la taille des données. Pour inclure toutes les colonnes, utilisez la `*` syntaxe (astérisque).

**Retourne**

* Taille estimée des données, en octets, de la taille de l’enregistrement. L’estimation est basée sur des types de données et des longueurs de valeurs.

**Exemples**

Calcul de la taille totale des données à l’aide de `estimated_data_size()` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|Total|
|---|
|180|

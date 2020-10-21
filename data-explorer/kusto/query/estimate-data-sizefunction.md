---
title: estimate_data_size ()-Azure Explorateur de données
description: Cet article décrit estimate_data_size () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09d722b07b3ff49a294d4d8ce19a89563fc8f66e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253016"
---
# <a name="estimate_data_size"></a>estimate_data_size()

Retourne une estimation de la taille des données, en octets, des colonnes sélectionnées de l’expression tabulaire.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

## <a name="syntax"></a>Syntaxe

`estimate_data_size(*)`

`estimate_data_size(`*col1* `, ` *col2* `, ` ...`)`

## <a name="arguments"></a>Arguments

* *col1*, *col2*: sélection de références de colonnes dans l’expression tabulaire source utilisée pour l’estimation de la taille des données. Pour inclure toutes les colonnes, utilisez la `*` syntaxe (astérisque).

## <a name="returns"></a>Retours

* Taille estimée des données, en octets, de la taille de l’enregistrement. L’estimation est basée sur des types de données et des longueurs de valeurs.

## <a name="examples"></a>Exemples

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

---
title: opérateur de fourchette - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de fourche dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 13cd837b3874a55ec758991f5609e089daba7c75
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515196"
---
# <a name="fork-operator"></a>fork, opérateur

Exécute plusieurs opérateurs de consommation en parallèle.

**Syntaxe**

*T* `|` `=``(``=``(``)` `)` *name**name**subquery* *subquery* [ nom ] sous-payse [ nom ] sous-pays ... `fork`

**Arguments**

* *sous-laquery* est un pipeline en aval des opérateurs de requête
* *nom* est un nom temporaire pour le tableau des résultats de sous-pays

**Retourne**

Tableaux de résultats multiples, un pour chacune des sous-ques.

**Opérateurs pris en charge**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**Remarques**

* [`materialize`](materializefunction.md)fonction peut être utilisée comme [`join`](joinoperator.md) [`union`](unionoperator.md) un remplacement pour l’utilisation ou sur les pattes de fourchette.
Le flux d’entrée sera mis en cache par matérialisation, puis l’expression mise en cache peut être utilisée dans les jambes de jointure/union.

* Un nom, donné `name` par l’argument ou par l’utilisation de l’opérateur [`as`](asoperator.md) sera utilisé comme l’onglet de résultat dans [`Kusto.Explorer`](../tools/kusto-explorer.md) l’outil.

* Évitez `fork` d’utiliser avec une seule sous-query.

* Préférez utiliser [le lot](batches.md) avec [`materialize`](materializefunction.md) `fork` des déclarations d’expression tabulaires à l’opérateur.

**Exemples**

```kusto
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 )
    ( project Timestamp, EventText | top 1000 by Timestamp desc)
    ( summarize min(Timestamp), max(Timestamp) by ActivityID )
 
// In the following examples the result tables will be named: Errors, EventsTexts and TimeRangePerActivityID
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 | as Errors )
    ( project Timestamp, EventText | top 1000 by Timestamp desc | as EventsTexts )
    ( summarize min(Timestamp), max(Timestamp) by ActivityID | as TimeRangePerActivityID )
    
 KustoLogs
| where Timestamp > ago(1h)
| fork
    Errors = ( where Level == "Error" | project EventText | limit 100 )
    EventsTexts = ( project Timestamp, EventText | top 1000 by Timestamp desc )
    TimeRangePerActivityID = ( summarize min(Timestamp), max(Timestamp) by ActivityID )
```
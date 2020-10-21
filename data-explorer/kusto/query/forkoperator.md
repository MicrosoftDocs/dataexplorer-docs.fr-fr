---
title: opérateur de fourche-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Fork dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dfa4d2218c3f54a9c85644fb0ee1edf4b7c012dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247412"
---
# <a name="fork-operator"></a>fork, opérateur

Exécute plusieurs opérateurs de consommateur en parallèle.

## <a name="syntax"></a>Syntaxe

*T* `|` `fork` [*nom* `=` ] `(` *sous* -requête `)` [*Name* `=` ] `(` *sous-requête* `)` ...

## <a name="arguments"></a>Arguments

* la *sous-requête* est un pipeline en aval d’opérateurs de requête
* le *nom* est un nom temporaire pour la table de résultats de la sous-requête

## <a name="returns"></a>Retours

Plusieurs tables de résultats, une pour chacune des sous-requêtes.

**Opérateurs pris en charge**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**Notes**

* [`materialize`](materializefunction.md) la fonction peut être utilisée comme remplacement pour l’utilisation de [`join`](joinoperator.md) ou [`union`](unionoperator.md) sur des jambes de fourche.
Le flux d’entrée sera mis en cache par matérialisation, puis l’expression mise en cache peut être utilisée dans les jambes de jointure/Union.

* Un nom, fourni par l' `name` argument ou à l’aide de l’opérateur, est [`as`](asoperator.md) utilisé comme pour nommer l’onglet résultat dans l' [`Kusto.Explorer`](../tools/kusto-explorer.md) outil.

* Évitez `fork` d’utiliser avec une seule sous-requête.

* Préférer l’utilisation de [batch](batches.md) à [`materialize`](materializefunction.md) des instructions d’expression tabulaires à l' `fork` opérateur.

## <a name="examples"></a>Exemples

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
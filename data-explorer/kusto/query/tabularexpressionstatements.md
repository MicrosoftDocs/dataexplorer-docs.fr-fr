---
title: Instructions d’expression tabulaires-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les instructions d’expression tabulaire dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 943281c2e06aecfffab72ca0f98597c19938d936
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250756"
---
# <a name="tabular-expression-statements"></a>Instructions d’expression tabulaire

L’instruction d’expression tabulaire est ce que les gens ont généralement à l’esprit lorsqu’ils abordent les requêtes. Cette instruction apparaît généralement en dernier dans la liste des instructions, et les deux entrées et sa sortie se composent de tables ou de jeux de données tabulaires.

Kusto utilise un modèle de workflow pour l’instruction d’expression tabulaire. La structure type d’une instruction d’expression tabulaire est une composition de *sources de données tabulaires* (telles que des tables Kusto), des *opérateurs de données tabulaires* (tels que des filtres et des projections) et des *opérateurs de rendu*potentiellement. La composition est représentée par le caractère de barre verticale ( `|` ), ce qui donne à l’instruction une forme très régulière qui représente visuellement le déroulement des données tabulaires de gauche à droite.
Chaque opérateur accepte un jeu de données tabulaires « à partir de la barre verticale » et d'autres entrées (notamment d'autres jeux de données tabulaires) à partir du corps de l'opérateur, puis transmet un jeu de données tabulaires à l'opérateur qui suit :    

*Source1* `|` *operator1* `|` *operator2* `|` *renderInstruction*

Dans l’exemple suivant, la source est `Logs` (une référence à une table dans la base de données active), le premier opérateur est `where` (qui filtre les enregistrements de son entrée conformément à un prédicat par enregistrement) et le deuxième opérateur est `count` (qui compte le nombre d’enregistrements dans son jeu de données d’entrée) :

```kusto
Logs | where Timestamp > ago(1d) | count
```

Dans l’exemple suivant, plus complexe, l' `join` opérateur est utilisé pour combiner des enregistrements de deux jeux de données d’entrée : un qui est un filtre sur la `Logs` table et un autre qui est un filtre sur la `Events` table.

```kusto
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
```

## <a name="tabular-data-sources"></a>Sources de données tabulaires

Une **source de données tabulaire** produit des jeux d’enregistrements, à traiter plus en détail par les **opérateurs de données tabulaires**. Kusto prend en charge plusieurs sources :

* Références de table (qui font référence à une table Kusto, dans la base de données de contexte ou dans un autre cluster/base de données).
* Opérateur de [plage](rangeoperator.md)tabulaire.
* [Opérateur d’impression](printoperator.md).
* Appel d’une fonction qui retourne une table.
* [Littéral de table](datatableoperator.md) (« DataTable »).